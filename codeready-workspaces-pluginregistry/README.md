# CodeReady Workspaces Plugin Registry

This repository holds ready-to-use plugins for different languages and technologies.


## Building and Publishing .vsix Files

Any .vsix file used in CRW needs to be built from source and have sources published alongside it. This is a non-negotiable requirement, and means that .vsix files hosted on OpenVSX or elsewhere are not fit for use in CRW. 

Luckily there is an automatic way to build and publish .vsix files and their sources. Please raise a PR against the [codeready-workspaces-vscode-extensions repository](https://github.com/redhat-developer/codeready-workspaces-vscode-extensions).

## Plugin Registry Build Process
The CRW plugin registry is a fork of the upstream [Che plugin registry](https://github.com/eclipse-che/che-plugin-registry). While the build logic to build the actual registry is very similar to upstream, the steps required to actually build + publish the CRW plugin registry images is quite different.

### Local CRW Plugin Registry Build Process
A local build works just like upstream -- use the `build.sh` script. The only difference in CRW is that the resulting meta.yaml files generated by the build process are zipped up into a .tar.gz file and copied into the container. This differs from upstream which just copies the output folder directly.

### CRW Plugin Registry Build/Publish
All contributions to the CRW plugin registry should land in this repository. From there on, the following things happen:
1. Code in this repository is synchronized from this repo to the [plugin registry directory in the codeready-workspaces-images repository](https://github.com/redhat-developer/codeready-workspaces-images/tree/crw-2-rhel-8/codeready-workspaces-pluginregistry) by a [Jenkins job](https://main-jenkins-csb-crwqe.apps.ocp-c1.prod.psi.redhat.com/job/CRW_CI/job/crw-pluginregistry_2.x).
2. The job from step `1.` kicks off a subsequent [Jenkins job](https://main-jenkins-csb-crwqe.apps.ocp-c1.prod.psi.redhat.com/job/CRW_CI/job/sync-to-downstream_2.x) that then runs the bootstrap build, which uses the sync'd code in [codeready-workspaces-images repository](https://github.com/redhat-developer/codeready-workspaces-images/tree/crw-2-rhel-8/codeready-workspaces-pluginregistry). The boostrap build builds the plugin registry in offline mode, using tagged image references. The resulting binaries and meta.yaml files are placed in a .tar.gz archive and the archive is committed to rhpkg for later usage in Brew.
3. If step `2.` is successful then a Brew build is kicked off. The Brew build copies the generated content in the .tar.gz archive from step `2.` into the [Dockerfile](https://github.com/redhat-developer/codeready-workspaces/blob/crw-2-rhel-8/dependencies/che-plugin-registry/build/dockerfiles/Dockerfile) and completes the last stages of the plugin registry build.

## Building and Publishing Third Party Binaries for Plugin Registry Sidecar Containers

Executables and language server dependencies needed in plugin sidecar containers can be built from this repo:

* [redhat-developer/codeready-workspaces-deprecated](https://github.com/redhat-developer/codeready-workspaces-deprecated/blob/crw-2-rhel-8/)

For example, [kamel](https://github.com/redhat-developer/codeready-workspaces-deprecated/blob/crw-2-rhel-8/kamel/build.sh) binaries are used in the kubernetes sidecar:

* [codeready-workspaces/plugin-kubernetes-rhel8](https://catalog.redhat.com/software/containers/codeready-workspaces/plugin-kubernetes-rhel8/5dae28895a13461646def87a)
* [crw/plugin-kubernetes-rhel8](https://quay.io/repository/crw/plugin-kubernetes-rhel8?tag=latest&tab=tags)

Sidecar image sources are then synced from the [codeready-workspaces-images](https://github.com/redhat-developer/codeready-workspaces-images) repo to a dist-git repo at Red Hat, and from built in Brew. For example, the kubernetes sidecar:

* [codeready-workspaces-images/codeready-workspaces-plugin-kubernetes](https://github.com/redhat-developer/codeready-workspaces-images/tree/crw-2-rhel-8/codeready-workspaces-plugin-kubernetes)
* [containers/codeready-workspaces-plugin-kubernetes](http://pkgs.devel.redhat.com/cgit/containers/codeready-workspaces-plugin-kubernetes/tree/sources?h=crw-2-rhel-8)


## License

CodeReady Workspaces is open sourced under the Eclipse Public License 2.0.