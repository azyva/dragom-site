---
title: References and dependencies
# keywords:
last_updated: 2017-05-08
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-references-dependencies.html
toc: true
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
if the artifact is actually referenced as a versionless dependency in the
module. In the previous example, if A did not have a (versionless) dependency
on B, the reference from P to B would still be part of the ReferenceGraph, but
would not actually be used. This may be seen as a problem as it implies
ReferenceGraph's can include useless references and ModuleVersion's. But the
fact that B is referenced by P implies that any other module which may have P
as its parent (direct or indirect, and even a static ArtifactVersion of P) may
use that dependency management declaration, so that when doing work involving
P, it remains logical to take into consideration the reference to B and ensure
it is correctly defined.

A similar situation arises with Maven profiles which can contain dependencies
and can be activated or not depending on conditions external to the module
itself. For example a test profile could be activated for CI builds but not for
release builds. This means that some dependencies may not always be used, even
though Dragom unconditionally considers them as references. But the argument is
the same: Because the dependency can be activated in some contexts, it deserves
to be taken into consideration.

Reference metadata
------------------

To help the user in interpreting references and ReferencePath's, Dragom
supports attaching metadata to references.

In the context of Maven, for example, the module (not in the Dragom sense here)
from which a reference comes from is stored in this metadata and shown to the
user in ReferencePath's.

Internal and external references
--------------------------------

Recall that in Dragom, Module's are atomic. Build tools such as Maven support
organizing the source code using modules (not tbe Dragom concept here). These
modules have their own dependencies, parents and dependency management
declarations. Some of these references refer to artifacts corresponding to
other Module's. But other references are between the modules and therefore
internal to the Module. It is a Dragom requirement that these internal references
be to the same ArtifactVersion as that of the Module so that atomicity is
preserved in fact at the ModuleVersion level.

Internal references within a ModuleVersion are considered implementation
details of the Module and are not considered by Dragom. Only external references
are. These external references can come from any module within the Module.
Dragom does not care and from its point of view the references all come from
the Module.

Plugin-based reference management
---------------------------------

In Dragom, it is the responsibility of the ReferenceManager plugin to manage
the references of a Module. Here we have used Maven as an example which is
therefore specific to the Maven implementation of this plugin
(MavenReferenceManagerImpl). It analyses the pom.xml files of the Maven modules
of a Module and extracts the dependencies, parents and dependency management
declarations as references.

Other build tools can obviously be supported with other plugin implementations.

{% include links.html %}
