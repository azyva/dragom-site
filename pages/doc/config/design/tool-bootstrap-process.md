---
title: Tool bootstrap process
# keywords:
last_updated: 2017-05-10
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-tool-bootstrap-process.html
toc: true
# folder: doc
---

Dragom tools, jobs and plugins execute in a context called ExecContext which
provides everything they need to operate. But that ExecContext has to be
created and properly initialized.

Any code which integrates with Dragom and invokes Dragom tasks is required to
provide an ExecContext beforehand. This includes Dragom's own tools.

All Dragom tools share the same ExecContext bootstrapping logic which is
described here chronologically.

{% include note.html content="Dragom is highly configurable with various types of properties and parameters and the ExecContext bootstrapping logic takes care of setting up many of those properties and parameters. It may be helpful at this point to review the section about properties." %}


1\. Pre-JVM start
-----------------

As for most Java applications, Java system properties (recognized by Dragom
code or by code external to Dragom, such as the Java libraries) are important
to Dragom.

System properties are defined when invoking the JVM using the `-D` command line
option. But like for most Java tools, Dragom provides a script to make
starting and passing arguments to a Dragom tool easier. This script is by
default simply called `dragom` (`dragom.cmd` on Windows) but can be renamed
since Dragom itself does not rely on that script.

This script is not meant to be customized (other than renaming it), but it
sources another script `set-env.sh` or `dragom-set-env.sh` (`set-env.cmd` or
`dragom-set-env.cmd` on Windows) whichever is available in the same directory
as the script. That sourced script is meant to be customized by defining shell
or environment variables recognized by the main script to in turn define system
properties and other JVM options. See the comments in those scripts for more
information.

Once the JVM is started, the ExecContext bootstrapping logic is performed by
Java code.


2\. System properties
---------------------

Dragom reads the properties defined in the `META-INF/dragom.properties` and
`META-INF/dragom-override.properties` classpath resources and transfers them as
system properties. The properties in the latter resource have precedence.

A default
[`META-INF/dragom.properties`](https://github.com/azyva/dragom-cli-tools/blob/master/dragom-cli-tools/src/main/resources/META-INF/dragom.properties)
is provided by Dragom, while `META-INF/dragom-override.properties` is intended
to be optionally provided by a user-customized distribution package.

These resources allow configuring fixed system properties within a
distribution. Note that already defined system properties are not overridden
since this would prevent the useful overriding provided by the `-D` command
line option to the JVM. This also implies that system properties predefined
by the JVM cannot be overridden (e.g.: `file.encoding`).


3\. Initialization properties from classpath resources
------------------------------------------------------

Dragom loads the properties defined in the `META-INF/dragom-init.properties`
and `META-INF/dragom-init-override.properties` classpath resources and
uses them as Dragom initialization properties. The properties in the latter
resource have precedence.

A default
[`META-INF/dragom-init.properties`](https://github.com/azyva/dragom-cli-tools/blob/master/dragom-cli-tools/src/main/resources/META-INF/dragom-init.properties)
is provided by Dragom, while `META-INF/dragom-init-override.properties` is
intended to be optionally provided by a user-customized distribution package.


4\. Initialization properties from a user properties file
---------------------------------------------------------

If the system property `org.azyva.dragom.IndUserProperties` is defined as true
(which it is by default from `META-INF/dragom.properties`), Dragom handles a
user properties file. If not, this step is skipped.

If the user specifies the `--user-properties` command line option, the
specified file is used. Otherwise, the default user properties file defined by
the system property `org.azyva.dragom.DefaultUserProperties` (set to
`~/.dragom/dragom.properties` by default from `META-INF/dragom.properties`) is
used, in which "~" is replaced by the user's home directory.

The properties defined in that file are loaded as initialization properties,
overriding initialization properties previously loaded.

{% include note.html content="The name of the <code>--user-properties</code> command line option can be changed with the <code>org.azyva.dragom.UserPropertiesCommandLineOption</code> system property which would generally be defined in <code>META-INF/dragom-override.properties</code>." %}


5\. Initialization properties from system properties
----------------------------------------------------

System properties having the `org.azyva.dragom.init-property.` prefix are added
as initialization properties, overriding initialization properties previously
loaded.

This step is to allow defining initialization properties as `-D` command line
options when staring the JVM, which override initialization properties defined
elsewhere.

{% include warning.html content="System properties loaded from <code>META-INF/dragom.properties</code> and <code>META-INF/dragom-override.properties</code> and having that prefix are recognized as initialization properties. But defining initialization properties this way is not recommended since their precedence over initialization properties defined elsewhere such as in a user properties file would generally not be desirable." %}


6\. Workspace directory
-----------------------

If the user specifies the `--workspace` command line option, the specified
directory is defined as the initialization property `WORKSPACE_PATH`.
Otherwise, no initialization property is defined.

{% include note.html content="The name of the <code>--workspace</code> command line option can be changed with the <code>org.azyva.dragom.WorkspacePathCommandLineOption</code> system property which would generally be defined in <code>META-INF/dragom-override.properties</code>." %}


4\. ExecContext instantiation
-----------------------------

The ExecContext is initialized with the initialization properties.

An ExecContext must be associated with a workspace. If the `WORKSPACE_PATH`
initialization property is defined, it is used. Otherwise, the current
directory is used as the workspace.

When the ExecContext is initialized, it in turn initializes the Model with the
same initialization properties.

{% include note.html content="The actual ExecContext instantiation details are related to the ExecContext factory used (see below). What is described above assumes <code>DefaultExecContextFactory</code>." %}


5\. Tool properties from tool properties file
---------------------------------------------

If the system property `org.azyva.dragom.IndToolProperties` is defined as true
(which it is by default from `META-INF/dragom.properties`), Dragom handles a
tool properties file. If not, this step is skipped.

If the user specifies the `--tool-properties` command line option, the
properties in that file are loaded as tool properties, set within the
ExecCopntext and made available during the execution of the tool.

{% include note.html content="The name of the <code>--tool-properties</code> command line option can be changed with the <code>org.azyva.dragom.ToolPropertiesCommandLineOption</code> system property which would generally be defined in <code>META-INF/dragom-override.properties</code>." %}


6\. Tool properties from `-D` command line options
---------------------------------------------------

If the system property `org.azyva.dragom.IndSingleToolProperties` is defined as
true (which it is by default from `META-INF/dragom.properties`), Dragom handles
single tool properties defined with the `-D` command line option.

{% include note.html content="We are talking about Dragom's <code>-D</code> options here, not the JVM's." %}

The properties defined with `-D` command line options override tool properties
previously defined.


7\. "No confirmation" tool properties
-------------------------------------

Dragom tools provide a certain level of interactivity, prompting the user when
required.

A recurring type of prompt is one which asks the user if he wants to continue
before performing a significant action such as creating a new Version.

Dragom allows the user to disable these prompts if required.

If the user specifies the `--no-confirm` command line option, the runtime
property `IND_NO_CONFIRM` is set to true as a tool property, therefore
disabling all such prompts.

Otherwise, for any `--no-confirm-context` command line option specified by the
user, the runtime property `IND_NO_CONFIRM.<context>` is set to true as a tool
property. `<context>` is the value specified for the `--no-confirm-context`
command line option. This disables only the prompts corresponding to the
specified context. Contexts here represent what the user is prompted for (e.g.:
creating a new Version vs merging a Version into another).

{% include note.html content="The name of the <code>--no-confirm</code> and <code>--no-confirm-context</code> command line options can be changed with the <code>org.azyva.dragom.NoConfirmCommandLineOption</code> and <code>org.azyva.dragom.NoConfirmContextCommandLineOption</code> system property which would generally be defined in <code>META-INF/dragom-override.properties</code>." %}

ExecContext and Model factories
-------------------------------

The ExecContext instantiation step above uses the factory design pattern to
support various ExecContext implementations.

The ExecContext bootstrapping logic used by Dragom uses the class
`org.azyva.dragom.execcontext.support.ExecContextFactoryHolder` to manage the
factory pattern for the ExecContext. This class uses the system property
`org.azyva.dragom.DefaultExecContextFactory` to know which ExecContext factory.
If this property is not defined,
`org.azyva.dragom.execcontext.support.DefaultExecContextFactory` is used.

`DefaultExecContextFactory` provides a versatile ExecContext implementation
with the following features:

- It supports the concept of workspace directory;
- The workspace directory is identified by the initialization property
  `WORKSPACE_PATH` or the current directory is used if not defined;
- As an optimization, ExecContext's are cached and keyed by their workspace in
  case multiple Dragom tools invocations are done in the same JVM instance;
- Workspace metadata, such as persistent properties and the registry of
  workspace subdirectories, is kept in the .dragom subdirectory;
- It uses the class `org.azyva.dragom.model.support.ModelFactoryHolder`
  to manage the factory pattern for the Model.
 
`ModelFactoryHolder` uses the system property
`org.azyva.dragom.DefaultModelFactory` to know which Model factory to use. If
this property is not defined,
`org.azyva.dragom.model.support.DefaultModelFactory` is used.

`DefaultModelFactory` provides a Model implementation with the following
features:

- The Model is loaded from an XML resource;
- The XML resource is identified by the initialization property `URL_MODEL`;
- This property is generally an URL (e.g.: `http://...`), but as a convenience
  can also be a regular file path (no `file://` protocol prefix); 
- The XML resource can be cached locally to avoid a time-consuming download
  at each tool invocation;
- As an optimization, Model's are cached and keyed by their URL in case
  multiple Dragom tools invocations are done in the same JVM instance with
  different ExecContext's (different workspaces), but the same Model;
- Model's are cached and keyed by their URL;

All this implies that the exact ExecContext and Model initialization processes
actually depend on which factory class is used.

{% include links.html %}
