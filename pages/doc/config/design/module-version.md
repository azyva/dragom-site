---
title: ModuleVersion
# keywords:
last_updated: 2017-04-07
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-module-version.html
toc: false
# folder: doc
---

A ModuleVersion is an identifier for a specific source code Version of a
Module. It is a tuple which includes the NodePath of a Module and a Version.

ModuleVersion's are written as follows:

*NodePath*`:`*Version*

For example:

`Domain/SubDomain/app-a:D/master`

ModuleVersion's are often encountered when using Dragom. In particular, the
reference from a Module to another is always to a specific Version of the
Module and a ModuleVersion is used to represent such a reference.

Of course, in Arifact-based development, references are expressed in terms of
Artifacts, not Module's. The Module-Artifact mapping mechanism of Dragom
therefore implies converting Artifact coordinates to ModuleVersion's.

{% include links.html %}
