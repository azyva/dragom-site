---
title: ReferenceGraph
# keywords:
last_updated: 2017-04-25
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-reference-graph.html
toc: true
# folder: doc
---

Introduction
------------
A ReferenceGraph is a directed acyclic graph where vertices are ModuleVersion's
and edges represent references between ModuleVersion's.

{% include note.html content="The name &quot;Dragom&quot; originates from an accronym meaning &quot;Directed Reference Acyclic Graph Of Modules&quot;, which stresses the fact that it is based on the management of such graphs." %}

We talk about a graph as opposed to a tree since a given ModuleVersion can be
referenced from more than one referring ModuleVersion's.

A ReferenceGraph is directed since the edges representing the references are
oriented from a ModuleVersion to another ModuleVersion referenced by the
former.

It is acyclic since a cycle in a ReferenceGraph would imply a cycle in the
dependencies between ModuleVersion's which is not a desirable situation in
software development and is generally not supported by other tools.

Most Dragom tools operate on and need to traverse ReferenceGraph's, either in
parent-first (top-down) or depth-first (bottom-up) order. For example the
*checkout* tool traverses in parent-first order a ReferenceGraph and checks out
the source code of the ModuleVersion's. The *release* tool traverses in
depth-first order a ReferenceGraph and performs a release on each dynamic
ModuleVersion.

Roots ModuleVersion
-------------------

Since a ReferenceGraph is directed, it has well-defined entry points. These
entry points are the root ModuleVersion's specified by the user and which are
used by tools to build the ReferenceGraph.

Note that although an entry point in a ReferenceGraph is necessarily such a
root ModuleVersion, not all root ModuleVersion's are entry points to the
ReferenceGraph since a root ModuleVersion may be reachable from another root
ModuleVersion.

Leafs
-----

Within a ReferenceGraph, ModuleVersion's which do not have any references are
called leaf ModuleVersion's.

Module's known to Dragom
------------------------

ReferenceGraph contain ModuleVersion's, which implies Module's known to Dragom.

In particular, dependencies on third party (generally open source) Artifacts,
or to Artifacts produced by the organization but not known to Dragom, are not
considered when building ReferenceGraph's.

ReferencePath
-------------

Within a ReferenceGraph, paths can be seen. Paths which start from a root
ModuleVersion and follow the references (directed edges) to 0 or more other
ModulesVersion's are called ReferencePath's.

{% include links.html %}
