# ansible-sandbox: docker

# Overview

The goal is to provide an `dockeradmin` script to build out a Docker base image. There are many ways to perform this task. The setup provided here assumes a RHEL-based target environment. Using [moby/moby](https://github.com/moby/moby) as a reference, this project modifies [mkimage-yum.sh](https://github.com/moby/moby/blob/master/contrib/mkimage-yum.sh) to match up with development requirements.

```
dockeradmin [options] /path/to/imgenv
  imgenv             The full path to the imgenv file
  -y  <yum.conf>     The full path to the yum.conf - default to /etc/yum.conf
  -t  <tag>          Tag name - default 'latest'
  -h                 Print the help message
```

# Image Environment

Configuration file detailing the following.

1. `reponame` The image repository name.

2. `packages` Full name of yum packages to install.

3. `rpms` Full path of RPM files on the local machine to install as part of the base image.

# Assumptions

1. The machine running `dockeradmin` is connected to a valid yum repository.

2. The user has `sudo` privileges.

# Building a Base Image

1. Use `vagrant.env` as a template to declare the yum artifacts and RPM artifacts in another file; the filename is the image repository name.

2. You may need to download the RPM artifacts that put then somewhere on the machine running `dockeradmin`.

3. As root: `dockeradmin <name>` (the name from Step 1)

4. As root: `docker image ls --all` to confirm local installed image.

5. Use the docker image in your application `Dockerfile`

## Example: Build a JDK Base Image

1. Download the [JDK 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

2. Create a base image `basejdk.env` file (below)

3. As root: `dockeradmin basejdk`

4. As root: `docker run basejdk:latest java -version`

```
# basejdk.env
reponame=basejdk
packages=()
rpms=('/tmp/jdk-11.0.4_linux-x64_bin.rpm')
```

```
sudo docker run basejdk:latest java -version
java version "11.0.4" 2019-07-16 LTS
Java(TM) SE Runtime Environment 18.9 (build 11.0.4+10-LTS)
Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.4+10-LTS, mixed mode)
```
