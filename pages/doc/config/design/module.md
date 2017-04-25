---
title: Module
# keywords:
last_updated: 2017-03-27
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-module.html
toc: false
# folder: doc
---

The Module is a building block of the [Model][doc-config-design-model].

A Module represents a collection of source files and directories that is an
atomic development unit. More precisely a Module:

* Has a name
* The unit of work
* The checkout unit from the SCM
* The commit unit into the SCM
* The versionning unit (branches and tags)
* The build unit
* Independant of other Module's

Certainly Modules are composed of individual files and directories and many
tools do not explicitly recognize Module's. For example:

* Subversion makes no distinction between the semantics of various directories
  within a repository and allows checking out directories at any level
* Maven sees any directory in a hierarchy having a `pom.xml` file as being
  buildable

Despite the flexibility offered by such tools, the use of Dragom requires
respecting the constraints imposed, which, regardless of Dragom, are generally
considered best practices.

More details about these constraints are provided below. Git, Subversion and
Maven are used as concrete tool examples, but the intent here is not to provide
complete documentation about the use of these tools within Dragom.

Module name
-----------

A Module has a name. By convention, the name of a Module is written in
lower-case-with-dashes notation.

As mentionned below, a Module generally corresponds to a Git repository or a
directory within a Subversion repository. The name of the Module therefore
generally corresponds to the name of the Git repository or of the Subversion
directory. Although this is not strictly required, it greatly simplifies
configuration and avoid user confusion. 

Work unit
---------

Aside from possible internal optimizations, Dragom works at the Module level
and always considers whole Module's when needing access to sources.

Of course Module's are organized as a hierarchy of files and directories and
work on a Module can involve only a subset of this bierarchy, such as a single
file or a group of closely related files under a given directory. The user
remains free to manage the files and directories within a Module as he sees
fit. But from Dragom's point of view the whole Module remains the work unit.

Checkout and commit unit
------------------------

Since Dragom works at the Module level, it follows that Dragom performs SCM
operations at the Module level when it needs to access the Module source code.

For Git this implies that there is a one-to-one correspondance between Module's
and SCM repositories since SCM operations are global to the repository.

For Subversion, this implies that the great flexibility of checking out or
commiting any directory in the repository cannot be exploited. Instead some
discipline must be followed to interact with the repository in a controlled and
structured manner which does not leave room for improvisation.

However, contrary to Git, SCM operations within a Subversion repository can be
specific to a directory. It follows that Module's can be represented by
directories in the repository, which can therefore contain multiple distinct
Module's. The Module's can be organized in a hierarchy. Of course a Module
cannot be represented by a directory within another Module.

Versioning unit
---------------

Dragom requires that branches and tags, together called Version's, be global to
the Module. One Version cannot include or span multiple Module's nor can it
target a specific directory within a Module.

For Git this is natural since a Module's need to correspond to a repository and
branches and tags are global to the repository.

For Subversion, this implies that directories representing branches and tags be
based on other branch and tag directories of the Module, or on the conventional
`trunk` directory. Generally a directory corresponding to a Module contains:

* The `trunk` subdirectory containing the main developpement line
* The `branches` subdirectory containing the branches
* The `tags` subdirectory containg the tags

Some users organize their Subversion repository with the `trunk`, `branches`
and `tags` directories at the root. This layout, although legitimate in some
cases, is not compatible with Dragom, unless of course the whole repository
represents a single Module.

Altbough Subversion allows making copies of any directory anywhere in the
hierarchy, Dragom imposes these constraints on versioning operations.

The fact that the Module is the versioning unit also often has implications for
build scripts. Maven, an Artifact-based build tool, supports modules and
requires that each module defines its
[ArtifactVersion][doc-config-design-artifact-version]. With Dragom, every Maven
module of a [ModuleVeresion][doc-config-design-module-version] needs to have
the same ArtifactVersion. Also, references within a ModuleVersion must be to
the ArtifactVersion of the Module. 

Build unit
----------

When Dragom needs or is asked to build some source code it always builds whole
Module's. This is significant in the context of build tools which support the
concept of modules (not in the Dragom sense here).

Maven supports modules. It allows building individual modules in the Module
hierarchy, or the whole Module with a ripple effects on modules. Users can be
tempted to consider these modules as work units. In fact they can when working
on the Module outside of Dradom. But Dragom remains unaware of these modules.
They are implementation details of the Module as known by Dragom.

Independence of Module's
------------------------

It should be obvious that Dragom considers Module as independant of each other.
When Dragom acts upon a Module it must not have automatic or hidden effects on
another Module.

Git support submodules and Subversion the similar concept of externals. These
could allow linking a directory in a Module hierarchy to some (other Version
of an) other Module. Dragom does not support these concepts. When using Dragom
relations between Module's must be represented by soft references
expressed in the source code of the Module's (generally in build scripts).

{% include links.html %}
