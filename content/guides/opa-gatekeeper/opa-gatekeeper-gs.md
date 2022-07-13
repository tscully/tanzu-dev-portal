---
title: "Getting Started with Opa Gatekeeper"
description: 
date: "2022-07-13"
lastmod: "2022-07-13"
level1:
level2:
tags:
- 
# Author(s)
team:
-
---

## What Is Opa Gatekeeper?

Open Policy Agent (OPA) is an Open Source Software project which is managed by the Cloud Native Computing Foundation (CNCF).  OPA has reached ‘graduated’ maturity level in the CNCF landscape.
 
OPA is a general purpose policy engine, which allows for unified policy enforcement across platforms and across the software stack.  OPA uses a high-level declarative language that lets you specify policy-as-code and APIs to offload policy decision-making from your software, providing a clear separation of concerns.
 
In OPA a policy is a set of rules that govern the behavior of a software service.  Policies can be used to encode information about how to comply with legal requirements, work within specific technical constraints, and to ensure consistency across releases and across platform instances.
 
There are multiple ways to set up the type of controls that policy can be used to define, but as with security in general, it is useful to be able to apply controls in layers, so that the failure of misconfiguration of one layer does not lead to an enforcement failure.
OPA policies are created in a declarative domain specific language called Rego.   Rego is based on the Datalog language and extends datalog to structured document formats like YAML or JSON..  Rego queries are assertions on data that is stored in OPA. These queries can be used to define policies that enumerate instances of data that violate the expected state of the system, for example a query could check that a particular field in a YAML document has been set to one of a range of acceptable values.
 
Here are some useful resources for learning Rego:
- Rego Documentation: https://www.openpolicyagent.org/docs/latest/policy-language/
- Rego playground to develop and test queries:  https://play.openpolicyagent.org/

...

## Before You Begin

There are a few things you need to do before getting started with Opa Gatekeeper:

- Step 1

- Step 2

## Using Opa Gatekeeper

#### Detail 1

...

```
code sample
```

#### Detail 2

...

```
code sample
```
