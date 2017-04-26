---
title: ClassificationNode
# keywords:
last_updated: 2017-03-27
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-classification-node.html
toc: false
# folder: doc
---

The ClassificationNode is a building block of the
[Model][doc-config-design-model]. A Model has a single root ClassificationNode.

ClassificationNode's are similar to directories, but have no direct
relationship to directories in a file system. In particular they have no
relation to directories within Module's. They are at a higher level than
Module's. They allow organizing the many Module's of an organization
hierarchically. ClassificationNode's can contain other ClaasificationNode's
and Module's.

Like Module's, a ClassificationNode has a name. By convention the name of a
ClassificationNode is written in PascalCase notation. 

A ClassificationNode provides a namespace for Node's it defines. Node names
must be unique within a given ClassificationNode only. They need not be unique
globally or across ClassificationNode's, unless of course they map to concrete
concepts where name uniqueness is enforced in another scope, such as multiple
Git repositories under a given base URL.

Compared to Module's, ClassificationNode's are more abstract. The relation
between the name of a ClassificationNode and a concrete concept is not always
direct. 

Reasons for organizing Module's hierarchically using ClassificationNode's
include:

- Facilitate browsing and locating Module's
- Provide a uniform naming hierarchhy by which Module's can be identified by
  Dragom and users
- Inheritance
- Automatic configuration of bidirectional mapping between Modules and Artifact's

Some of these are explicitly described below.

Inheritance
-----------

Although each Module in the hierarchy of ClassificatuonNode's can be fully
configured individually, this hierarchy is core to an important reuse and
simplifying feature of Dragom, inheritance.

Model properties and plugins defined on a ClassificationNode are inherited by
children ClassificationNode's and Module's. Correctly structuring the hierarchy
of ClassificationNode's can enable a high degree of reuse by taking into
consideration the similarities between Module's.

For example, if the source code of many Module's is kept in Git repositories
under the same root URL and use a similar versioning strategy, they probably
deserve being defined under the same ClassificationNode on which the Git SCM
Model plugin is defined, as well as common Model properties.

Note however that it is not required that Module's sharing similarities (such
as having the same base Git URL) be defined under the same ClassificationNode.
Many considerations need to be taken into account by configurators when
defining the Module hierarchy, inheritance being one of them. Others can even
be external to Dragom if the Model is exploited for purposes outside of Dragom
such as for defining access rules.

Module-Artifact mapping configuration
-------------------------------------

See [Module-Artifact mapping][doc-config-design-module-artifact-mapping].

Module-Artifact mapping can be defined explicitly for each Module, this
mapping can be inferred from the ClassificationNode hierarchy with much less
configuration.

Consider the following simplified hierarchy:

```
Domain
  SubDomain
    app-a
```

With very little configuration, Dragom can infer that builds of Module
`Domain/SubDomain/app-a` produce Artifact's having the following
coordinates:

GroupId   |`com.acme.domain.sub-domain`
ArtifactId|`app-a`

Therefore ease of Module-Artifact mapping is an important consideration when
configuring the ClassificationNode hierarchy. Generally, basing the hierarchy
on business domains is the good thing to do since Artifact coordinates
generally also relate to business domains.

{% include links.html %}

[//]: # (TODO: links, to node...)
