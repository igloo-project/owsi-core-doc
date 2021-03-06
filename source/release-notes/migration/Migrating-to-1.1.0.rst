==================
Migrating to 1.1.0
==================

.. warning: 1.1.x versions are considered stable releases.

1.1.0 is a big update of owsi-core 0.x versions. It includes a major package
rename, a lot of updates :

* move to Java 8, Tomcat 8.5 stack
* introduce Java 8 lambdas, streams; reworked predicates, ...
* bindgen optimizations (at compilation time) with lambda
* hibernate update (5.2 -> 5.3)
* bootstrap 4 (wicket & bootstrap 3 code kept for compatibility)
* fontawesome 5 (fontawesome 4 kept for compatibility)
* wicket 8.x
* removed deprecated early target definition in linkdescriptor API
* deprecated GenericListItem removed and replaced by ReferenceData
* migration to select2 from wicketstuff
* maven-release-plugin is replaced by jgitflow plugin
* and others...

Detailed explanations and migration plan can be found from this page.

This is the first official release of igloo-project, forked from OWSI-Core
project.

.. contents:: :local:


Detailed modifications
######################

.. toctree::
  :maxdepth: 2

  1.1/bindgen.rst
  1.1/functional.rst
  1.1/configuration.rst
  1.1/property.rst
  1.1/hibernate.rst
  1.1/bootstrap.rst
  1.1/fontawesome.rst
  1.1/wicket.rst
  1.1/linkdescriptor.rst
  1.1/referencedata.rst
  1.1/select2.rst
  1.1/cleaning.rst
  1.1/misc.rst


Others
######

Renamed configuration
---------------------

``hibernate.defaultSchema`` is renamed ``db.schema`` as it is used by flyway.
You need to rename it in your ``configuration.properties``.

Updated
-------

Infinispan
~~~~~~~~~~

With jgroups 4.0, Infinispan don't use any longer oob and internal threads.
You have to remove all ``internal_thread_pool.*``, ``oob_thread_pool.*`` and
``thread_pool.queue_enabled`` settings in your jgroups configuration
(``*jgroup*.xml`` files).

See http://planet.jboss.org/post/removing_thread_pools_in_jgroups_4_0

All references to ``org.jgroups.Address`` must be replaced with
``org.iglooproject.infinispan.model.AddressWrapper`` (as Address is no longer
``Serializable``, AddressWrapper handles Serialization).


Mockito
~~~~~~~

Mockito is upgraded to 2.x version. You way need to rewrite some tests in your
projects.

We recommend to exclude mockito 2.x dependency as a first step and check your
test results without any rewrite, then to update mockito once all your tests are
fixed.


No longer supported
-------------------

JDK 7
~~~~~

JDK 7 support is removed as planned.


Tomcat 7
~~~~~~~~

Tomcat 7 is no longer supported. Servlet 3.1 is targetted, and so Tomcat 8.5
is needed.


JFreeChart
~~~~~~~~~~

Dependency management entry removed from parent pom. If your project use
JFreeChart, declare dependency inside your project's pom.


Maven
~~~~~

* unused property ``igloo.gson.version`` is removed


Password encoding
~~~~~~~~~~~~~~~~~

.. note:: **CoreLowerCaseShaPasswordEncoder**, **Md5PasswordEncoder**, **CoreShaPasswordEncoder** removed
   (Spring Security 5 update related)

To know if your application is compatible with new password encoding, please check stored passwords.
If your encoded passwords all start with ``$2a$`` (bcrypt marker), your application may be compatible.

If this is the case, **you need to update your hashed password** by prefixing it ``{bcrypt}`` with prefix
as the new ``PasswordEncoder`` is a delegating one, that choose the correct PasswordEncoder based on
this prefix. This PasswordEncoder uses ``bcrypt`` to hash new passwords.

If not you need to write your own password encoder based on code from previous versions. Please take care
of case insensitive check if **CoreLowerCaseShaPasswordEncoder** was used.

This page https://en.wikipedia.org/wiki/Bcrypt, your application configuration, and hashed password patterns
may allow you to identify password encoder behavior and identify needed use-cases.

If you upgrade your application, you should take into consideration to handle
all new passwords with modern hashing (use encoded password prefix to switch encoder behavior).

You should also consider `this paragraph from Spring documentation <https://docs.spring.io/spring-security/site/docs/5.0.1.RELEASE/reference/htmlsingle/#pe-history>`_

Property ``security.passwordSalt`` and method ``DefaultJpaSecurityConfig.getPasswordSalt()`` are removed.


YUI Compressor
~~~~~~~~~~~~~~

YUI Compressor (maven plugin, minification at build time) is removed as it
was no longer used to provide minification (handled internally at runtime by
wicket).

I you use YUI Compressor, you need to include your maven plugin configuration
inside your project.


maven-release-plugin
~~~~~~~~~~~~~~~~~~~~

Removed. Use jgitflow or reconfigure release plugin in your project.


tomcat-jdbc
~~~~~~~~~~~

We use HikariCP as database pool provider. tomcat-jdbc is no longer used.
Switch to HikariCP.

As tomcat-jdbc is a provided dependency (included in tomcat), this may not
affect your web-application. It may affect your tests or main scripts: if this
is the case, you need to ensure that tomcat-jdbc dependency connfiguration
is correct.


Joda-Time
~~~~~~~~~

Joda-Time is removed from dependency; you can continue to use it by re-adding
this dependency to your project.


Session - redirectUrl
~~~~~~~~~~~~~~~~~~~~~

Igloo mechanisms to handle post-login redirectUrl are completely removed.
You should use easily spring-security based one.

Removed methods are, on ``AbstractCoreSession``:

* signOutWithoutCleaningUpRedirectUrl
* registerRedirectUrl
* getRedirectUrl
* consumeRedirectUrl
* registerRedirectPageLinkDescriptor
* getRedirectPageLinkDescriptor

If you use these methods, you should check how you handle your login success.
If you use LoginSuccessPage (wicket-more), then Spring-Security redirect should
work.

Here are the use-cases to check that there are no regressions on your application:

* login to default home page; logout
* visit a protected page; you should be redirect to it after login; logout
* login with a wrong password; check error message
* visit a protected and forbidden page; you should be redirected to default
  home page with an error message


Javascript
~~~~~~~~~~

The following dependencies are split from igloo-component-wicket-more and
marked as optional dependencies (you need to add them manually in your projects
to use them):

* jquery.json
* jquery.carouFredSel
* jquery.fancybox


New features
------------

Test tooling
~~~~~~~~~~~~

A new **igloo-dependency-test** provides basic dependencies for tests. You can
use this dependency in place of junit, mockito, spring-test, ... dependencies.

**org.iglooproject.jpa.junit.AbstractTestCase** and **org.iglooproject.jpa.EntityManagerExecutionListener**
are moved in a new **igloo-dependency-jpa-test** module. If you want to use
them, add this new dependency with scope test, and fix your imports.

Bootstrap 4
~~~~~~~~~~~

Bootstrap 4 is available and used by basic-application archetype.

Codebase for bootstrap 3 is still available and unchanged.

JNDI Datasource
~~~~~~~~~~~~~~~

Configuration of JNDI can be done with a configuration switch. Please see
:ref:`jndi`


Migration script
----------------

The following script intends to help initiating migration by dealing with dump
replacements.

.. warning ::
  As is, the script move **from version 0.14 to 1.1**. Please replace
  version numbers in the first block to adjust to your version.

.. literalinclude:: 1.1/scripts/migration-igloo.sh
  :language: bash
