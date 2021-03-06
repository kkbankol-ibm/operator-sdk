---
title: Quickstart for Ansible-based Operators
linkTitle: Quickstart
weight: 2
description: A simple set of instructions to set up and run an Ansible-based operator.
---

This guide walks through an example of building a simple memcached-operator powered by [Ansible][ansible-link] using tools and libraries provided by the Operator SDK.

## Prerequisites

- Go through the [installation guide][install-guide].
- User authorized with `cluster-admin` permissions.

## Steps

1. Create a project directory for your project and initialize the project:

  ```sh
  mkdir memcached-operator
  cd memcached-operator
  operator-sdk init --domain example.com --plugins ansible
  ```

1. Create a simple Memcached API:

  ```sh
  operator-sdk create api --group cache --version v1alpha1 --kind Memcached --generate-role
  ```

1. Use the built-in Makefile targets to build and push your operator.
Make sure to define `IMG` when you call `make`:

  ```sh
  export OPERATOR_IMG="quay.io/example/memcached-operator:v0.0.1"
  make docker-build docker-push IMG=$OPERATOR_IMG
  ```


### OLM deployment

1. Install [OLM][doc-olm]:

  ```sh
  operator-sdk olm install
  ```

1. Bundle your operator and push the bundle image:

  ```sh
  make bundle IMG=$OPERATOR_IMG
  # Note the "-bundle" component in the image name below.
  export BUNDLE_IMG="quay.io/example/memcached-operator-bundle:v0.0.1"
  make bundle-build BUNDLE_IMG=$BUNDLE_IMG
  make docker-push IMG=$BUNDLE_IMG
  ```

1. Run your bundle:

  ```sh
  operator-sdk run bundle $BUNDLE_IMG
  ```

1. Create a sample Memcached custom resource:

  ```console
  $ kubectl apply -f config/samples/cache_v1alpha1_memcached.yaml
  memcached.cache.example.com/memcached-sample created
  ```

1. Uninstall the operator:

  ```sh
  operator-sdk cleanup memcached-operator
  ```


### Direct deployment

1. Deploy your operator:

  ```sh
  make deploy IMG=$OPERATOR_IMG
  ```

1. Create a sample Memcached custom resource:

  ```console
  $ kubectl apply -f config/samples/cache_v1alpha1_memcached.yaml
  memcached.cache.example.com/memcached-sample created
  ```

1. Uninstall the operator:

  ```sh
  make undeploy
  ```


## Next Steps

Read the [full tutorial][tutorial] for an in-depth walkthough of building a Ansible operator.


[ansible-link]:https://www.ansible.com/
[install-guide]:/docs/building-operators/ansible/installation
[doc-olm]:/docs/olm-integration/quickstart-bundle/#enabling-olm
[tutorial]:/docs/building-operators/ansible/tutorial/
