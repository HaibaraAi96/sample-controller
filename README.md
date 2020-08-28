# sample-controller

This repository implements a simple controller for watching Icecream resources as
defined with a CustomResourceDefinition (CRD).

**Note:** go-get or vendor this package as `k8s.io/sample-controller`.

This particular example demonstrates how to perform basic operations such as:

* How to register a new custom resource (custom resource type) of type `Icecream` using a CustomResourceDefinition.
* How to create/get/list instances of your new resource type `Icecream`.
* How to setup a controller on resource handling create/update/delete events.

It makes use of the generators in [k8s.io/code-generator](https://github.com/kubernetes/code-generator)
to generate a typed client, informers, listers and deep-copy functions. 

By using the `./hack/update-codegen.sh` script, we automatically generate the following files &
directories:

* `pkg/apis/samplecontroller/v1alpha1/zz_generated.deepcopy.go`
* `pkg/generated/`

Changes should not be made to these files manually, and when creating your own
controller based off of this implementation you should not copy these files and
instead run the `update-codegen` script to generate your own.

## Details



## Fetch sample-controller and its dependencies

### When using go 1.11 modules

When using go 1.11 modules (`GO111MODULE=on`), issue the following
commands --- starting in whatever working directory you like.

```sh
git clone https://github.com/kubernetes/sample-controller.git
cd sample-controller
```

- You also need to clone the **code-generator repo** to exist in $GOPATH/k8s.io/  
- An alternative way to do this is to use the command `go mod vendor` to create and populate the `vendor` directory


## Purpose

This is an example of how to build a kube-like controller with a single type.

## Running

**Prerequisite**: Since the sample-controller uses `apps/v1` deployments, the Kubernetes cluster version should be greater than 1.9.

```sh
# assumes you have a working kubeconfig, not required if operating in-cluster
go build -o sample-controller .
./sample-controller -kubeconfig=$HOME/.kube/config

# create a CustomResourceDefinition
kubectl create -f artifacts/examples/crd.yaml

# create a custom resource of type Foo
kubectl create -f artifacts/examples/example-foo.yaml

# check deployments created through the custom resource
kubectl get deployments
```

## Use Cases

CustomResourceDefinitions can be used to implement custom resource types for your Kubernetes cluster.
These act like most other Resources in Kubernetes, and may be `kubectl apply`'d, etc.

Some example use cases:

* Provisioning/Management of external datastores/databases (eg. CloudSQL/RDS instances)
* Higher level abstractions around Kubernetes primitives (eg. a single Resource to define an etcd cluster, backed by a Service and a ReplicationController)

## Defining types

Each instance of your custom resource has an attached Spec, which should be defined via a `struct{}` to provide data format validation.
In practice, this Spec is arbitrary key-value data that specifies the configuration/behavior of your Resource.

For example, if you were implementing a custom resource for an Icecream, you might provide a IcecreamSpec like the following:

``` go
// Icecream is a specification for a Icecream resource
type Icecream struct {
	metav1.TypeMeta   `json:",inline"`
	metav1.ObjectMeta `json:"metadata,omitempty"`

	Spec   IcecreamSpec   `json:"spec"`
	Status IcecreamStatus `json:"status"`
}

// IcecreamSpec is the spec for an Icecream resource
type IcecreamSpec struct {
	DeploymentName string `json:"deploymentName"`
	Replicas       *int32 `json:"replicas"`
}
```


## Cleanup

You can clean up the created CustomResourceDefinition with:

    kubectl delete crd icecreams.controller.nicoleh.io

## Compatibility

HEAD of this repository will match HEAD of k8s.io/apimachinery and
k8s.io/client-go.

## Where does it come from?

`sample-controller` is cloned from
https://github.com/kubernetes/kubernetes/blob/master/staging/src/k8s.io/sample-controller.
