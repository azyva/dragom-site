---
title: ReferencePath
# keywords:
last_updated: 2017-04-25
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-reference-path.html
toc: false
# folder: doc
---

A ReferencePath is a path in a ReferenceGraph originating from a root
ModuleVersion (vertex) and following 0 or more references (directed edges) to
other ModuleVersion's.

A ReferencePath can contain a single ModuleVersion, in which case that
ModuleVersion is necessarily a root ModuleVersion.

The last ModuleVersion in a ReferencePath is called its leaf ModuleVersion. The
leaf ModuleVersion of a ReferencePath is not necessarily a leaf ModuleVersion
of the ReferenceGraph. In this case, the ReferencePath has children
ReferencePath's, which are those which have the same initial
ModuleVersion's.

Within a ReferenceGraph, many different ReferencePath's can have the same leaf
ModuleVersion. This is so since a ReferenceGraph is indeed a graph, not a tree,
where the same ModuleVersion can be reached by references coming from different
referring ModuleVersion's.

Since a ReferenceGraph is acyclic, a ReferencePath is also acyclic in that a
given ModuleVersion cannot occur twice in the same ReferencePath. This
constraint is entirely reasonable since such cycles would not be correctly
supported by other tools.

Also, ReferencePath's cannot contain more than one occurrence of the same
Module, regardless of its Version, even though other tools would generally
support such cycles when different Versions are involved. For example,
Version v1 of Module A could refer to B which could itself refer to Version v2
of Module A, where v2 would have been published before v1. This constraint is
deemed reasonable given that such cycles are generally a sign of bad design and should be eliminated.

Dragom focuses on inter-Module references as opposed to intra-Module
references. References which may exist within a given Module, such as a typical
parent reference from a Maven module to its main container module, are
considered implementation details. This implies that intra-Module references
are not represented in ReferencePath's. If Module A has an internal reference
from Maven module A1 to Maven module A2, and if Maven module A2 has an external
reference to another Module B, the ReferencePath from A to B exists and no
ReferencePath reveals the internal reference from A1 to A2.

ReferencePath cannot be explicitly specified by the user. They come to life
within Dragom, generally while traversing a ReferenceGraph. However, users can
specify ReferencePathMatcher's to select the ReferencePath's on which a tool
operates. More precisely, users can specify element ReferencePathMather's which
can in some cases look like ReferencePath's since each element can match a
ModuleVersion within a ReferencePath.

When Dragom instantiates a ReferencePath, it includes information about the
references in addition to the ModuleVersion's themselves. For example consider
the case where the reference from Module A to Module B is actually within Maven
module `app` of A, and is an artifact reference to a given groupId, artifactId
and ArtifactVersion produced by some Maven module of Module B. The artifact
reference is included in the ReferencePath and can be matched by a
ReferencePathMatcher. Also, when the ReferencePath A -> B is shown to the user,
that extra information is also conveniently provided. 

{% include links.html %}

[//]: # (have a diagram to illustrate the various facts)
