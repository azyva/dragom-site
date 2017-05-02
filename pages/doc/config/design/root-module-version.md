---
title: Root ModuleVersion
# keywords:
last_updated: 2017-05-01
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-root-module-version.html
toc: false
# folder: doc
---

Most Dragom tools operate on ReferenceGraph's. Given a ModuleVersion, a
ReferenceGraph containing this ModuleVersion, all its referenced
ModuleVersion's and all the ModuleVersion's referenced by them and so on can be
built with no additional input. But specifying this initial ModuleVersion is
required.

Such an initial ModuleVersion is called a root ModuleVersion. Dragom tools
allow the user to specify one or more root ModuleVersion's from which the
ReferenceGraph to operate on can be built.

A root ModuleVersion generally contains the source code of a top-level artifact
such as a deployable application.

The fact that more than one root ModuleVersion's can be specified is to support
the common scenario where the development effort involves multiple interrelated
top-level Module's which are otherwise not linked together by build-time
references. These Module's may for example represent a group of applications
part of a larger system and sharing a common framework and which may need to be
updated simultaneously when a common referenced Module changes.

A root ModuleVersion is not necessarily an entry point to the ReferenceGraph
since it can happen to also be referenced by some other ModuleVersion part of
the ReferenceGraph of some other root ModuleVersion.

Root ModuleVersion's can be specified at the workspace level, therefore being
automatically effective for all tools operating within the workspace. They can
also be specified on the command line when invoking a tool.

{% include links.html %}
