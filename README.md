# Workshop
## 01-basic-components

## 02-docker-multistage
To play around with and get a feel for multistage builds


```bash
cd 02-docker-multistage
IMAGE_NAME=spring-test

docker build $IMAGE_NAME .
docker build -t ${IMAGE_NAME}:runtime-alpine --target runtime-alpine .
docker build -t ${IMAGE_NAME}:debug --target debug .


TAG_TO_RUN=runtime-alpine
docker run --rm -p 8080:8080 ${IMAGE_NAME}:${TAG_TO_RUN}
```

### Docker basics

#### COPY VS ADD
"Although ADD and COPY are functionally similar, generally speaking, COPY is preferred. That’s because it’s more transparent than ADD. COPY only supports the basic copying of local files into the container, while ADD has some features (like local-only tar extraction and remote URL support) that are not immediately obvious. Consequently, the best use for ADD is local tar file auto-extraction into the image, as in ADD rootfs.tar.xz /."

## Entrypoint https://docs.docker.com/engine/reference/builder/#entrypoint
`--init # https://github.com/krallin/tini`
## ENTRYPOINT VS. CMD
https://docs.docker.com/engine/reference/builder/#entrypoint


Further resoruces
- https://medium.com/@tonistiigi/advanced-multi-stage-build-patterns-6f741b852fae
- https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

## 03-okd-s2i
s2i: https://hub.docker.com/r/fabric8/s2i-java // https://github.com/fabric8io-images/s2i

`cd 03-okd-s2i`

# Steps

1. Import the base image: `oc import-image -h`

`oc apply -f imagestream.yml`

2. create s2i-build-config: 

```bash
# process template via: oc process -f s2i.yml -p APPLICATION_NAME=XY -p REPOSITORY_URL=re...
oc process -f s2i.yml \
  -p APPLICATION_NAME=spring-boot \
  -p REPOSITORY_URL=https://github.com/spring-guides/gs-spring-boot.git \
  -p CONTEXT_DIR=complete \
  | oc apply -f -
```

3. start a build: `oc start-build spring-boot-dev -w # -w watches the output`

4. Create svc and dc: `oc new-app --image-stream spring-boot`


## Resources

https://docs.openshift.com/online/dev_guide/builds/advanced_build_operations.html
https://docs.openshift.com/online/dev_guide/builds/build_strategies.html

## 04-okd-chained-build
see README.md in 04-okd-chained-build

## Pattern 
![pattern](https://devopedia.org/images/article/122/7070.1538988426.jpg)
- Single node multi container: sidecar, ambassador, adapter
- Multi node: leader election, work queue, scatter/gather

### Ambassador / proxy - 05-ambassador
The main container communicates or gets called through an ambassador. (Proxy, name resolver, etc.)
https://github.com/openshift/oauth-proxy/blob/master/contrib/sidecar.yaml

### Sidecar - 06-sidecare
Enhances basic pod functionality. Logging, circut breaker, ...

### Adapter - 07-adapter
Like an adapter in software development. Changes the interface or converts the requests from or to the main container 

## Openshift guide
https://github.com/openshift-labs/devops-guides/tree/ocp-3.9