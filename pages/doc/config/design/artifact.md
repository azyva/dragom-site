---
title: Artifact 
# keywords:
last_updated: 2017-04-07
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-artifact.html
toc: false
# folder: doc
---

An Artifact refers to a file which, after having performed a build on a
Module's source code, needs to be kept as part of the result of the build.

Said in another way, building a Module is processing its source files and
producing Artifact's as a result.

It is often understood that building a Module involves using a compiler to
transform code written in some high-level language into binary file Artifacts
representing an executable version of that code expressed in a lower-level
language. But in the general sense, building a Module need not involve
compiling source files. It can very well be limited to packaging source files
into archive Artifact's, or simply identifying source files as Artifact's. 

The process of building a Module can generate intermediate files which are not
Artifacts in the sense that they are not kept as part of the result of the
build. Artifacts must be specifically identified by the build process.

Artifact-based development
--------------------------

Although the concept of Artifact's is present whenever we talk about builds, it
is specifically prevalent in Artifact-based development in which build dependencies
of a Module are expressed in terms of Artifact's.

Artifact's produced by the build of a Module therefore become the externally
consumable files a Module exposes to be reused by builds of other Module's, or
to be included in deployment or installation packages.

Artifact coordinates
--------------------

In Artifact-based development, Artifact's are identified by their coordinates.
Since many Artifact-based build tools use the ubiquitous Maven Artifact
coordinate system, so does Dragom.

In the Maven Artifact coordinate system, each Artifact is identified with the
following coordinate axis:

Coordinate axis|Description
---------------|-----------
groupId        |Namespace within which an Artifact lives. This generally represents an organization or system within an organization. GroupIds are unique globally. Exemple: `org.azyva.dragom`
artifactId     |Base name of the Artifact, unique within a groupId. Example: `dragom-core`
version        |Version of the Artifact. Example: `1.2.3`
type           |File type of the Artifact. Exemple: `jar` 
classifier     |Optional. Classifier of the Artifact, to allow having multiple different Artifacts for the same previous axis. Example: `dist`

In general, and especially within Dragom, it is important to group these
coordinate axis in the following way:

Group              |Description
-------------------|-----------
groupId, artifactId|Together these axis uniquely identify a logical grouping of Artifacts. 
version            |This is the version of the grouping
type, classifier   |These axis distinguish the various Artifacts within a (version of a) grouping.

?Another heading here?
---

All the Artifacts under a given groupId, artifactId and version (often
abbreviated GAV) are generally produced at once by performing the build of some
module. The groupId and artifactId used is specific to this module (no other
module uses the same groupId/artifactId combination). And the version of the
Artifacts is specific to the version of the module being built.

Note that in the paragraph above, we talk about "module" and "version", not
"Module" and "Version". This subtlety stems from the fact despite the above,
Dragom does not impose that a given ModuleVersion produces Artifacts with the
same groupId and artifactId. It is legitimate for a given ModuleVersion to
produce Artifacts with different groupId and artifactId. However, this is not
true for the version. A given ModuleVersion must produce Artifacts with the
same version, which we call ArtifactVersion. If that was not the case,
traceability and mapping between Version and ArtifactVersion would be very
confusing for humans, too complex to support in Dragom and would not provide
significant benefits.

Maven supports the concepts of modules by which a single


 
Given that Artifacts are produced by performing the build of a module and that 


dragom uses ArtifactGroupId and ArtifactVersion.
mapping groupId to NodePath.
when we talk about an Artifact, we often mean the grouping
we talk about all the artifactids produced by a Module
Maven modules are seen as implementation details and must share the same version, which makes sense.

{% include links.html %}
