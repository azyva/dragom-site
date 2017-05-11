---
title: Properties
# keywords:
last_updated: 2017-03-17
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-properties.html
toc: true
# folder: doc
---

Dragom is highly configurable. Plugins and many functionalities can be
configured using different [types](#property-types) of properties which
configurators define at various levels.

The type of property used depends on its meaning and where it is used. The
developer decides the type of a property.

A property has a name and a value. The name of a property identifies it.

The name is a character string. Although internally within Dragom there is no
constraints on property names, they are by convention composed of uppercase
letters, digits and "_" in the base ASCII cbaracter set.

Example property name: `GIT_REPOS_BASE_URL`

The value is also a string of characters whose value domain depends on the
meaning of the property. Property values are therefore less constrained than
the conventions for property names but generally use regular printable
characters in the base ASCII charCter set.

Example property value: `https://github.com`

Property types
--------------

### Model properties

Model properties are defined within the [Model][doc-config-design-model]. Model
properties are associated with
[ClassificationNode's][doc-config-design-classification-node] and
[Module's][doc-config-design-module] within the Model.

Since the Model is generally static, these properties can be considered global
and static as well.

For example the property defining the base remote Git repository URL is a Model
property.

The default Model implementation supports property inheritance. A property
defined for a ClassificationNode applies to child ClassificationNode's or
Module's at any level, unless the property is redefined (with the same name) at
a lower level.

If within the value of a property `$parent$` is used, it is replaced by the
value of the same property evaluated in the context of the parent Node. This is
expected to be rarely used. The only situation it is believed to be useful is
for managing Maven properties where new properties can be introduced by child
Node's. See
[MavenBuilderPluginImpl][doc-config-model-plugin-impls-MavenBuilderPluginImpl]
for more information. 

A property definition can also apply only to the Node on which it is defined in
which case there is no inheritance to lower levels. This also stops inheritance
from a higher level property.

The default Model implementation splits the actual Model and its configuration
so that the Model configuration can be defined using different Model
configuration implementations. Here are some Model property examples for the
XML-based Model configuration:

```xml
<config>
  <root-classification-node>
    <properties>
      <PROP>value1</PROP>
    </properties>
    <child-nodes>
      <classification-node>
        <name>ClassificationNode</name>
        <properties>
          <PROP ind-only-this-node="true">value2</PROP>
        </properties>
        <child-nodes>
          <module>
            <name>module1</name>
          </module>
          <module>
            <name>module2</name>
            <properties>
              <PROP>value3</PROP>
            </properties>
          </module>
        </child-nodes>
      </classification-node>
      <module>
        <name>module3</name>
      </module>
    </child-nodes>
  </root-classification-node>
</config>
```

With the above configuration, property `PROP` has the following value when
evaluated in the following contexts:

Node                        |Value of `PROP`
----------------------------|----------------
root (globally)             |`value1`
`ClassificationNode`        |`value2`
`ClassificationNode/module1`|undefined
`ClassificationNode/module2`|`value3`
`ClassificationNode/module3`|`value1`

Defining or accessing a model property globally is synonym with defining or
accessing it for the root Node.

Model properties are documented [here][doc-config-config-model-properties].

### ExecContext properties {#exec-context-properties}

ExecContext properties are defined within the
[ExecContext][doc-config-design-exec-context] of tools, which is generally
closely related to the workspace. Therefore ExecContext properties can be
customized per workspace. They can also be modified during tool execution.

For example, ExecContext properties are used to persist the
[root ModuleVersion's][doc-config-design-root-module-version]
associated with a workspace. 

ExecContext properties are documented
[here][doc-config-config-exec-context-properties].

### Tool properties

Tool properties are defined when invoking tools. They live for the duration
of a tool execution. Tool properties are static in that they provide a
unidirectional way for the user to communicate configuration data to tools.

For example, the property which defines which ModuleVersion to change and to
which version to map it to would generally be specified using a tool property
since this varies between different tool invocations. 

The tools provided with Dragom support the `-D` command line option which allows
defining tool properties in a way similar to how Java system properties are
defined when invoking java. But tool properties are not Java system properties,
and vice-versa.

The tools provided with Dragom also support defining a file which contain tool
properties.

See [tool bootstrap process][doc-config-design-tool-bootstrap-process] for more
information.

Tool properties are documented [here][doc-config-config-tool-properties].

### Initialization properties {#init-properties}

Initialization properties are similar to Model properties in that they are
generally static and can be considered global. But instead of being defined
within the Model, they are generally configured within the Dragom
installation. Different Dragom distributions (local to developers' workstation
or to be installed on a central build automation server) can define
initialization properties differently.

For example, the property which defines the credential file in which to store
credentials for authenticated access to external systems such as Jenkins or
BitBucket Server would generally be an initialization property.

The default tool initialization bootstrap code recognizes Java system
properties prefixed with `org.azyva.dragom.init-property.` and infers the
corresponding initialization properties. For example, Java system property
`org.azyva.dragom.init-property.PROP` defines the initialization property
`PROP`.

Dragom loads the classpath resource `/META-INF/dragom-init.properties` and
makes properties defined therein initialization properties. This resource is
part of the dragom-cli-tools JAR, but configurators can override it by providing
another version earlier in the classpath. 

The tools provided with Dragom support defining a user properties file which
contain initialization properties. 

The default tool initialization bootstrap code and scripts allows defining
initialization properties in the set-env.sh, dragom-set-env.sh, set-env.cmd or
dragom-set-env.cmd.

See [tool bootstrap process][doc-config-design-tool-bootstrap-process] for more
information.

Initialization properties are documented [here][doc-config-config-init-properties].

### Java system properties {#sys-properties}

Java system properties are not specific to Dragom. Java system properties may
need to be specified within the Dragom installation to control Java-specific
behavior, such as proxies, logging, etc.

Although the other types of properties mentionned above are preferred, Dragom
uses some Java system properties which affect the
[tool bootstrap process][doc-config-design-tool-bootstrap-process] and which
executes before the other types of properties are considered.

Dragom loads the classpath resource `/META-INF/dragom.properties` and makes
properties defined therein Java system properties. This resource is part of the
dragom-cli-tools JAR, but configurators can override it by providing another
version earlier in the classpath.

See the comment about Java system properties prefixed with
`org.azyva.dragom.init-property.` in
[Initialization properties](#init-properties).  

Java system properties are documented [here][doc-config-config-sys-properties].

### Runtime properties

The property types above refer to physical locations where they are defined.
Dragom also supports runtime properties.

Just as for the other property types, a developer can decide to refer to a
property as a runtime property instead of another specific property type.

#### DefaultRuntimePropertiesPluginImpl

The concept of runtime properties is implemented using
[RuntimePropertiesPlugin][doc-config-exec-context-plugins-RuntimePropertiesPlugin]
meaning that the exact nature of runtime properties is arbitrary. Developers
use runtime properties by accessing them using this plugin.

But
[DefaultRuntimePropertiesPluginImpl][doc-config-exec-context-plugin-impls-DefaultRuntimePropertiesPluginImpl]
provides a very useful implementation of this plugin which is expected to be
used in most cases.

This plugin implementation checks for the existence of the property in the
following locations in sequence:

1. [Transient data](doc-config-design-exec-context.html#transient-data)
   stored in the ExecContext
2. [Tool properties](#tool-properties)
3. [ExecContext properties](#exec-context-properties)
4. [Initialization properties](#init-properties)
5. [Model properties](#model-properties)

As soon as the property is found it is returned.

This allows decoupling the decision of a developer to use a certain type of
property from the needs of the configurator in terms of where the property
should be defined. It also allows global properties (Model or initialization)
to be overridden by the user when invoking a tool.

For example, the default behavior of Dragom is to interact with the user by
asking confirmation when important actions are about to be taken, such as
creating a new version. But if Dragom is used in a non-interactive context on a
build automation server, interaction with the user is not possible. Code uses
the value of a runtime property to know if confirmation must be obtained from
the user. This allows the property to be defined as a Model or initialization
property in the case where Dragom is always used in a non-interactive context.
In another scenario, the property could not be defined globally (Model or
initialization) and be defined as a tool property on a build automation server,
and not defined at all for a distribution local to developers' workstation so
that the get the default safer behavior of having user interaction, unless the
user invokes the Dragom tool by defining the tool property.

#### Node inheritance

Runtime properties can be accessed by specifying a
[Node](doc-config-design-model.html#Node). For
DefaultRuntimePropertiesPluginImpl, this is obviously used when falling back
to Model properties which are specifically associated with Node's. But for
other property locations, DefaultRuntimePropertiesPluginImpl implements a
naming hierarchy based on the [NodePath][doc-config-design-node-path].

For example, for the Module `ClassificationNode/module1` and runtime property
`PROP`, the following properties are accessed in turn for each property
location:

1. `ClassificationNode.module1.PROP`
2. `ClassificationNode.PROP`
3. `PROP`

As soon as the property is found it is returned. If it is not found under any
of these names for a given property type, the plugin moves on to the next
property location (ExecContext transient data, tool properties, ExecContext
properties, initialization properties and the Model properties).

It is the code which accesses a runtime properties which decides to access it
in the context of a specific Node, or globally. This is documented for each
runtime property.

When a runtime property is accessed in the context of a specific Node, the
configurator will often decide to define it globally for simplicity and if
appropriate, but can also define it for the Node for which it is desired to
have the property have its effect, or any Node in its parent hierarchy.

When a runtime property is accessed globally, the configurator has no choice
but to define it globally since otherwise it will not be seen.

Defining or accessing a runtime property globally is equivalent to defining or
accessing it for the root Node.

#### Runtime properties are modifiable

Runtime properties can also be modified at runtime.
DefaultRuntimePropertiesPluginImpl stores a modified runtime property in
ExecContext transient data, regardless of from where the property was obtained.
To extend on the example above, when Dragom asks for confirmation from the
user, it allows the user to apply a positive response to either the specific
action being taken, or to all subsequent similar confirmation requests. It does
this by modifying the value of the runtime property so that it remains
available for subsequent requests. And the user can still invoke a tool by
defining the runtime property to disable interaction from the start.

#### Runtime properties are the most used

Most properties used within Dragom are runtime properties because of the
flexibility they offer to the configurator and user. But not all properties can
be runtime properties. The fact that runtime properties can be modified implies
that they should be accessed whenever needed and not cached. But for some
configuration data it is better if they can be considered static and immutable,
such as the URL of a remote Git repository. Also, even if the code does not
modify a runtime property, it is often more appropriate to specifically use
properties defined globally (Model or initialization) in order to support
deploying Dragom as a background service in which global properties are
expected to not change, and use runtime properties for potentially
tool-specific properties. 

Runtime properties are documented [here][doc-config-config-runtime-properties].

[//]: # (TODO: should provide a diagram to help undertand the various property types)

{% include links.html %}
