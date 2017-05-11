---
title: root-manager
# keywords:
last_updated: 2017-05-10
# tags: [doc]
# summary: ""
sidebar: doc_user_sidebar
permalink: doc-user-tools-root-manager.html
toc: false
# folder: doc
---

Name
----

`dragom root-manager`

Synopsis
--------

```
dragom root-manager [ <option> ... ] <command and arguments>
```
> Main synopsis.

```
dragom root-manager --help
```
> Displays basic help information.

### Command and arguments

`list`: Displays the list of root ModuleVersion's.

`add <module-version> ...`: Adds root ModuleVersion's to the list.

`add-from-file <file-containing-module-version> ...`: Adds root
ModuleVersion's from a file to the list.

`add-artifact <artifact-coordinates> ...`: Adds ModuleVersion's corresponding
to Artifact coordinates to the list.

`add-artifact-from-file <file-containing-artifact-coordinates> ...`: Adds
ModuleVersion's corresponding to Artifact coordinates from a file to the
list.

`remove <module-version>`: Removes a ModuleVersion from the list.

`remove-all`: Removes all ModuleVersion's from the list.

`list-reference-path-matchers`: Displays the list of root
ReferencePathMatcher's.

`add-reference-path-matcher <reference-path-matcher>`: Adds a root
ReferencePathMatcher to the list.

`remove-reference-path-matcher <reference-path-matcher>`: Removes a root
ReferencePathMatcher from the list.

`remove-all-reference-path-matchers`: Removes all root ReferencePathMatcher's
from the list.

Description
-----------

Manages the list of
[root ModuleVersion's][doc-config-design-root-module-version] and
root
[element ReferencePathMatcher](doc-config-design-reference-path-matcher.html#element-reference-path-matcher)
within the [ExecContext][doc-config-design-exec-context].

Tools use the list of root ModuleVersion's and root element
ReferencePathMatcher's to determine the scope of their actions.

Options
-------

`--workspace=<workspace-directory>`: Path to the workspace. If not specified,
the current directory is assumed to be the workspace.

`--allow-duplicate-modules`: Allow having multiple ModuleVersion's for the
same Module (different Version's). If not specified with the add\* commands
and if ModuleVersion exists for the same Module as the one specified, it is
replaced.

`--user-properties=<user-properties-file>`: Initialization properties file.
Defaults to `~/.dragom/dragom.properties` if it exists ("~" represents the
user's home directory).

`--tool-properties=<tool-properties-file>`: Tool properties file.

`-D<tool-property>=<value>`: Defines a tool property. Multiple occurrences are
supported.

`--no-confirm`: Disables all "do you want to continue?" prompts.

`--no-confirm-context=<context>`: Disables "do you want to continue?" prompts
for the specified context. Multiple occurrences are supported.

Arguments
---------

`<module-version>`: [ModuleVersion][doc-config-design-module-version] literal.

`<file-containing-module-versions>`: File containing one
[ModuleVersion][doc-config-design-module-version] literal per line.

`<artifact-coordinates>`: Artifact coordinates in Maven format `<groupId>:<artifactId>:<version>`.

`<file-containing-artifact-coordinates>`: File containing one
Artifact coordinates literal per line.

`<reference-path-matcher>`:
[Element ReferencePathMatcher](doc-config-design-reference-path-matcher.html#element-reference-path-matcher)
literal.

{% include links.html %}
