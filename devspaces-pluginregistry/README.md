# CodeReady Workspaces Plugin Registry

This repository holds ready-to-use plugins for different languages and technologies. 

## Building and Publishing .vsix Files

Any .vsix file used in CRW needs to be built from source and have sources published alongside it. This is a non-negotiable requirement, and means that .vsix files hosted on OpenVSX or elsewhere are not fit for use in CRW.

Luckily there is an automatic way to build and publish .vsix files and their sources. Please raise a PR against the [devspaces-vscode-extensions repository](https://github.com/redhat-developer/devspaces-vscode-extensions).

## Plugin Registry Build Process
The CRW plugin registry is a fork of the upstream [Che plugin registry](https://github.com/eclipse-che/che-plugin-registry). While the build logic to build the actual registry is very similar to upstream, the steps required to actually build + publish the CRW plugin registry images is quite different.

### Local CRW Plugin Registry Build Process
A local build works just like upstream -- use the `build.sh` script. The only difference in CRW is that the resulting meta.yaml files generated by the build process are zipped up into a .tar.gz file and copied into the container. This differs from upstream which just copies the output folder directly.

### CRW Plugin Registry Build/Publish
All contributions to the CRW plugin registry should land in this repository. From there on, the following things happen:
1. Code in this repository is synchronized from this repo to the [plugin registry directory in the devspaces-images repository](https://github.com/redhat-developer/devspaces-images/tree/devspaces-3-rhel-8/devspaces-pluginregistry) by a [Jenkins job](https://main-jenkins-csb-crwqe.apps.ocp-c1.prod.psi.redhat.com/job/CRW_CI/job/crw-pluginregistry_2.x).
2. The job from step `1.` kicks off a subsequent [Jenkins job](https://main-jenkins-csb-crwqe.apps.ocp-c1.prod.psi.redhat.com/job/CRW_CI/job/sync-to-downstream_2.x) that then runs the bootstrap build, which uses the sync'd code in [devspaces-images repository](https://github.com/redhat-developer/devspaces-images/tree/devspaces-3-rhel-8/devspaces-pluginregistry). The boostrap build builds the plugin registry in offline mode, using tagged image references. The resulting binaries and meta.yaml files are placed in a .tar.gz archive and the archive is committed to rhpkg for later usage in Brew.
3. If step `2.` is successful then a Brew build is kicked off. The Brew build copies the generated content in the .tar.gz archive from step `2.` into the [Dockerfile](https://github.com/redhat-developer/devspaces/blob/devspaces-3-rhel-8/dependencies/che-plugin-registry/build/dockerfiles/Dockerfile) and completes the last stages of the plugin registry build.

## Building and Publishing Third Party Binaries for Plugin Registry Sidecar Containers

Executables and language server dependencies needed in plugin sidecar containers can be built from this repo:

https://github.com/redhat-developer/devspaces-images

Sidecar image sources are then synced from the [devspaces-images](https://github.com/redhat-developer/devspaces-images) repo to a dist-git repo at Red Hat, and from built in Brew. 

For example, the udi sidecar:

* [devspaces-images/devspaces-udi](https://github.com/redhat-developer/devspaces-images/tree/devspaces-3-rhel-8/devspacesudi)
* [containers/devspaces-udi](http://pkgs.devel.redhat.com/cgit/containers/devspaces-udi/tree/sources?h=devspaces-3-rhel-8)

## License

Red Hat OpenShift Dev Spaces is open sourced under the Eclipse Public License 2.0.