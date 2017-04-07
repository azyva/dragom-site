---
title: Version
# keywords:
last_updated: 2017-04-07
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-version.html
toc: false
# folder: doc
---

The concept of Version is of course not specific to Dragom. But within Dragom
it has a specific meaning.

A Version of a Module is a state of its source code. In SCM vocabulary,
Version's are branches and tags.

Dragom distinguishes between two types of Version's:

- Dynamic Version's: Equivalent to SCM branches
- Static Version's: Equivalent to SCM tags

The main reason for introducing the concept of Version, as opposed to simply
dealing with the familiar concepts of branches and tags, is to have a way to
uniformly identify any branch or tag in an unambiguous manner. Branches and
tags are both simple character strings which do not convey their types. Using a
simple character string such as "v-1.2.3" does not tell with certainty whether
we are talking about a branch or tag.

Note that Dragom assumes static Version's are immutable, which is the generally
accepted semantics of tags, even tbough SCM generally allow changing tags,
albeit with more effort than branches.

Version's are written as folows:

[`D`/`S`]`:`*branch-or-tag*

where the D or S prefix indicates whether the Version is dynamic or static.

For example:

- `D/master`: Branch master
- `S/v-1.2.3`: Tag v-1.2.3

Dragom also supports the concept of ArtifactVersion.

{% include links.html %}
