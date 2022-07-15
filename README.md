# Getting started with OCM
This document gives an introduction how to work with the [OCM](https://github.com/gardener/component-spec/blob/master/doc/proposal/00-overview.md)  (Open Component Model) and the OCM Command line interface (CLI). It describes some real-world scenarios how to use this model and what benefit it provides.

All files from the examples can be found in this repository.

## Create a component-descriptor for a simple application
The [Dockerfile](./Dockerfile) creates a container with a simple static web server.

Building: (This example uses the GCR as image repository, any other is fine too)
```
gcloud auth login
docker push eu.gcr.io/gardener-project/test/ocm/examples/hello:0.1.0
docker tag hello:0.1.0 eu.gcr.io/gardener-project/test/ocm/examples/hello:0.1.0
docker push eu.gcr.io/gardener-project/test/ocm/examples/hello:0.1.0

docker run -d --rm --name web-test -p 80:8000 hello:0.1.0
```

### Creating a component-descriptor manually

Create a directory named `ocm` with a file named `resources.yaml`:

```
name: 'ocm-hello'
version: '0.1.0'
type: 'ociImage'
relation: 'external'
access:
  type: 'ociRegistry'
  imageReference: 'eu.gcr.io/gardener-project/test/ocm/examples/hello:0.1.0'

```

Then call:
```
ocm create componentarchive github.com/gardener/ocm-example-hello 0.1.0 "SAP SE" ocm-out
```

The outcome is a file structure:

```
.
└── ocm-out
    ├── blobs
    └── component-descriptor.yaml
```

Add the sources to the component-descriptor (Note: currently not implemented, not working)
Create in folder `ocm` with a file named `sources.yaml`:

```
name: github_com_jensh007_ocm-hello
access:
  type: github
  repoUrl: github.com/jensh007/ocm-example-hello
  ref: refs/heads/main
  commit: 166152df2856f6ec4d48fd68154987ed990894fa
version: 0.1.0-dev
type: git
```

Then call

```
ocm add sources ocm-out ocm/sources.yaml
```

The outcome is:
```
TODO
```

### Storing the component-descriptor in a repository
TODO:

### Retrieving the component-descriptor
TODO: read the content from remote list resources, dependencies

### Deploying the example on Kubernetes
TODO: First step manual kuebctl commands

Goal: Create a second cd describing the current deployment state referring to the first one

Point to scenarios where you could use this:

* a scan tool checking the code and/or image
* transporting the landscape to other environments

### Upgrading the application
There are fields in the cd changing frequently (e.g. version, commit) and others being stable (e.g. repo-url). Use templating to update the cd. Describe the immutability of the cd. Create a second cd via CLI by templating the first one. Perhaps provide a simple build script providing a version upgrade

### Repeat the deploy step and create a second deployment CD

## Scenario Transport
Take the CD describe the steps to transfer this to a different cluster.
Describe the necessary steps how to modify the CD.

### Step Automation
What do we need to automate this?

* Library instead of CLI
* cd creation as part of build process
* cd update as part of deployment process
* Git Ops / Flux / ...?
* OCM controller ?
* Catalog of known deployment types (kustomize, helm, etc)

TODO to decide what we can demo here, maybe to put this somewhere else

