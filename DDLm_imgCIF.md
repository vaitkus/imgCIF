Aligning DDLm imgCIF with DDL2 imgCIF
=====================================

DRAFT FOR DISCUSSION

Introduction
============

The DDL2 imgCIF dictionary is used to capture metadata and images from
single-crystal diffraction experiments. It was originally designed to
integrate with the mmCIF dictionary. With the advent of the more
flexible DDLm language it has become possible to use these datanames
to describe images collected in a wide variety of experiments, for
example powder and Laue diffraction. However, in order to do this in a
machine-actionable fashion, the precise way in which data names from
the imgCIF dictionary add to pre-existing categories in other
dictionaries must be defined.

Review: imgCIF DDL2 conventions
===============================

imgCIF adopts the following DDL2 conventions:

1. Use of 'implicit'. Where the value of a (key) data name is
deducible from context, it can be omitted. In particular,
if a data name value is given as a key-value pair, all child
data names may be assumed to take this single value and are
omitted from loops.

2. Use of 'implicit'. Where a key data name takes a single
value and that value is arbitrary, it may be omitted completely
from a data block and an arbitrary value generated as needed.

3. Use of non-machine-readable instructions for missing values. DDL2
text definitions sometimes indicate that missing key data names should
be set equal to the values of other data names (see e.g. the
definition for ``_diffrn_detector.id``.). As these instructions apply
only to singleton values of key data names, their significance is
limited.

imgCIF and mmCIF
================

## ``_diffrn.id``

The DIFFRN category uniquely identifies a set of diffraction data, and
is therefore the primary link between raw data and the rest of the
data set.  A single set of diffraction conditions is identified by
``_diffrn.id``, and a single mmCIF entry may have several sets of
data.  The link to mmCIF from imgCIF is accomplished by including
child keys of ``_diffrn.id`` to many imgCIF categories. Where data
consist of a single run at a single wavelength, these child keys may
be completely omitted from the data block using the conventions above.
Any imgCIF categories that begin with ``_diffrn`` include a child data
name of ``_diffrn.id``.

## mmCIF incompatibility

imgCIF extends certain mmCIF categories in a way that renders some
conforming imgCIF data files incompatible with the mmCIF schema, by
adding extra key data names to categories:

### ``diffrn_detector``

imgCIF adds the ``_diffrn_detector.id`` key data name to the
``diffrn_detector`` category, while stating that it should be set
equal to ``_diffrn_detector.diffrn_id`` if missing.  Any imgCIF file
that has more than one distinct value for ``_diffrn_detector.id`` is
not compatible with mmCIF.

### ``diffrn_measurement``

As for ``diffrn_detector``, imgCIF adds extra key data names to
``diffrn_measurement``.  For compatibility with mmCIF the extra key
data names are allowed to default to ``_diffrn.id``.  If either of the
new key data names are allowed to have more than one distinct value,
they will violate the mmCIF data model.

### ``diffrn_refln``

The key for this category is extended to include
``_diffrn_refln.frame_id``.  Thus each reflection can have the frame
in which it was measured recorded, with multiple appearances of the
same h,k,l possible when it appears on multiple frames.  Again, this
is an extension of the mmCIF schema.

imgCIF and DDLm
===============

DDLm dictionaries may be extended by creating an expansion dictionary
that (among other things) can append keys to categories. This change
in the schema is signalled by a new value of ``_audit.schema``.  The
above modifications by imgCIF to the mmCIF schema can be captured by defining a
``mmcif`` imgCIF dictionary (in DDLm) that matches precisely the mmCIF schema,
leaving the current imgCIF dictionary (DDLm version) as the ``default``
schema.

Going in the other direction, the core CIF dictionary does not loop
``diffrn``, and so a further dictionary is possible that does not use
``_diffrn.id`` and its children as key data names at all.  So there
are 3 schemas: no ``_diffrn.id``, ``_diffrn.id`` only, and full
imgCIF.

imgCIF and other DDLm dictionaries
==================================

For machine-actionability of a data file that includes imgCIF data
names, the precise relationship of those data names to data names from
the other dictionaries must be available. As for the case of mmCIF
above, the relationship can be specified solely via ``_diffrn.id`` and
child data names, with an implicit value if only a single value is
necessary.

Scenarios
=========

## Simple

### Core

A data collection expressed using data names from the core CIF
dictionary wants to also include the image data, collected at a
single wavelength.

1. The ``_audit_conform`` loop can simply list the core-CIF compatible
imgCIF and core_CIF dictionaries as the dictionaries against which to
check.  There are no ambiguities around expanded category keys.

### Powder

A single-detector powder diffraction experiment wants to record images.

pdCIF is constructed by importing core CIF. pdCIF expands the refln
category key, but does not loop the diffrn category, so the same
approach as the simple scenario above works. Further imgCIF definitions
may be useful to identify a processed image.

## Complex

### Joint X-ray and neutron powder diffraction

Both X-ray and neutron measurements are collected from a sample, and
the neutron measurements are modelled using a magnetic structure.

1. ``_diffrn.id`` now takes multiple values, and multiple detectors
are used.  The simple core-CIF compatible imgCIF is no longer suitable.
Any categories that implicitly assume a particular wavelength or
radiation would need to be expanded.  The full version of imgCIF
should be listed in the ``_audit_conform`` loop.

Program of work
===============

Given the above considerations, it is proposed that:

1. A ``mmCIF`` schema is defined that loops the categories that are
looped in mmCIF.

2. Versions of DDLm imgCIF are defined corresponding to the 3 schemes
outlined above ('core', 'mmCIF' and 'default').

3. Instructions for including raw data that can be described using
imgCIF data names are prepared.
