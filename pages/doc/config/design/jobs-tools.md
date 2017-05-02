---
title: Jobs
# keywords:
last_updated: 2017-05-01
# tags: [doc]
# summary: ""
sidebar: doc_config_sidebar
permalink: doc-config-design-jobs-tools.html
toc: false
# folder: doc
---

Jobs
----

A Dragom job is the basic work unit. Jobs are designed as building blocks for
developing various functionality based on ReferenceGraph's.

Tools
-----

Tools are command line entry points (Java classes with a `main` method) which
the user can invoke.

Tools can use jobs to perform work, but not all tools do. Also, many tools are
simply command line wrappers around a single job, which can sometimes blur the
distinction between tools and jobs.


Distinction between jobs and tools
----------------------------------

Jobs and tools may look similar from a user's point of view, but are quite
different. Jobs perform work and tools are command line entry points (Java
classes with a `main` method) which the user can invoke. Most tools internally
use and invoke jobs to perform useful work.

The fact that many tools are simply command line wrappers around a single job
contributes to the confusion between jobs and tools. But understanding that a
tool can use multiple jobs to perform its work, or use a job for only part of
the work helps to avoid the confusion.

For example, the `reference-graph-report` tool (Java class
`org.azyva.dragom.tool.ReferenceGraphReport` which has a main method) uses the
`BuildReferenceGraph` job (Java class
`org.azyva.dragom.job.BuildReferenceGraph`) to internally build the
ReferenceGraph and thereafter traverse the
ReferenceGraph to produce the report.

Jobs are more a Dragom development concept while tools are more a Dragom end
user concept.

{% include links.html %}

[//]: # (common job design patters, root modules versions, reference path matchers, command line, etc.)
