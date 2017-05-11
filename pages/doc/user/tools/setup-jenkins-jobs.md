---
title: setup-jenkins-jobs
# keywords:
last_updated: 2017-05-08
# tags: [doc]
# summary: ""
sidebar: doc_user_sidebar
permalink: doc-user-tools-setup-jenkins-jobs.html
toc: false
# folder: doc
---

Name
----

`dragom setup-jenkins-jobs`

Synopsis
--------

```
dragom setup-jenkins-jobs [ <option> ... ]
```
> Main synopsis.

```
dragom setup-jenkins-jobs --help
```
> Displays basic help information.

Description
-----------

Manages Jenkins jobs corresponding to the
[ModuleVersion's][doc-config-design-module-version] having dynamic
[Version's][doc-config-design-version] within a
[ReferenceGraph][doc-config-design-reference-graph].

Applications are generally composed of multiple
[Module's][doc-config-design-module] organized in a ReferenceGraph. When doing
development work on such an application, dynamic
[Version's][doc-config-design-version] (branches) are typically created for
those Module's which are modified, including Module's which are modified only
to update a reference on another Module which itself is modified.

In order to do continuous integration, one typically has to have a job in a
build automation server for each Module and configure it so that it is
triggered automatically when a commit is performed in the SCM. The jobs must
also be configured so that when one job which produces
[Artifact's][doc-config-design-artifact] consumed by the build other jobs
completes successfully, the other jobs are triggered even if no commit is
performed for the corresponding Module's.

Configuring those jobs manually is tedious and error-prone, especially when
multiple Module's are involved, and when this must be done often because the
ReferenceGraph changes or because new projects start regularly, each using
different dynamic Version's to better manage each project's evolution and
integration points with other projects.

This tool automates the management of these jobs, including their creation,
update and suppression, within the Jenkins build automation server.

It builds a ReferenceGraph starting with the root ModuleVersion's defined
within the [ExecContext][doc-config-design-exec-context] and matching
the [ReferencePath's][doc-config-design-reference-path] with the
[effective ReferencePathMatcher](doc-config-design-reference-path-matcher.html#effective-reference-path-matcher).
Only dynamic Version's are considered since it does not make sense to create
a job for a static Version.

Once the ReferenceGraph is created, it traverses all its ModuleVersion's
whether they were matched or not in the all-parents-first order so that
whenever a ModuleVersion is visited, all its parents have already been visited
and therefore corresponding jobs have already been created. When visiting a
ModuleVersion, it creates a Jenkins job for it.

This tool maintains an items-created file within the workspace to keep track of
the folders and jobs it has created. This allows the tool to be used
incrementally to adjust the jobs as the ReferenceGraph evolves during
development, including adding new jobs, updating existing jobs and deleting
corresponding to ModuleVersion's which are no more in the ReferenceGraph.

Jenkins folders provided by the CloudBees Folders plugin are supported.  


Options
-------

`--items-created-file=<items-created-file>`: Items-created file. Defaults to
`.dragom/jenkins-items-created.txt` in the workspace.

`--items-create-file-mode=<items-created-file-mode>`: Items-created file mode.
The possible values are:

- `IGNORE`: Replace the items-created file, ignoring its current contents.
- `MERGE`: Add new items, replace existing jobs. Existing folders and jobs are
  not touched, other than manipulating the jobs within existing folders. This
  is the default value.
- `REPLACE`: Add new items, replace existing jobs and delete items (folders and
  jobs) which do not exist anymore. Existing folders are not touched, other
  than manipulating the jobs within them. This mode can be used to perform a
  complete cleanup of previously created items by specifying an empty element
  ReferencePathMatcher.
- `REPLACE_DELETE_FOLDER_ONLY_IF_EMPTY`: Similar to REPLACE, but folders which
  do not exist anymore are deleted only if empty (after having deleted the jobs
  which do not exist anymore).
- `REPLACE_NO_DELETE_FOLDER`: Similar to REPLACE, but folders are not deleted
  (only jobs). 

`--reference-path-matcher=<reference-path-matcher>`: Tool-specific
[element ReferencePathMatcher](doc-config-design-reference-path-matcher.html#element-reference-path-matcher).
Multiple occurrences are supported.

`--workspace=<workspace-directory>`: Path to the workspace. If not specified,
the current directory is assumed to be the workspace.

`--user-properties=<user-properties-file>`: Initialization properties file.
Defaults to `~/.dragom/dragom.properties` if it exists ("~" represents the
user's home directory).

`--tool-properties=<tool-properties-file>`: Tool properties file.

`-D<tool-property>=<value>`: Defines a tool property. Multiple occurrences are
supported.

`--no-confirm`: Disables all "do you want to continue?" prompts.

`--no-confirm-context=<context>`: Disables "do you want to continue?" prompts
for the specified context. Multiple occurrences are supported.


`SimpleJenkinsJobInfoPluginBaseImpl`
----------------------------------------

This tool uses `JenkinsJobInfoPlugin` to know which job to create for a given
[ModuleVersion][doc-config-design-module-version] (its name), whether it is
based on a template or is a regular job, what are its parameters if it is based
on a template, and what is its configuration if it is a regular job. The exact
behavior of the tool therefore depends on the plugin implementation.

Dragom does not provide a default implementation for this plugin. But it does
provide a flexible base implementation class
`SimpleJenkinsJobInfoPluginBaseImpl` to make it easy to develop such an
implementation which only has to take care of the non-generic job
configuration. It is expected that most `JenkinsJobInfoPlugin` implementations
will be based on `SimpleJenkinsJobInfoPluginBaseImpl`.

`SimpleJenkinsJobInfoPluginBaseImpl`, and therefore any implementation based on
it, supports the following runtime properties which can be specified by the
user, generally as tool properties:

- `JENKINS_USE_NODE_PATH`: Indicates to use the parent
  [NodePath][doc-config-design-node-path] of the
  [Module][doc-config-design-module] as the subfolder where to create the job.
  It is appended to the base Jenkins URL and root folder preconfigured in the
  customized distribution package.
  
  If true, `JENKINS_SUBFOLDER` is ignored.

  If both `JENKINS_USE_NODE_PATH` and `JENKINS_SUBFOLDER` are not defined,
  interaction with the user occurs to allow him to define a subfolder.

  It is evaluated on the Module for which a job is to be created.

  The possible values are true and false.

- `JENKINS_SUBFOLDER`: Subfolder where the job for the
  [Module][doc-config-design-module] is created. It is appended to the base
  Jenkins URL and root folder preconfigured in the customized distribution
  package.

  If both `JENKINS_SUBFOLDER` and `JENKINS_USE_NODE_PATH` are not defined,
  interaction with the user occurs to allow him to define a subfolder.

  It is evaluated on the Module for which a job is to be created.

- `JENKINS_PROJECT`: Project name to include in the path of the job. Optional.

  It is appended to the job path built up to now with "/" as a separator.
  
  If it ends with "/", it is considered to be a subfolder in relation to what
  follows. Otherwise, it is considered to be prefix.

  It is evaluated on the Module for which a job is to be created.

- `JENKINS_INCLUDE_VERSION`: Indicates to include the
  [Version][doc-config-design-version] of the
  [Module][doc-config-design-module].

  If specified, the Version is prefixed to the Module name that is used as the
  main job name, with "_" as separator.

  If `JENKINS_PROJECT` ends with "/", the Version and the Module name form the
  name of the job within the project subfolder. Otherwise, the Version and the
  Module name are suffixed to the project with "_" as separator to form a
  3-part job name within the path being built. If `JENKINS_PROJECT` is not
  defined, the Version and Module name form the name of the job within the path
  being built, with no project subfolder or prefix.
  
  If not specified, only the Module name is used.

  It is evaluated on the Module for which a job is to be created.

  The possible values are true and false. Defaults to false.

{% include links.html %}
