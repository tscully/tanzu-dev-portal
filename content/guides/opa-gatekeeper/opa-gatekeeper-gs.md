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

## What Is OPA?

Open Policy Agent (OPA) is an Open Source Software project which is managed by the Cloud Native Computing Foundation (CNCF).  OPA has reached ‘graduated’ maturity level in the CNCF landscape.
 
OPA is a general purpose policy engine, which allows for unified policy enforcement across platforms and across the software stack.  OPA uses a high-level declarative language that lets you specify policy-as-code and APIs to offload policy decision-making from your software, providing a clear separation of concerns.
 
In OPA a policy is a set of rules that govern the behavior of a software service.  Policies can be used to encode information about how to comply with legal requirements, work within specific technical constraints, and to ensure consistency across releases and across platform instances.
 
There are multiple ways to set up the type of controls that policy can be used to define, but as with security in general, it is useful to be able to apply controls in layers, so that the failure of misconfiguration of one layer does not lead to an enforcement failure.
OPA policies are created in a declarative domain specific language called Rego.   Rego is based on the Datalog language and extends datalog to structured document formats like YAML or JSON..  Rego queries are assertions on data that is stored in OPA. These queries can be used to define policies that enumerate instances of data that violate the expected state of the system, for example a query could check that a particular field in a YAML document has been set to one of a range of acceptable values.
 
Here are some useful resources for learning Rego:
- Rego Documentation: https://www.openpolicyagent.org/docs/latest/policy-language/
- Rego playground to develop and test queries:  https://play.openpolicyagent.org/

## What is Gatekeeper?
 
Gatekeeper is a specialized implementation of OPA that provides integration with Kubernetes using dynamic admission control and custom resource definitions to allow the Kubernetes cluster administrator and other users to create policy templates and specific policy instances, as outlined below.
 
## What is admission control?
 
In Kubernetes, OPA policies are evaluated at the Admission control phase of a request being processed by the Kubernetes API.  Generally requests to the Kubernetes API go through three broad phases before they are accepted or rejected:
 
- **Authentication** - checking the identity of the user or service making the request, for example by presenting a valid token to the API
- **Authorization** - checking that the identity has the required access to perform the request, for example has appropriate role bound
- **Admission Control** - checking that the request passes any built-in validation or any checks implemented by the cluster administrator
 
If the request successfully completes the above stages, the changes specified in the request are applied to the cluster.
 
Policy decisions like those defined in OPA are implemented at the admission control stage, and the OPA project provides an admission controller plugin called Gatekeeper to perform this function in a Kubernetes cluster.
 
Admission control in Kubernetes is dynamically extensible using webhooks, so that once Gatekeeper is deployed and running in a cluster, each request to the Kuberentes API will be validated against an OPA rules that in place, without having to reconfigure the Kubernetes API itself.  This is managed by Gatekeeper registering with the Kuberentes API as a validating and mutuating webhook. 
 
See https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/ for more details on dynamic admission control.

## What is VMware Tanzu Community Edition

VMware Tanzu Community Edition (TCE) is a full-featured, easy-to-manage Kubernetes platform for learners and users, especially those working in small-scale or preproduction environments.

You can learn much more about TCE here:  https://tanzucommunityedition.io/

This guide will use a TCE unmanaged cluster to show OPA Gatekeeper on a cluster running locally on a laptop.

















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



