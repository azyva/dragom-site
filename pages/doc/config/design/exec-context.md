---
title: ExecContext
# keywords:
last_updated: 2017-03-18
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-exec-context.html
toc: false
# folder: doc
---

When using Dragom, the user generally invokes multiple Dragom tools in sequence
in order to complete a global task involving many individual steps. He can also
come back later expecting tools which he invokes to build upon the previous
state.

Dragom tools execute within a context called the ExecContext. The main
motivation for the ExecContext is to support such a state-based workflow. For
example, starting from scratch, the user may:

- Define one or more static root ModuleVersion's with the root-manager tool;
- Change the Version of a ModuleVersion in the ReferenceGraph to a new dynamic
  Version in order to isolate some development work. This would be done with
  the switch-to-dynamic-version tool;
- Setup the continuous integration jobs in a build automation tool to support
  the development effort on the dynamic ModuleVersion's;
- Rebuild all the dynamic ModuleVersion's in the ReferenceGraph;
- Perform a release of the dynamic ModuleVersion's in the ReferenceGraph.

The ExecContext also offers other facilities to tools, jobs and plugins
related to their execution environment. Facilities offered by the ExecContext
include:

- Access to the Model
- Access to ExecContext plugins
- Access to initialization properties (read only)
- Access to tool properties (read only)
- Persistent properties which they can get and set
- Workspace directory where they can manage persistent files and subdirectories
- Secure storage for passwords used during tool execution (to avoid requesting
  the same password from the user multiple times)

The ExecContext is nearly always backed by a workspace directory where all the
persistent data related to the context is stored. This is similar to how the
Eclipse IDE works.

Note that the management of the workspace is performed by WorkspacePlugin which
can have various implementations. The default implementation provided by
Dragom, DefaultWorkspacePluginFactory, is expected to be adequate in most cases
but other implementations are possible which may not handle the concept of
workspace directory the same way.

Also Dragom theoretically supports a workspace-less ExecContext. But most
tools, jobs and plugins require a workspace and use WorkspacePlugin.

Transient data {#transient-data}
--------------

ExecContext also supports transient data storage scoped by a single tool
invocation, allowing multiple jobs and plugins to remember or share data during
a tool execution.

Transient data are not persisted anywhere and Dragom ensures they are destroyed
when the tool execution ends, even if an application embeds Dragom and
programmatically invokes tools within the same JVM execution.

Plugins
-------

Although allowing a state-based workflow is an important motivation for the
ExecContext, the latter is also there to provide a general execution context to
tools allowing them to interact with their environment. These point of
interaction are provided by ExecContext plugins. WorkspacePlugin is one such
plugin. The management of a secure storage area for passwords also mentioned
above is also provided by a plugin, CredentialStorePlugin.

Another important plugin is UserInteractionCallbackPlugin which allows
interacting with the user. It theoretically allows implementing various user
interface mechanisms, although the command line and text-based console
interface provided by its default implementation,
DefaultUserInteractionCallbackPluginImpl, is probably the most appropriate for
Dragom.

ExecContext plugins are available during tool execution and can be used by
tools, jobs and other plugins, including ExecContext plugins. For example,
CredentialStorePlugin can use UserInteractionCallbackPlugin to request a
missing password from the user, and ScmPlugin (a Node plugin) can use
WorkspacePlugin to manage workspace directories to checkout ModuleVersion's
source code in.

Note that Dragom also supports Node plugins which are distinct from ExecContext
plugins. Node plugins provide Node-specific functionality. For a given Node
plugin, for example ScmPlugin, different Node's can have different
implementations. A given Node can even have multiple named implementations of
the same Node plugin. On the other hand, only one implementation of a given
ExecContext plugin can be defined for a given Dragom installation.

{% include links.html %}
