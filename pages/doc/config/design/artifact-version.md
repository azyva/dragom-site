---
title: ArtifactVersion
# keywords:
last_updated: 2017-04-07
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-artifact-version.html
toc: false
# folder: doc
---

The concept of ArtifactVersion is similar to that of Version, but for Artifact's
in Artifact-based development.

All the Artifact's produced by the build of a ModuleVersion have the same
ArtifactVersion. But the Version of the Module is generally not the same as the
ArtifactVersion.

The support for Artifact-based development in Dragom is highly inspired by
Maven. That is not to say that Dragom does not support other Artifact-based
build tools, but Maven being probably the most popular such tool, and the fact
that other Artifact-based build tools also reuse concepts introduced by Maven,
Dragom also reuses some of these concepts.

Specifically the concepts of SNAPSHOT and release versions are supported and
borrowed from Maven supported. They correspond to dynamic and static
ArtifactVersion's, the two types of ArtifacrVersion's supported by Dragom.

A dynamic (SNAPSHOT) ArtifactVersion is one which can change. Said in another
way, the file corresponding to a dynamic ArtifactVersion can change.

Such changes occur during development when new builds of a Module produce new
different Artifacts. A change in an Artifact corresponding to a dynamic
ArtifactVersion generally requires rebuilding other Module's which refer to
that ArtifactVersion.

A static (release) ArtifactVersion, on the other hand, is considered fixed once
produced. The file corresponding to a static ArtifactVersion remains, for all
practical purposes, associated with that ArtifactVersion forever.

Releasing a Module generally requires that all ArtifactVersion's used in
references are static, as well as the ArtifactVersion of the Module being released.

ArtifactVersion's are written as follows:

*version*[`-SNAPSHOT`]

where the -SNAPSHOT suffix means the ArtifactVersion is dynamic as opposed to
static when that suffix is not present.

For example:

- `master-SNAPSHOT`: Dynamic ArtifactVersion probably produced by builds of the
  master branch (Version `D/master`) of the Module
- `1.2.3`: Static ArtifactVersion probably produced by the release build of the
  `v-1.2.3` tag of the Module

Note that some build tools such as Maven support version ranges with which it
is possible to define a reference to the most recent version within a given
range, such as any version greater than or equal to 1.0 but smaller than 2.0.
Dragom does not support such version ranges, which is generally considered a
bad practice anyways, since Module builds become dependent on factors outside
the Module source code.

{% include links.html %}
