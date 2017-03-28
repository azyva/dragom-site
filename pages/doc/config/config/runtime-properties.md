---
title: Runtime properties
# keywords:
last_updated: 2017-03-19
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-config-runtime-properties.html
toc: false
# folder: doc
---

The [runtime properties](doc-config-design-properties.html#runtime-properties)
are documented here in alphabetical order.

You can consult a specific plugin implementation documentation to learn which
runtime propeties it uses. 

BUILD_LOG_DIR {#BUILD_LOG_DIR}
-------------

Used by        |[Build][doc-config-jobs-BuildRemote]
Possible values|Absolute or relative paths
Default value  |Root of the workspace directory.
Access scope   |[Module][doc-config-design-module] being built

Directory for build logs. A separate build log file is created for each Module
(since they can be built in parallel).

If not specified, build logs will be extracted to the root of the workspace
directory.

If relative, it is relative to the current directory.

BUILD_MONITORING_CYCLE_DELAY (#BUILD_MONITORING_CYCLE_DELAY}
----------------------------

Used by        |[Build][doc-config-jobs-BuildRemote]
Possible values|Positive integers
Default value  |None (must be defined)
Access scope   |Global.

Delay between build monitoring cycles, in milliseconds.

When [RemoteBuilderPlugin][doc-config-model-plugins-RemoteBuilderPlugin] is
invoked to submit a build, the monitoring of the build status is performed by
querying the handle the plugin returned. This typically involves communication
with the remote build server and must be paced correctly to balance the need
for fast feedback and the desire to avoid overwhelming the server with
requests.

BUILD_SCOPE {#BUILD_SCOPE}
-----------

Used by        |[Build][doc-config-jobs-Build]
Possible values|`ONLY_USER`, `ONLY_USER_ABORT_IF_SYSTEM`, `ALL`, `ALL_ABORT_IF_SYSTEM_AND_NO_ARTIFACT`
Default value  |`ONLY_USER`
Access scope   |[Module][doc-config-design-module] being built


BuildScope.

`ONLY_USER`|Build only [ModuleVersion's][doc-config-design-module-version] that are in a user workspace directory and ignore ModuleVersion's that are not.
`ONLY_USER_ABORT_IF_SYSTEM`|Build only ModuleVersion's that are in a user workspace directory and fail if a ModuleVersion needing to be built is not.
`ALL`|Build all ModuleVersion's, whether they are in a user workspace directory or not. This is useful if it is desired to rebuild a ModuleVersion along with its child ModuleVersion's without having to explicitly check them out in the workspace.
`ALL_ABORT_IF_SYSTEM_AND_NO_ARTIFACT`|Build all ModuleVersion's, but if a ModuleVersion is not in a user workspace directory, fail if the Module does not expose [ArtifactInfoPlugin][doc-config-model-plugins-ArtifactInfoPlugin], indicating that it does not produce artifacts and that its build is likely required in a user workspace directory so that ModuleVersion's that depend on it can in turn build successfully.

BUILD_CONTEXT {#BUILD_CONTEXT}
-------------

Used by        |[Build][doc-config-jobs-Build]
Possible values|Any
Default value  |None
Access scope   |[Module][doc-config-design-module] being built


Build context to pass to the build method of [BuilderPlugin][doc-config-model-plugins-BuilderPlugin].

IND_BATCH_MODE {#IND_BATCH_MODE}
--------------

Used by        |[DefaultUserInteractionCallbackPluginImpl][doc-config-exec-context-plugin-impls-DefaultUserInteractionCallbackPluginImpl]
Possible values|true, false
Default value  |false
Access scope   |Global

Indicates that no information must be requested from the user. If code attempts
to request information from the user, an exception is thrown.

IND_BUILD_REFERENCE_PATH {#IND_BUILD_REFERENCE_PATH}
------------------------

Used by        |[BuildRemote][doc-config-jobs-BuildRemote]
Possible values|true, false
Default value  |false
Access scope   |[Module][doc-config-design-module] being built

Indicates whether to also build
[ModuleVersion's][doc-config-design-module-version] within the
[ReferencePath's](doc-config-design-reference-path-matcher.html#ReferencePath)
to the matched ModuleVersion's.

IND_ECHO_INFO {#IND_ECHO_INFO}
-------------

Used by        |[DefaultUserInteractionCallbackPluginImpl][doc-config-exec-context-plugin-impls-DefaultUserInteractionCallbackPluginImpl]
Possible values|true, false
Default value  |false
Access scope   |Global

Indicates to echo information input by the user. By default, information input
by the user is not echoed since this would produce redundant information in the
standard output stream.

This can be useful during development for automated tests where user responses
are provided by a mock standard input stream and would otherwise not shown in
the standard output stream.

INCLUDE_NODE_PATH_IN_BUILD_LOG_FILE_NAMES {#INCLUDE_NODE_PATH_IN_BUILD_LOG_FILE_NAMES}
-----------------------------------------

Used by        |[Build][doc-config-jobs-BuildRemote]
Possible values|true, false
Default value  |false
Access scope   |[Module][doc-config-design-module] being built

Indicates if the [NodePath][doc-config-design-node-path] of the ClassificationNode} should be included in build log file names.





???





IND_PASSWORD_INPUT_NORMAL {#IND_PASSWORD_INPUT_NORMAL}
-------------------------

Used by        |[DefaultUserInteractionCallbackPluginImpl][doc-config-exec-context-plugin-impls-DefaultUserInteractionCallbackPluginImpl]
Possible values|true, false
Default value  |false
Access scope   |Global

Indicates to treat password input as normal. By default, password input is not
performed using the standard input stream but directly from the console.

This can be useful during development for automated tests where user responses
are provided by a mock standard input stream. 

INDENT {#INDENT}
------

Used by        |[DefaultUserInteractionCallbackPluginImpl][doc-config-exec-context-plugin-impls-DefaultUserInteractionCallbackPluginImpl]
Possible values|Positive integer
Default value  |4
Access scope   |Global

Number of spaces to use to indent information provided to the user when code
starts a new indentation level.

MAIN_MODULE_VERSION {#MAIN_MODULE_VERSION}
-------------------

Used by        |[MainModuleVersionWorkspacePluginFactory][doc-config-exec-context-plugin-impls-MainModuleVersionWorkspacePluginFactory]
Possible values|[ModuleVersion][doc-config-design-module-version] in literal form
Default value  |None 
Access scope   |Global

Main ModuleVersion associated with the workspace.

This is expected to be defined as a
[tool property](doc-config-design-properties.html#tool-properties) when the
workspace is not initialized yet. Once initialized, the main ModuleVersion is
persisted as an
[ExecContext property](doc-config-design-properties.html#exec-context-properties). 

WRAP_MODE {#WRAP_MODE}
---------

Used by        |[DefaultUserInteractionCallbackPluginImpl][doc-config-exec-context-plugin-impls-DefaultUserInteractionCallbackPluginImpl]
Possible values|`NO_WRAP`, `WRAP_WITH_INDENT`, `WRAP_WITHOUT_INDENT`
Default value  |`NO_WRAP`
Access scope   |Global

Wrap mode.

`NO_WRAP`|Do not perform wrapping at the application level. Wrapping will be performed by the console attached to the standard output stream, if any.
`WRAP_WITH_INDENT`|Wrap to the defined width including the indent. Indented sections will have less available width for the actual text, but the whole output will be indented uniformly.
`WRAP_WITHOUT_INDENT`|Wrap to the specified width not including the indent. All sections regardless of indent will have the same available with for the actual text and global width of the whole output will increase with the indent.

WRAP_WIDTH {#WRAP_WIDTH}
----------

Used by        |[DefaultUserInteractionCallbackPluginImpl][doc-config-exec-context-plugin-impls-DefaultUserInteractionCallbackPluginImpl]
Possible values|Positive integer
Default value  |132
Access scope   |Global

Width to use when [`WRAP_MODE`](#WRAP_MODE) is `WRAP_WITH_INDENT` or `WRAP_WITHOUT_INDENT`.

{% include links.html %}












  /**
   * Runtime property that specifies if the {@link Version} of the
   * {@link ModuleVersion} should be included in build log file names. The default
   * value is false.
   * <p>
   * It is the version string that is included (excluding the {@link VersionType}).
   * <p>
   * It is accessed in the context of each {@link Module} so that its value can be
   * different for each Module. But in general it will be defined for the root
   * NodePath only.
   */
  public static final String RUNTIME_PROPERTY_IND_INCLUDE_VERSION_IN_BUILD_LOG_FILE_NAMES = "INCLUDE_VERSION_IN_BUILD_LOG_FILE_NAMES";

