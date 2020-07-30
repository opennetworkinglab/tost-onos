# TOST ONOS Development Build Environment

Docker build environment capable of producing a version of **ONOS** and needed apps that can run with **TOST**. Typically the **ONOS** restful api would be used to include apps after **ONOS** is started.

## Build

We provide multiple build targets for the Makefile

`onos-build` is used to build a specialized **Docker** image of **ONOS** (`tost-onos`) that will contain only the apps needed by **TOST**. It depends on `onos` target, which is used to setup the `onos` workspace for the build. It is possible to build the image by using a specific branch (export `ONOS_BRANCH` variable) or by defining a specific review (export `ONOS_REVIEW` variable). It clones `onos` if it does not exist in the workspace and uses current workspace unless above vars are defined.

```sh
# Build a tost-onos image from the current workspace.
make onos-build
```

```sh
# Build a tost-onos image from the onos-2.2 branch.
make ONOS_BRANCH=onos-2.2 onos-build
```

```sh
# Build a tost-onos image from the review 12345.
make ONOS_REVIEW=12345 onos-build
```

Makefile will build also the apps. These are the apps currently integrated in the script: **trellis-control**, **trellis-t3**, **fabric-tofino**, **up4**, **kafka-onos** and **fabric-tna**. For each one, there is a **build** target.

`app-name-build` builds the oar from the source code. If **APPNAME_MVN** is specified, the script will download the oar from the maven repositories. As prerequisite, the script prepares `mvn_settings.xml` file, creates the `local-apps` folder and checks out the code if it is not present (relies on `app-name` target). `app-name` allows the developers to specify an **APPNAME_BRANCH** or an **APPNAME_REVIEW**.

```sh
# Build trellis-control from the source code.
make trellis-control-build
```

```sh
# Build up4 app from the source code.
make up4-build
```

`apps-build` is an additional target that automates the build process of the apps building one by one all the apps.

```sh
# Build one by one all the apps
make apps-build
```

Finally, the last build target is `tost-build`. It builds a `tost` monolithic image using as base the `tost-onos` image. It basically adds the external apps used by **TOST**. It does not activate all the required apps. This step is performed during the deployment and the required apps are specified in the chart.

```sh
# Build a tost image from the current workspace.
make tost-build
```

## Clean

Use target `clean` to remove the local artificats generated by the tool.

```sh
# Cleans the workspace
make clean
```

## Push

We provide multiple push target for the Makefile. Typically, you need to first login by `docker login` command to push the image on a repository.

`onos-push` will push the `tost-onos` image.

```sh
make onos-push
```

`tost-push` will push the `tost` image on the defined **DOCKER_REGISTRY** and **DOCKER_REPOSITORY**.

```sh
make DOCKER_REPOSITORY=onosproject/ tost-push
```

## CI/CD targets

There are two special targets used by the CI/CD jobs: `docker-build` and `docker-push`. The first target automates the build process of the `tost` image (`onos-build`, `apps-build` and `tost-build`). While the second one, it is just a `tost-push` called in a different way (temporary). Feel free to use them if you are ok with the prerequisites steps.
