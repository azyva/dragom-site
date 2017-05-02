---
title: References and dependencies
# keywords:
last_updated: 2017-05-01
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-references-dependencies.html
toc: false
# folder: doc
---

In Dragom, which deals with ReferenceGraph, the concept of references is
prevalent. A reference is a build time relation between two Module's, more
specifically ModuleVersion's.

Generally in the context of build tools, such as Maven, we generally talk about
dependencies, not references. These two concepts are for most practical
purposes the same. However in Dragom there is a subtle difference between the
two that is seen in the context of some build tools such as Maven, which is
used in the description below.

Maven allows the user to declare Artifact-level dependencies in the build
script of a module, its pom.xml file. These dependencies are needed and
resolved by Maven when building the module.

Note that Maven supports various dependency scopes, such as compile and
runtime. All these dependencies are required at some point or another during
the build, in one Maven life cycle phase or another. Dragom is not concerned
with the scope of dependencies.

Maven also supports the concept of parent POM which is not called a dependency,
but is nevertheless required when building the module and is therefore a
reference from Dragom's point of view. So using the term reference instead of
dependency helps avoid confusion.

Maven also supports the concept of dependency management where the build script
of a module can declare a dependency without specifying the version. The
version then needs to be specified in the dependencyManagement section of a
parent POM. This dependency management declaration therefore specifies the
complete coordinates of the artifact, including its groupId and artifactId so
that it can be matched with the versionless dependency in the module build
script, and the version itself.

From Dragom's point of view, the reference is not from the Module, but rather
from the Module corresponding to the parent POM which contains the dependency
management declaration.

It is as if the reference was moved from a direct reference in the Module to an
indirect reference in one of its parent's depedency management. In terms of
build, that reference is not a dependency as it is not required for "building"
the module corresponding to that parent POM (unless the module also has an
internal dependency on that parent POM). But from a reference management stand
point, this is where the reference truly is.

As a convincing argument, consider the following exame: Module A depends on
module B without specifying its version, and module A has a parent POM P which
specifies the version for B in a dependency management declaration. If A is to
be updated to refer to another version of B, it is actually P which will be
updated, and A may need to be updated to refer to the new version of P. So
although the true build dependency is from A to B, the ReferencePath is A -> P
-> B.

Note that a dependency management declaration in a parent POM is involved only
if the artifact is actually referenced as a versionless dependency. In the
previous example, if A does not ha



parent
profiles


job of reference manager to extract references
meta data about references


{% include links.html %}

[//]: # (TODO: no internal dependencies; only between ModuleVersion's; internal references are implementation details (but must be within the same ModuleVersion); submodules can refer between them, but once get out to another ModuleVersion, cannot come back since no cycle.; no outside references; dependency management vs dependencies vs parent in maven)
