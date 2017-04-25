---
title: ReferencePathMatcher
# keywords:
last_updated: 2017-04-10
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-reference-path-matcher.html
toc: true
# folder: doc
---

Many Dragom jobs and tools operate by traversing a ReferenceGraph and
performing some actions on the ModuleVersion's in it. Generally the user will
not want to target all ModuleVersion's in the ReferenceGraph. To allow the user
to select which ModuleVersion's should be targeted by a job, Dragom supports
ReferencePathMatcher's.

A ReferencePathMatcher is a boolean condition which either matches or does not
match a given ReferencePath. During the traversal of a ReferenceGraph, the leaf
ModuleVersion of each ReferencePath is visited only if the ReferencePath is
matched by the effective ReferencePathMatcher.

Implicit filtering by jobs
--------------------------

The effective ReferencePathMatcher is not the only condition which determines
whether a given ModuleVersion is visited. By design, some jobs may impose
additional conditions on whether to visit or not ModuleVersion's. For example:

- Some jobs operate only on static or dynamic Version's;
- Some jobs skip traversing part of a ReferenceGraph rooted at some
  intermediate ModuleVersion when some condition is met during the traversal;
- ModuleVersion's can occur multiple times in a ReferenceGraph and some jobs
  avoid visiting the same ModuleVersion more than once.

This implies that some ReferencePath's in a ReferenceGraph may not be visited
even if they would match the effective ReferencePathMatcher.

ModuleVersion vs. ReferencePath
-------------------------------

ReferencePathMatcher's match ReferencePath's, not simply ModuleVersion's. A
RecerencePathMatcher can be made to match any ReferencePath with a specific
leaf ModuleVersion, but in general a ReferencePathMatcher matching a
ReferencePath with a given leaf ModuleVersion does not necessarily match
another ReferencePath with the same leaf ModuleVersion if the other
ModuleVersion's in the ReferencePath do not match.

ReferencePathMatcher implementations
------------------------------------

Within Dragom a ReferencePathMatcher is represented by a Java interface and is
therefore an abstraction which can have various implementations.

Implementation|Description
--------------|-----------
And           |Combines multiple operand ReferencePathMatcher's in a logical "and" operation. The resulting ReferencePathMatcher matches a given ReferencePath if all the operand ReferencePathMatcher's match it or if no operand ReferencePathMatcher's are defined.
Or            |Combines multiple operand ReferencePathMatcher's in a logical "or" operation. The resulting ReferencePathMatcher matches a given ReferencePath if at least one operand ReferencePathMatcher's match it or if no operand ReferencePathMatcher's are defined.
Not           |Logical "not" operation on an operand ReferencePathMatcher. The resulting ReferencePathMatcher matches a given ReferencePath if the operand ReferencePathMatcher matches it. 
All           |Always matches, regardless of the ReferencePath. Useful when a ReferencePathMatcher is required and it is desired to match all ReferencePath's. 
Element       |See [Element ReferencePathMatcher](#element-referencepathmatcher) below.

Among the ReferencePathMatcher implementations above, only the "element"
implementation is explicitly exposed to the user. The others are used
internally within Dragom when multiple ReferencePathMatcher's need to be
combined to obtain the desired effective ReferencePathMatcher.

Because of this, from the user's point of view, when we speak about
ReferencePathMatcher's, we generally mean element ReferencePathMathcer.

Element ReferencePathMatcher
----------------------------

The element ReferencePathMatcher matches ReferencePath's using a sequence of
matching elements.

It is a sequence of element matchers where each element matcher includes
part matchers for matching different parts of a reference in a ReferencePath.
The parts that can be matched are:

Source-level parts:

- NodePath of the ModuleVersion
- Version of the ModuleVersion

Artifact-level parts:

- GroupId of the Artifact's produced by the Module
- ArtifactId of the Artifact's produced by the Module
- ArtifactVersion of the Artifact's produced by the Module

An element matcher cannot contain both source-level and artifact-level parts.

The `*` element matcher matches any one reference in a ReferencePath. 

The `**` element matcher matches not only any reference, but any number
(including 0) of consecutive references in a ReferencePath.

`*` and `**` are chosen for their similarity in
<a href="https://ant.apache.org/manual/dirtasks.html#patterns" target="_blank">Ant patterns</a>.

The whole element ReferencePathMatcher is always absolute meaning that all
references in the ReferencePath must match starting with the first one and
ending with the last one. But if the first element matcher in the element
ReferencePathMatcher is "**", this effectively results in a relative
element ReferencePathMatcher.

A part matcher can be either fixed which must match exactly, or a regular
expression. It can also be empty which always matches, regardless of the value
of the corresponding ReferencePath reference part.

An element ReferencePathMatcher has a literal form which can be defined in BNF
as follows:

```
<element-reference-path-matcher> ::=   ""
                                     | <element-matcher-sequence>

<element-matcher-sequence>       ::=   <element-matcher>
                                     | <element-matcher> "->" <element-matcher-sequence>

<element-matcher>                ::=   "*"
                                     | "**"
                                     | <source-level-matcher>
                                     | <artifact-level-matcher>

<source-level-matcher>           ::=   "/" <node-path-matcher>
                                     | "/" <node-path-matcher> ":" <version-matcher>

<node-path-matcher>              ::=   ""
                                     | <fixed-node-path>
                                     | "(" <node-path-regex> ")"

<version-matcher>                ::=   ""
                                     | <fixed-version>
                                     | "(" <version-regex> ")"

<artifact-level-matcher>         ::=   <group-id-matcher>
                                     | <group-id-matcher> ":" <artifact-id-matcher>
                                     | <group-id-matcher> ":" <artifact-id-matcher> : <artifact-version-matcher>

<group-id-matcher>               ::=   ""
                                     | <fixed-group-id>
                                     | "(" <group-id-regex> ")"

<artifact-id-matcher>            ::=   ""
                                     | <fixed-artifact-id>
                                     | "(" <artifact-id-regex> ")"

<artifact-version-matcher>       ::=   ""
                                     | <fixed-artifact-version>
                                     | "(" <artifact-version-regex> ")"
```

The following non-terminals are not defined in BNF:

- `<fixed-node-path>`: A NodePath which must match exactly
- `<node-path-regex>`: A regular expression for matching a NodePath
- `<fixed-version>`: A Version which must match exactly
- `<version-regex>`: A regular expression for matching a Version
- `<fixed-group-id>`: A groupId which must match exactly
- `<group-id-regex>`: A regular expression for matching a groupId
- `<fixed-artifact-id>`: An artifactId which must match exactly
- `<artifact-id-regex>`: A regular expression for matching an artifactId
- `<fixed-artifact-version>`: An ArtifactVersion which must match exactly
- `<artifact-version-regex>`: A regular expression for matching an ArtifactVersion

Notes:

- The leading `/` in an element matcher differentiates between a source-level
  matcher from an artifact-level matcher. It is not part of the NodePath but
  since Nodes in a NodePath are separated using `/`, using it as an
  introductory character for source-level matcher is intuitive;
- The `<*-matcher>` production rules can be the empty string or absent from the
  `<source-level-matcher>` or `<artifact-level-matcher>` which always matches
  the corresponding part in a reference;
- When a `<*-matcher>` is enclosed in `(` and `)` it is treated as a Java regex
  (see Pattern class). But these characters are not actually part of the regex.
  `(` and `)` are useful reserved characters in regexes and can still be used
  since since `)` will be considered as a closing regex delimiter only if
  followed by `:`, `->` or the end of the literal;
- Because of the way the different parts of an element ReferencePathMatcher
  literal are delimited, some characters and character sequences cannot be used
  within element matcher parts. They are `:` and `->`. Dragom currently does
  not support escaping them. Fortunately they are generally not used within
  ReferencePath elements. And `:` can still be used within a regex, either as a
  literal (escaped with `\`) or as the special character, since when the
  opening regex delimiter `(` is found, the part is delimited by the closing
  `)` followed by `:`, `->` or the end of the element ReferencePathMatcher
  literal, regardless of occurrences of `:` within the regex;
- An empty element ReferencePathMatcher matches nothing;
- The first element in a ReferencePath is not a reference from a ModuleVersion
  to another ModuleVersion, but rather a user-supplied reference to a root
  ModuleVersion. Such a reference is generally always a source-level reference,
  so that the first element in an element ReferencePathMatcher is generally
  always a source-level matcher;
- A given Module can produce multiple Artifacts and therefore multiple
  Artifact-level references can refer to the same Module. An element
  ReferencePathMatcher that includes an Artifact-level element matcher
  will match only corresponding Artifact-level references. If the module
  includes a reference to an Artifact that is not matched by the
  element matcher, the match is negative even though the Artifact may be
  produced by the same Module;
- ReferencePath does not include internal references within Modules. Some build
  tools such as Maven support modules which can refer to other modules within
  the same Module. Dragom only considers inter-Module (as opposed to
  intra-Module) references. This means that an element ReferencePathMatcher
  using Artifact-level element matcher can sometimes be confusing as only the
  external references are part of ReferencePath's.

Here are examples of element ReferencePathMatcher literals along with some
explanations:

- `/Domain/app-a`: Matches the specific Module whose NodePath is
  `Domain/app-a`, regardless of its Version, when that Module is the first
  element of the ReferencePath;
- `**->/Domain/app-a:(D/.*)`: Matches the specific ModuleVersion whose
  NodePath is `Domain/app-a` if its Version is dynamic, when that ModuleVersion
  is the last element of the ReferencePath (there can be any number of other
  elements preceding it within the ReferencePath;
- `/Domain/app-a->**`: Matches any ReferencePath of depth 1 or more, as
  long as the first ModuleVersion has the NodePath `Domain/app-a`;
- `*->/Domain/app-a->**`: Matches any ReferencePath of depth 2 or more,
  as long as the second ModuleVersion has the NodePath `Domain/app-a`;
- `**->com.acme::(.*-SNAPSHOT)`: Matches any ReferencePath of depth 1 or
  more, where the last reference is to an Artifact having the groupId
  `com.acme` and a SNAPSHOT ArtifactVersion.

Effective ReferencePathMatcher
------------------------------

Dragom tools allow the user to define ReferencePathMatcher's to select the
ReferencePath's and ModuleVersion's on which to operate. Without the user
explicitly specifying it, these ReferencePathMatcher's are actually the
operands of a "Or" ReferencePathMatcher that is implicitly created internally
by Dragom and which is used as a surrogate representing the
ReferencePathMatcher's defined by the user, providing the intuitively expected
behavior that each ReferencePathMatcher selects a different subset of the
ReferencePath's in the ReferenceGraph, the union of which being the set of all
selected ReferencePath's.

Other factors can also affect the ReferencePath's targeted by a tool. For
example, Dragom supports defining ReferencePathMatcher's to impose
workspace-level constraints on the ReferencePath's selected by tools operating
within the workspace. These ReferencePathMatcher's are combined as operands of
a "Or" ReferencePathMatcher created internally by Dragom, wbich is itself
combined using a "And" ReferencePathMatcher with the "Or" ReferencePathMatcher
representing the ReferencePathMatcher's defined by the user at tool invocation
time.

It is this single top-level ReferencePathMatcher that is actually used by a
tools and is called the effective ReferencePathMatcher.

Optimization for children
-------------------------

Although a ReferencePathMatcther can theoretically match arbitrary
ReferencePath's, given such a ReferencePath, which it may or may not match, we
can often infer on its ability to match its children.

For example, consider the following element ReferencePathMatcher:

`**->/Domain/app-a:D/master`

This ReferencePathMatcher matches any ReferencePath whose leaf ModuleVersion is
`Domain/app-a:D/master`. Given the ReferencePath
`OtherDomain/app-b:D/master->Domain/app-a/D/master` (which it matches), we know
it does not match any of its children. Also, given the (single element)
ReferencePath `OtherDomain/app-b:D/master`, we know it may match some of its
children (in this case it matches exactly one child).

As another example. consider the following element ReferencePathMatcher:

`*->/Domain/app-a:D/master->**`

This ReferencePathMatcher matches any ReferencePath whose 2<sup>nd</sup>
ModuleVersion is `Domain/app-a:D/master`. Given the (single element)
ReferencePath `Domain/app-a:D/develop`, which it does not match, we know it
does not match any of its children. Any child not having
`Domain/app-a:D/master` as its 2<sup>nd</sup> ModuleVersion is not matched and
no children exist with the 2<sup>nd</sup> ModuleVersion `Domain/app-a:D/master`
since this would create a cycle in the ReferencePath.

Also, given the ReferencePath `OtherDomain/app-b->Domain/app-a:D/master`, wbich
it matches, we know it matches all of its children.

This inferred knowledge about a ReferencePathMatcher's ability to match
children of a given ReferencePath is used to optimize ReferenceGraph traversal
by avoiding to visit parts of a ReferenceGraph whose ReferencePath's cannot be
matched.

The exact inference rules are not documented as they can evolve over time to
improve the optimizations. When the current rules do not infer on a
ReferencePathMatcher's ability to match children of a ReferencePath, they
conclude that children can be matched, which is always safe as this forces the
traversal of the children and applying the effective ReferencePathMatcher to
each of them.

Note that the ReferencePathMatcher's implementing logic conditions do support
these optimizations based on their operands' optimizations. For example, a
"And" ReferencePathMatcher can match the children of a ReferencePath if and
only if all of its operand ReferencePathMatcher's can match its children. As
another example, the "Not" ReferencePathMatcher does not match any children of
a ReferencePath if its operand ReferencePathMatcher matches all of its
children.

{% include links.html %}

[//]: # (TODO: Have a graphic for Effective ReferencePathMatcher)
