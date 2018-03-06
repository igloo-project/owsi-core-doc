Lucene - French analyzer
========================

Due to code refactor to eliminate duplicate code, some classes are renamed.

.. literalinclude:: scripts/lucene-replace.sh
  :language: bash

.. warning::
  ``org.iglooproject.spring.util.lucene.search.LuceneUtils.toFilterRangeQuery(...)``
  is modified to throw an exception when min and max are null (previously, it
  silently returned ``null``).

SLF4JLoggingListener
====================

Renamed from ``org.iglooproject.commons.util.logging.SLF4JLoggingListener``
to ``org.iglooproject.slf4j.jul.bridge.SLF4JLoggingListener``.

.. literalinclude:: scripts/slf4j-replace.sh
  :language: bash


Commons split
=============

igloo-component-commons is split in several package.

.. literalinclude:: scripts/commons-replace.sh
  :language: bash

Javascript libraries
====================

Here is the list of removed obsolete javascript libraries, code is available in git history :

* jstree

ICloneable<T>
=============

``Object.clone`` use is discouraged. This interface, barely used, is removed.
