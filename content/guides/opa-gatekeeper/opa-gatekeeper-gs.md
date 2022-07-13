---
title: "Getting Started with OPA Gatekeeper"
linkTitle:  "Getting Started with OPA Gatekeeper"
description: "An introduction to the Open Policy Agent on Kubernetes"
date: "2022-07-13"
lastmod: "2022-07-13"
level1:
level2:
tags:
- Kuberenetes
- Security
# Author(s)
team:
- Tony Scully
- Tiffany Jernigan
topics:
- Kubernetes
---

## What Is OPA?

Open Policy Agent (OPA) is an Open Source Software project which is managed by the Cloud Native Computing Foundation (CNCF).  OPA has reached ‘graduated’ maturity level in the CNCF landscape.
 
OPA is a general purpose policy engine, which allows for unified policy enforcement across platforms and across the software stack.  OPA uses a high-level declarative language that lets you specify policy-as-code and APIs to offload policy decision-making from your software, providing a clear separation of concerns.
 
In OPA a policy is a set of rules that govern the behaviour of a software service.  Policies can be used to encode information about how to comply with legal requirements, work within specific technical constraints, and to ensure consistency across releases and across platform instances.
 
There are multiple ways to set up the type of controls that policy can be used to define, but as with security in general, it is useful to be able to apply controls in layers, so that the failure of misconfiguration of one layer does not lead to an enforcement failure.

OPA policies are created in a declarative domain specific language called Rego. Rego is a declarative language for defining queries, so you can focus on the value returned by the queries rather tna on how the query is executed.

Rego is based on the Datalog language and extends datalog to structured document formats like YAML or JSON..  Rego queries are assertions on data that is stored in OPA. These queries can be used to define policies that enumerate instances of data that violate the expected state of the system, for example a query could check that a particular field in a YAML document has been set to one of a range of acceptable values.
 
Here are some useful resources for learning and understanding Rego:
- Rego Documentation: https://www.openpolicyagent.org/docs/latest/policy-language/
- Rego playground to develop and test queries:  https://play.openpolicyagent.org/
- Testing locally using the `conftest` tool:  https://github.com/open-policy-agent/conftest
- Developing policies:  https://tanzu.vmware.com/developer/guides/platform-security-opa/
## What is Gatekeeper?
 
Gatekeeper is a specialized implementation of OPA that provides integration with Kubernetes using dynamic admission control and custom resource definitions to allow the Kubernetes cluster administrator and other users to create policy templates and specific policy instances, as outlined below.
 
## What is admission control?
 
In Kubernetes, OPA policies are evaluated at the Admission control phase of a request being processed by the Kubernetes API.  Generally requests to the Kubernetes API go through three broad phases before they are accepted or rejected:
 
- **Authentication** - checking the identity of the user or service making the request, for example by presenting a valid token to the API
- **Authorization** - checking that the identity has the required access to perform the request, for example has appropriate role bound
- **Admission Control** - checking that the request passes any built-in validation or any checks implemented by the cluster administrator
 
If the request successfully completes the above stages, the changes specified in the request are applied to the cluster.
 
Policy decisions like those defined in OPA are implemented at the admission control stage, and the OPA project provides an admission controller plugin called Gatekeeper to perform this function in a Kubernetes cluster.
 
Admission control in Kubernetes is dynamically extensible using webhooks, so that once Gatekeeper is deployed and running in a cluster, each request to the Kuberentes API will be validated against an OPA rules that in place, without having to reconfigure the Kubernetes API itself.  This is managed by Gatekeeper registering with the Kuberentes API as a validating and mutating webhook. 
 
See https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/ for more details on dynamic admission control.

## What is VMware Tanzu Community Edition

VMware Tanzu Community Edition (TCE) is a full-featured, easy-to-manage Kubernetes platform for learners and users, especially those working in small-scale or preproduction environments.

You can learn much more about TCE here:  https://tanzucommunityedition.io/

This guide will use a TCE unmanaged cluster to show OPA Gatekeeper on a cluster running locally on a laptop.  You can also deploy TCE on public cloud infrastructure or in an on-premises environment.

OPA Gatekeeper can be deployed on any Kuberentes cluster.  TCE clusters and other Tanzu Kubernetes clusters use an additional controller to help with deploying software, and introduce the concept of a package to help with managing the lifecycle of software.  You can read more about packages here:  https://tanzucommunityedition.io/packages/


## Using OPA Gatekeeper


## Set up a TCE unmanaged cluster

Complete prerequisites and setup for TCE on your preferred operating system can be found here:  https://tanzucommunityedition.io/docs/v0.12/installation-planning/

Once your environment is setup, you can run the following steps:


### Deploy an unmanaged cluster

```
tanzu um create opa-guide
```

### List available packages
```
tanzu package available list
```

The output will be similar to the following:

```
 NAME                                                    DISPLAY-NAME                 SHORT-DESCRIPTION                                                                                                                                          LATEST-VERSION
  app-toolkit.community.tanzu.vmware.com                  App-Toolkit package for TCE  Kubernetes-native toolkit to support application lifecycle                                                                                                 0.2.0
  cartographer-catalog.community.tanzu.vmware.com         Cartographer Catalog         Reusable Cartographer blueprints                                                                                                                           0.3.0
  cartographer.community.tanzu.vmware.com                 Cartographer                 Kubernetes native Supply Chain Choreographer.                                                                                                              0.3.0
  cert-injection-webhook.community.tanzu.vmware.com       cert-injection-webhook       The Cert Injection Webhook injects CA certificates and proxy environment variables into pods                                                               0.1.1
  cert-manager.community.tanzu.vmware.com                 cert-manager                 Certificate management                                                                                                                                     1.8.0
  contour.community.tanzu.vmware.com                      contour                      An ingress controller                                                                                                                                      1.20.1
  external-dns.community.tanzu.vmware.com                 external-dns                 This package provides DNS synchronization functionality.                                                                                                   0.10.0
  fluent-bit.community.tanzu.vmware.com                   fluent-bit                   Fluent Bit is a fast Log Processor and Forwarder                                                                                                           1.7.5
  fluxcd-source-controller.community.tanzu.vmware.com     Flux Source Controller       The source-controller is a Kubernetes operator, specialised in artifacts acquisition from external sources such as Git, Helm repositories and S3 buckets.  0.21.5
  gatekeeper.community.tanzu.vmware.com                   gatekeeper                   policy management                                                                                                                                          3.7.1
  grafana.community.tanzu.vmware.com                      grafana                      Visualization and analytics software                                                                                                                       7.5.11
  harbor.community.tanzu.vmware.com                       harbor                       OCI Registry                                                                                                                                               2.4.2
  helm-controller.fluxcd.community.tanzu.vmware.com       Flux Helm Controller         The Helm Controller is a Kubernetes operator, allowing one to declaratively manage Helm chart releases with Kubernetes manifests.                          0.17.2
  knative-serving.community.tanzu.vmware.com              knative-serving              Knative Serving builds on Kubernetes to support deploying and serving of applications and functions as serverless containers                               1.0.0
  kpack-dependencies.community.tanzu.vmware.com           kpack dependencies           Dependencies in the form of Buildpacks and Stacks for the kpack package                                                                                    0.0.27
  kpack.community.tanzu.vmware.com                        kpack                        kpack builds application source code into OCI compliant images using Cloud Native Buildpacks                                                               0.5.3
  kustomize-controller.fluxcd.community.tanzu.vmware.com  Flux Kustomize Controller    Kustomize controller is one of the components in GitOps toolkit.                                                                                           0.21.1
  local-path-storage.community.tanzu.vmware.com           local-path-storage           This package provides local path node storage and primarily supports RWO AccessMode.                                                                       0.0.22
  multus-cni.community.tanzu.vmware.com                   multus-cni                   This package provides the ability for enabling attaching multiple network interfaces to pods in Kubernetes                                                 3.8.0
  prometheus.community.tanzu.vmware.com                   prometheus                   A time series database for your metrics                                                                                                                    2.27.0-1
  velero.community.tanzu.vmware.com                       velero                       Disaster recovery capabilities                                                                                                                             1.8.0
  whereabouts.community.tanzu.vmware.com                  whereabouts                  A CNI IPAM plugin that assigns IP addresses cluster-wide                                                                                                   0.5.1
(⎈ |kind-opa-guide:default)➜ 
```

### Install the OPA Gatekeeper package. 

The `install` verb requires the name and version of the package as shown here:

```
tanzu package install gatekeeper --package-name gatekeeper.community.tanzu.vmware.com --version 3.7.1
```

### Check the status of the install

```
tanzu package installed list
```
The output should be similar to:

```
NAME        PACKAGE-NAME                           PACKAGE-VERSION  STATUS
gatekeeper  gatekeeper.community.tanzu.vmware.com  3.7.1            Reconcile succeeded
```


## Using Gatekeeper

Now that Gatekeeper has been installed, there are some new components running in the cluster.  You can examine the content of the ```gatekeeper-system``` namespace:

```
(⎈ |kind-opa-guide:default)➜  ~ kubectl -n gatekeeper-system get deployments,services
NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/gatekeeper-audit                1/1     1            1           65m
deployment.apps/gatekeeper-controller-manager   1/1     1            1           65m

NAME                                 TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/gatekeeper-webhook-service   ClusterIP   10.96.212.9   <none>        443/TCP   65m
```

And also see these new customer resource definitions that have been created in the cluster:
```
(⎈ |kind-opa-guide:default)➜  ~ kubectl get crd -l gatekeeper.sh/system=yes
NAME                                                 CREATED AT
assign.mutations.gatekeeper.sh                       2022-07-13T10:16:20Z
assignmetadata.mutations.gatekeeper.sh               2022-07-13T10:16:21Z
configs.config.gatekeeper.sh                         2022-07-13T10:16:21Z
constraintpodstatuses.status.gatekeeper.sh           2022-07-13T10:16:21Z
constrainttemplatepodstatuses.status.gatekeeper.sh   2022-07-13T10:16:20Z
constrainttemplates.templates.gatekeeper.sh          2022-07-13T10:16:20Z
modifyset.mutations.gatekeeper.sh                    2022-07-13T10:16:20Z
mutatorpodstatuses.status.gatekeeper.sh              2022-07-13T10:16:20Z
providers.externaldata.gatekeeper.sh                 2022-07-13T10:16:21Z
```

Using the `constrainttemplates.templates.gatekeeper.sh` CRD you can create general policy definitions for OPA that Gatekeeper will use.

Once you have created a template, you can create specific constraint instances for each use-case you want to define. 

### Creating a constriant template

As an example of constraint templates, the cluster admin can create a template to require labels on objects.

This template defines a general constraint that checks for the existence of labels.  Note that there are no specific cases (label names or object types) defined in the template.

Once created, the template can be used to create constraints that require a specific label or set of labels to be defined on an object.

You can see the general structure of the template in the YAML below.  The spec contains two main fields; `crd` and `targets`.  The `targets` contains the rego definition of the general constraint you want to use.   You can find more information in the Rego documentation or you can use `kubectl explain` to explore the structure of the YAML used to define the templates.




In the ‘targets’ section, you can see who the template refers to the Kuberentes specification of the resources to be reviewed by the policy, in this case `object.metadata.labels`.  
You can define the message that you want to be reported if the contraint is violated in the template.

```
-
apiVersion: templates.gatekeeper.sh/v1
kind: ConstraintTemplate
metadata:
  name: k8srequiredlabels
  annotations:
    description: >-
      Requires resources to contain specified labels, with values matching
      provided regular expressions.
spec:
  crd:
    spec:
      names:
        kind: K8sRequiredLabels
      validation:
        openAPIV3Schema:
          type: object
          properties:
            message:
              type: string
            labels:
              type: array
              description: >-
                A list of labels and values the object must specify.
              items:
                type: object
                properties:
                  key:
                    type: string
                    description: >-
                      The required label.
                  allowedRegex:
                    type: string
                    description: >-
                      If specified, a regular expression the annotation's value
                      must match. The value must contain at least one match for
                      the regular expression.
  targets:
    - target: admission.k8s.gatekeeper.sh
      rego: |
        package k8srequiredlabels

        get_message(parameters, _default) = msg {
          not parameters.message
          msg := _default
        }

        get_message(parameters, _default) = msg {
          msg := parameters.message
        }

        violation[{"msg": msg, "details": {"missing_labels": missing}}] {
          provided := {label | input.review.object.metadata.labels[label]}
          required := {label | label := input.parameters.labels[_].key}
          missing := required - provided
          count(missing) > 0
          def_msg := sprintf("you must provide labels: %v", [missing])
          msg := get_message(input.parameters, def_msg)
        }

        violation[{"msg": msg}] {
          value := input.review.object.metadata.labels[key]
          expected := input.parameters.labels[_]
          expected.key == key
          # do not match if allowedRegex is not defined, or is an empty string
          expected.allowedRegex != ""
          not re_match(expected.allowedRegex, value)
          def_msg := sprintf("Label <%v: %v> does not satisfy allowed regex: %v", [key, value, expected.allowedRegex])
          msg := get_message(input.parameters, def_msg)
        }

```

Applying the YAML above to the cluster created earlier:

```
(⎈ |kind-opa-guide:default)➜  ~ kubectl apply -f example-contraint-template.yaml
constrainttemplate.templates.gatekeeper.sh/k8srequiredlabels created
```

And we can see the resource that has been created:

```
(⎈ |kind-opa-guide:default)➜  ~ kubectl get constrainttemplates
NAME                AGE
k8srequiredlabels   64s
```

Next, you need to create a specific contraint to be applied.

In this case, the contraint defines that any `namespace` objects that are created must have a value set for the `owner` label.  The YAML is should below:

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sRequiredLabels
metadata:
  name: all-ns-must-have-owner-label
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Namespace"]
  parameters:
    message: "All namespaces must have an `owner` label"
    labels:
      - key: owner
```

When this is applied, we can see a sepcific instance of the contraint template `K8sRequiredLabels` is created:

```
(⎈ |kind-opa-guide:default)➜  ~ kubectl apply -f example-constraint-instance.yaml
k8srequiredlabels.constraints.gatekeeper.sh/all-ns-must-have-owner-label created
```

```
(⎈ |kind-opa-guide:default)➜  ~ kubectl get constraints
NAME                           AGE
all-ns-must-have-owner-label   35s
```

## Testing the constraint

Here is the minimal YAML to create a namespace:

```
(⎈ |kind-opa-guide:default)➜  ~ cat test-opa.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: test-opa
spec: {}
```

If you now apply this to the cluster:

```
(⎈ |kind-opa-guide:default)➜  ~ kubectl apply -f test-opa.yaml
Error from server ([all-ns-must-have-owner-label] All namespaces must have an `owner` label): error when creating "test-opa.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [all-ns-must-have-owner-label] All namespaces must have an `owner` label
```

You can see the contraint created earlier is violated, and creation of the object is denied.

Modifying the YAML as shown here and attempting to create with the label added:

```
apiVersion: v1
kind: Namespace
metadata:
  name: test-opa
  labels:
    owner: opa-tester
spec: {}
```

```
(⎈ |kind-opa-guide:default)➜  ~ kubectl apply -f test-opa.yaml
namespace/test-opa created

(⎈ |kind-opa-guide:default)➜  ~ kubectl get ns test-opa
NAME       STATUS   AGE
test-opa   Active   17s
```

## Adding another constraint using the same template

Once a constraint template exists, it can be used for multiple use-cases.  

For example, this constraint would check that a label named `stage` is set on any pod spec that is sent to the Kubernetes API:

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sRequiredLabels
metadata:
  name: all-pods-must-have-stage-label
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
  parameters:
    message: "All pods must have a `stage` label"
    labels:
      - key: stage
```

## Checking the status of constraints

One thing to note is that once a constraint has been put in place, using `kubectl describe` will show violation so the constraint by objects that already ecist in the cluster.

The existing objects will not be affected, for example a running pod that violates a constraint will not be evicted, but updates to those objects will fail.  For example in the cluster deployed earlier:

```
(⎈ |kind-opa-guide:default)➜  kubectl describe  constraint all-ns-must-have-owner-label
Name:         all-ns-must-have-owner-label
Namespace:
Labels:       <none>
Annotations:  <none>
API Version:  constraints.gatekeeper.sh/v1beta1
Kind:         K8sRequiredLabels
Metadata:
  Creation Timestamp:  2022-07-13T12:14:32Z
  Generation:          1
  Managed Fields:
    API Version:  constraints.gatekeeper.sh/v1beta1
    Fields Type:  FieldsV1
    fieldsV1:
      f:status:
    Manager:      gatekeeper
    Operation:    Update
    Subresource:  status
    Time:         2022-07-13T12:14:32Z
    API Version:  constraints.gatekeeper.sh/v1beta1
    Fields Type:  FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .:
          f:kubectl.kubernetes.io/last-applied-configuration:
      f:spec:
        .:
        f:match:
          .:
          f:kinds:
        f:parameters:
          .:
          f:labels:
          f:message:
    Manager:         kubectl-client-side-apply
    Operation:       Update
    Time:            2022-07-13T12:14:32Z
  Resource Version:  28190
  UID:               e1584a76-2a12-43c1-a9a6-dfa677c62de5
Spec:
  Match:
    Kinds:
      API Groups:

      Kinds:
        Namespace
  Parameters:
    Labels:
      Key:    owner
    Message:  All namespaces must have an `owner` label
Status:
  Audit Timestamp:  2022-07-13T14:44:37Z
  By Pod:
    Constraint UID:       e1584a76-2a12-43c1-a9a6-dfa677c62de5
    Enforced:             true
    Id:                   gatekeeper-audit-6d9c86d658-47krm
    Observed Generation:  1
    Operations:
      audit
      status
    Constraint UID:       e1584a76-2a12-43c1-a9a6-dfa677c62de5
    Enforced:             true
    Id:                   gatekeeper-controller-manager-7f66c8bb65-65v89
    Observed Generation:  1
    Operations:
      mutation-webhook
      webhook
  Total Violations:  8
  Violations:
    Enforcement Action:  deny
    Kind:                Namespace
    Message:             All namespaces must have an `owner` label
    Name:                tkg-system
    Enforcement Action:  deny
    Kind:                Namespace
    Message:             All namespaces must have an `owner` label
    Name:                gatekeeper-system
    Enforcement Action:  deny
    Kind:                Namespace
    Message:             All namespaces must have an `owner` label
    Name:                tanzu-package-repo-global
    Enforcement Action:  deny
    Kind:                Namespace
    Message:             All namespaces must have an `owner` label
    Name:                local-path-storage
    Enforcement Action:  deny
    Kind:                Namespace
    Message:             All namespaces must have an `owner` label
    Name:                kube-public
    Enforcement Action:  deny
    Kind:                Namespace
    Message:             All namespaces must have an `owner` label
    Name:                kube-system
    Enforcement Action:  deny
    Kind:                Namespace
    Message:             All namespaces must have an `owner` label
    Name:                default
    Enforcement Action:  deny
    Kind:                Namespace
    Message:             All namespaces must have an `owner` label
    Name:                kube-node-lease
Events:                  <none>

```

You can see in the violations section above, that a number of exisitng namespaces do not have the owner label set, as required by the constriant.

If you attempt to update the namespace the update will fail until the constraint is met.

```
(⎈ |kind-opa-guide:default)➜  policy k edit namespace default
error: namespaces "default" could not be patched: admission webhook "validation.gatekeeper.sh" denied the request: [all-ns-must-have-owner-label] All namespaces must have an `owner` label
```




