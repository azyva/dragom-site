---
title: Artifact 
# keywords:
last_updated: 2017-04-25
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-artifact.html
toc: true
# folder: doc
---

Introduction
------------

An Artifact refers to a file which, after having performed a build on a
Module's source code, needs to be kept as part of the result of the build.

Said in another way, building a Module is processing its source files and
producing Artifact's as a result.

It is often understood that building a Module involves using a compiler to
transform code written in some high-level language into binary file Artifact's
representing an executable version of that code expressed in a lower-level
language. But in the general sense, building a Module need not involve
compiling source files. It can very well be limited to packaging source files
into archive Artifact's, or simply identifying source files as Artifact's. 

The process of building a Module can generate intermediate files which are not
Artifact's in the sense that they are not kept as part of the result of the
build. Artifact's must be specifically identified by the build process.

Artifact-based development
--------------------------

Although the concept of Artifact's is present whenever we talk about builds, it
is specifically prevalent in Artifact-based development in which build
dependencies of a Module are expressed in terms of Artifact's.

Artifact's produced by the build of a Module therefore become the externally
consumable files a Module exposes to be reused by builds of other Module's, or
to be included in deployment or installation packages.

Artifact coordinates
--------------------

In Artifact-based development, Artifact's are identified by their coordinates.
Since many Artifact-based build tools use the ubiquitous Maven Artifact
coordinate system, so does Dragom.

In the Maven Artifact coordinate system, each Artifact is identified with the
following coordinate axes:

Coordinate axis|Description
---------------|-----------
groupId        |Namespace within which an Artifact lives. This generally represents an organization or system within an organization. GroupIds are unique globally. Example: `org.azyva.dragom`.
artifactId     |Base name of the Artifact, unique within a groupId. Example: `dragom-core`..
version        |Version of the Artifact. Example: `1.2.3`.
type           |File type of the Artifact. Exemple: `jar`.
classifier     |Optional. Classifier of the Artifact, to allow having multiple different Artifact's for the same previous axes. Example: `dist`.

In general, and especially within Dragom, it is important to group these
coordinate axes in the following way:

Group              |Description
-------------------|-----------
groupId, artifactId|Together these axes uniquely identify a logical grouping of Artifact's. This is called the ArtifactGroupId.
version            |This is the version of the grouping. This is called the ArtifactVersion.
type, classifier   |These axes distinguish the various Artifact's within a (version of a) grouping.

Alternate meaning of Artifact
-----------------------------

Often when speak about an Artifact, we actually mean all or any of the
Artifacts sharing the same ArtifactGroupId. For example a sentence such as
"this module depends on artifact `com.acme.domain:app-a`" is often
encountered, which actually means "this module depends on some artifact having
groupId `com.acme.domain`, artifactId `app-a`.

What is meant by Artifact is generally obvious from the context.

Artifact's and Module's
-----------------------

All the Artifact's under a given groupId, artifactId and version (often
abbreviated GAV) are generally produced at once by performing the build of some
module. The groupId and artifactId used is specific to this module (no other
module uses the same groupId/artifactId combination). And the version of the
Artifact's is specific to the version of the module being built.

Note that in the paragraph above, we talk about "module" and "version", not
"Module" and "Version". This subtlety stems from the fact that despite the
above, Dragom does not impose that a given ModuleVersion produces Artifact's
with the same groupId and artifactId. It is legitimate for a given
ModuleVersion to produce Artifact's with different groupId and artifactId.
However, this is not true for the version. A given ModuleVersion must produce
Artifact's with the same version, which we call ArtifactVersion. If that was
not the case, traceability and mapping between Version and ArtifactVersion
would be very confusing for humans, too complex to support in Dragom and would
not provide significant benefits.

Note that while a given ModuleVersion *can* produce Artifact's with different
groupId and artifactId, Dragom configuration is generally simpler when all the
Artifact's produced by a given Module have the same groupId and share a common
artifactId prefix. This allows
[Module-Artifact mapping][doc-config-design-module-artifact-mapping] to be
based on educated guesses when looking for a Module which produces a given
Artifact instead of explicit configuration.

Following these simplifying constraints is generally a reasonable thing to do
since although the source code domain is distinct from the Artifact domain,
coherent Module design generally implies some logical equivalence between the
Module and its Artifact's.

Maven modules
-------------

Maven supports the concepts of modules by which a single Dragom Module
can be composed of multiple Maven modules which are considered implementation
details of the Module by Dragom.

Every Maven module can produce Artifact's and the set of Artifact's produced by
a given Dragom Module is the union of all the Artifact's produced by the Maven
modules of which the Dragom Module is composed.

Maven does not impose any particular constraints on the Artifact's produced by the different modules of a build. But when integrated within Dragom, the
Artifact's produced by a Module, whether or not it is composed of Maven
modules, must still at least respect the constraint that all such Artifacts
share the same ArtifactVersion.

Also, as mentioned above, the Artifact's produced by the various Maven modules
of a Dragom Module should have the same groupId and should share a common
artifactId prefix.

Finally, another more subtle constraint is that dependencies between the Maven modules of a given Module (e.g.: the typical parent reference from a Maven module to its main container module) must be within the same ModuleVersion. Said in another way, these internal dependencies must be to the same
ArtifactVersion as that of all the Artifact's produced by the ModuleVersion.

{% include links.html %}
