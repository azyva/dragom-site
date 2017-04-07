---
title: NodePath
# keywords:
last_updated: 2017-04-07
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-node-path.html
toc: false
# folder: doc
---

The Model is a collection of Node's (ClassificationNode's and Module's)
organized hierarchically. Individual Node's need to be identifiable. Each Node
has a name but such names are only unique within a ClassificatiionNode, not
globally.

A Node is therefore identified by a NodePath. Since the hierarchical
organization of Node's is similar to the organization of files and directories
in a file system, a NodePath is very similar to a file path.

For example, consider the following simplified Model:

```
Domain
  SubDomain
    app-a
  AnotherSubDomain
    app-b
```

where Domain is a ClassificationNode containing SubDomain and AnotherSubDomain,
also ClassificationNode's, which in turn contain the app-a and app-b Module's.
The following NodePath's can be used to identify the various Node's:

- `Domain/`
- `Domain/SubDomain1/`
- `Domain/SubDomain1/app-a`
- `Domain/SubDomain2/`
- `Domain/SubDomain2/app-b`

Here are the rules and facts about NodePath's:

- The NodePath of a Node is the sequence of Node names following the hierarchy
  of Node's leading to the identified Node
- Node names in a NodePath are separated by "/" with no intervening additional
  characters
- The NodePath of a ClassificationNode ends with "/"
- The NodePath of a Module does not end with "/"
- NodePath's do not start with"/"
- The root ClassificationNode is identified by the empty NodePath
- Dragom does not support relative NodePath's

NodePath's are central to Dragom. They are the main identification mechanism
for Node's. They are used extensively in logs and other interactions with the
user when Module's need to be identified.

In Artifact-based development, Artifact coordinates are also an important
identifying mechanism for Artifacts. Given the close relationship between
Module's and Artifact's produced by builds of such Module's, users often
identify Module's using the corresponding Artifact coordinates. Dragom does not
do that and rather uses NodePath's, unless the concept of Artifact is clearly
in context. This can be confusing for users since NodePath is a Dragom-specific
concept.

But because of the Module-Artifact mapping which Dragom must perform, and
because the mapping configuration is greatly simplified when the Model
hierarchy is similar to the Artifact coordinate structure, it is generally the
case that Module NodePath are easily recognized by users.

{% include links.html %}
