---
title: What is Dragom?
# keywords:
last_updated: 2017-03-12
# tags: [intro]
summary: "Dragom makes continuous integration, delivery and release engineering fast and efficient for complex multi-module enterprise systems."
sidebar: main_sidebar
permalink: what-is-dragom.html
redirect_from: /index.html
toc: false
# folder: intro
---

{% include warning.html content="This site is under construction. Please come back regularly!" %}

Dragom is a plugin-based, extensible and highly configurable tool development
framework and tool set for managing multi-module reference graphs encountered
in artifact-based development.

It makes working with such reference graphs as easy as working with single
modules.

Dragom also includes tools which operate on multiple modules of a multi-module
reference graph at once:

- Checkout
- Build
- Create branches (development, release, etc.)
- Release
- Merge in main branch (reintegration merge)
- Merge arbitrary reference graphs
- Report
- Interact with build automation tools such as Jenkins

Dragom is developed in Java and mostly targets Java artifact-based development
processes and build tool such as Maven.

Being plugin-based and extensible, Dragom aims to integrate seamlessly with
other custom tools. But its stand-alone tools can also be used as is, or be
integrated through plugins with other existing tools.

Dragom currently integrates through plugins with the following external tools:

- Git
- Maven
- Jenkins

It also supports various strategies for creating branches and releasing
modules, including strategies compatible with continuous integration,
continuous delivery, semantic versioning, uniform date-based versioning and
others, all in the context of multi-module reference graphs.

Other plugins can and will be developed to support other tools and strategies.

Dragom is FOSS (free and open-source software) licensed under AGPL v3.

## Ask yourself these questions...

- Are you working on a Java system composed of a large number of independently
  released modules?
- Are you working on multiple such systems which share various versions of
  common reusable module?
- Is your development community composed of many developers or development
  teams working on the same systems simultaneously?
- Does your development landscape involve multiple simultaneous projects
  related to the same systems?
- Does releasing the multiple modules of your systems involve significant
  efforts?

Sorry for the cliché, but if you answered yes to any of these, please read on.
Dragom may be the missing link you were looking for in your artifact-based
development process!