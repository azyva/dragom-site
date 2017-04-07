---
title: Version-ArtifactVersion mapping
# keywords:
last_updated: 2017-04-07
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-version-artifact-version-mapping.html
toc: false
# folder: doc
---

There is clearly a relation between Version's and ArtifactVeraion's. A given
ArtifactVersion is necessarily produced by a given Version of a Module.

One of Dragom's core functionality is its ability to map Version's to and from
ArtifactVersion's. This is required for Artifact-based development since Dragom
operates mostly at the source code level where Version's are used whereas
references are expressed at the Artifact level using Artifact coordinates in
which ArtifactVersion's prevail.

Tools which support Artifact-based development generally do not assume a
relationship between Version's (branches and tags) at the source code level and
ArtifactVersion's at the Artifact level. For example:

- Build tools such as Maven build a Module whose source code has been
  previously extracted from the SCM into a directory without knowing which
  Version, if any, the source code comes from.
- Resolving Artifact references during the build process involves only
  Artifact's and thus only ArtifactVersion's. The SCM is not accessed and
  therefore ModuleVersion's are not involved.
- Finally when releasing a Module where Artifact's are deployed to some
  Artifact repository under a static ArtifactVersion, a static Version is
  generally created at the SCM level so that the Artifact's produced are backed
  by known source code, and diagnostics, fixes or new development can be done
  based on that source. But that Version is defined by the user or by some
  automated script under the control of the user. It is not the build tool
  which defines and manages this Version in relation to the ArtifactVersion
  being released.

Version-ArtifactVersion mapping is therefore specific to Dragom.

Avoiding mapping collisions
---------------------------

In Version-ArtifactVersion mapping it is generally important to ensure that
each Version corresponds to a distinct ArtifactVersion.

For static Version mapping to static ArtifactVersion (releases) this is
obvious. It is important that the Version of a Module which produces a given
ArtifactVersion be unambiguously defined. 

For dynamic Version mapping to dynamic ArtifactVersion (development projects),
this is especially important if:

- Multiple independent projects isolated on different Version's can be ongoing
  simultaneously, and
- A central Artifact repository manager is used

Otherwise, ArtifactVersion collision can occur, which generally leads to
unintended cross-project interactions (a projects uses an inappropriate
Artifact from another project).

Mapping configuration
---------------------

Dragom Version-ArtifactVersion mapping is flexible. For example, it is possible
to configure Dragom so that for a given Module and for a given tool invocation,
the ArtifactVersion `1.2.3-SNAPSHOT` maps to the Version `D/develop` and
vice-versa, even though they generally bear no relation to each other, except
for that specific Module at that specific point in time. It is very likely that
the Version `D/develop` will eventually map to another ArtifactVersion such as
`1.2.4-SNAPSHOT` 

However, such Module-specific and context-specific mapping implies more
configuration and/or requires defining more parameters at tool-invocation time.
Dragom configuration and of Dragom tools which need to perform
Version-ArtifactVersion mapping can be greatly simplified if
such mappings can be defined globally for all Module's or at least for many
Module's under a given ClassificationNode, and if there is no need to force a
mappings at tool-invocation time.

For example the following conventions can be followed:

- Version's `D/`*version* map to ArtifactVersion's *version*`-SNAPSHOT` (e.g.:
  `D/develop-my-project` maps to `develop-my-project-SNAPSHOT`)
- Version's `S/v-`*version* map to ArtifactVersion's *version* (e.g.:
  `S/v-1.2.3` maps to `1.2.3`)

Such global mappings can be easily expressed generically in Dragom. When such
simple mappings are possible, it is recommended to use them.

Imposing such strict mappings may seem unnatural and overly restrictive,
especially given that tools which have existed since long before Dragom do not
impose such constraints. But adopting a development workflow based on such
generic mappings remains a sensible thing to do and should be considered. Here
are other motivating factors:

- It makes the mappings obvious for humans. One who sees an Artifact with a
  given ArtifactVersion can readily know which Version of the Module at the
  source code level produces it. This is especially useful when easy
  Module-Artifact mapping is also enforced.
- It ensures that to each Version corresponds a distinct ArtifactVersion,
  therefore avoiding ArtifactVersion collision when using a central Artifact
  repository manager (distinct ArtifactVersion's used by different projects).

{% include links.html %}
