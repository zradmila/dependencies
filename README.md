# Dependencies management 

## Theory [2]

As usual, we will start with a few theoretical questions:

* [0.5] What is Docker, and how it differs from dependencies management systems? From virtual machines?    
Docker is a software development tool and a virtualization technology that makes it easy to develop, deploy, and manage applications by using containers. A container refers to a lightweight, stand-alone, executable package of a piece of software that contains all the libraries, configuration files, dependencies, and other necessary parts to operate the application.  

**Docker vs Virtual machines**  
1. Architecture - VMs have the host OS and guest OS inside each VM. A guest OS can be any OS, like Linux or Windows, irrespective of the host OS. In contrast, Docker containers host on a single physical server with a host OS, which shares among them. Sharing the host OS between containers makes them light and increases the boot time. Docker containers are considered suitable to run multiple applications over a single OS kernel; whereas, virtual machines are needed if the applications or services required to run on different OS. 
2. Security - Virtual Machines are stand-alone with their kernel and security features. Therefore, applications needing more privileges and security run on virtual machines. On the flip side, providing root access to applications and running them with administrative premises is not recommended in the case of Docker containers because containers share the host kernel. The container technology has access to the kernel subsystems; as a result, a single infected application is capable of hacking the entire host system.
3. Portability - VMs are isolated from their OS, and so they are not ported across multiple platforms without incurring compatibility issues. At the development level, if an application is to be tested on different platforms, then Docker containers must be considered. 
4. Perfomance - Virtual Machines are more resource-intensive than Docker containers as the virtual machines need to load the entire OS to start.

* [0.5] What are the advantages and disadvantages of using containers over other approaches?  
 **Advantages** 
1. Light and minimal overhead – Docker images are typically very small, which promotes rapid delivery and reduces the time to deploy new application containers.
2. Rapid deployment – containers carry the minimal runtime requirements of the application, decreasing their size and enabling them to be deployed instantly.  
3. Portability – an application and all its dependencies can be grouped into a separate container that is autonomous from the host version of Linux kernel, platform configuration, or deployment type.
4. Version control and component retain – it is possible to pursue succeeding versions of a container, inspect irregularities or go back to previous versions. Containers reuse segments from the preceding layers, which makes them remarkably light.
5. Sharing – it allows to use a distant repository to share your container with others.

 **Disadvantages**  
1.  There are some applications that are not suited for Docker and should be run on a dedicated machine. Applications that have many services that need to seamlessly work together might be an example since Docker containers are meant to only do one thing. This would require a separate container for each service.
2.  Difficult to manage large amount of containers.

* [0.5] Explain how Docker works: what are Dockerfiles, how are containers created, and how are they run and destroyed?  

 **Dockerfile** is a text document that contains all the commands a user could call on the command line to assemble an image. Docker can build images automatically by reading the instructions from a Dockerfile.  
   
 **Image** is a read-only template with instructions for creating a Docker container. Often, an image is based on another image, with some additional customization. It is possible to create your own images or use those created by others and published in a registry. To build an image, a user create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a Dockerfile creates a layer in the image. When changes are applied to the Dockerfile and the image is rebuilt, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.  

 **Container** is a runnable instance of an image. It is possible to create, start, stop, move, or delete a container using the Docker API or CLI. Docker allows to connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.
By default, a container is relatively well isolated from other containers and its host machine. We can control how isolated a container’s network, storage, or other underlying subsystems are from other containers or from the host machine. A container is defined by its image as well as any configuration options a user provide to it when they create or start it. When a container is removed, any changes to its state that are not stored in persistent storage disappear.  

Container can be created with the command:  
`docker create [OPTIONS] IMAGE [COMMAND] [ARG...]`  
The docker daemon creates a writeable container layer over the specified image and prepares it for running the specified command.  
The comand that is used to run container:  
`docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`  
The docker run command first creates a writeable container layer over the specified image, and then starts it using the specified command.  
Containers can be destroyed by:  
`docker kill [OPTIONS] CONTAINER [CONTAINER...]`  

* [0.25] Name and describe at least one Docker competitor (i.e., a tool based on the same containerization technology).  

**Kubernetes** (aka K8) is an open-source container automation system developed by Google to manage container applications in physical, virtual, or cloud environments. Kubernetes functions as an orchestrator that controls thousands of containers and workloads.  
If you’re running multiple containerized applications irrespective of their hosting platforms, you will be needing Kubernetes, which serves as an API for coordinating, controlling, scheduling, and automating multiple containers. 
Although Docker performs a similar orchestration function, unlike Kubernetes, it can only manage a node (made of a cluster of containers), and it does not have an automatic node rescheduling feature for rescheduling inactive nodes. 
In contrast, Kubernetes can easily and efficiently manage multiple clusters (multiple nodes) and automatically reschedule inactive nodes.

If you’re running multiple containerized applications, you can combine Docker with Kubernetes. Kubernetes lets you manage and control multiple containers from a single machine and helps you network, do load-balancing, and security upscaling across all your container nodes.

* [0.25] What is conda? How it differs from apt, yarn, and others?   

**Conda** is an open-source, cross-platform, language-agnostic package manager and environment management system. Conda allows users to easily install different versions of binary software packages and any required libraries appropriate for their computing platform. Also, it allows users to switch between package versions and download and install updates from a software repository.    
In comparison to other package manageres,  packages installed with conda are installed locally with a user-specific configuration. Administrative privileges are not required, and no upstream files or other users are affected by the installation.

## Problem [6.5]

The problem itself is relatively simple. 

Imagine that you developed an excellent RNA-seq analysis pipeline and want to share it with the world. Based on your experience, you are confident that the popularity of the pipeline will be proportional to its ease of use. So, you decided to help your future users and to pack all dependencies in a Conda environment and a Docker container.

Here is the list of tools and their versions that are used in your work:
* [fastqc](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/), v0.11.9
* [STAR](https://github.com/alexdobin/STAR), v2.7.10b
* [reat](https://github.com/alnfedorov/reat), commit ee19bc928badd227975410a6b8b715c0e03bd4ab 
* [samtools](https://github.com/samtools/samtools), v1.16.1
* [picard](https://github.com/broadinstitute/picard), v2.27.5
* [salmon](https://github.com/COMBINE-lab/salmon), commit tag 1.9.0
* [bedtools](https://github.com/arq5x/bedtools2), v2.30.0
* [multiqc](https://github.com/ewels/MultiQC), v1.13

**Anaconda**:

* [1] Install conda, create a new virtual environment, and install all necessary packages. 
* [0.75] You won't be able to install some tools - that's fine. List their names, and explain what should be done to make them conda-friendly ([conda-forge](https://conda-forge.org/docs/maintainer/adding_pkgs.html) channel, [bioconda](https://bioconda.github.io/contributor/workflow.html) channel). 
* [0.25] [Export](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#exporting-the-environment-yml-file) the environment ([example](https://github.com/nf-core/clipseq/blob/master/environment.yml)) to the file and verify that it can be [rebuilt](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-from-an-environment-yml-file) from the file without problems.

-----
## Conda installation 
Instructions for installation were derived from this [page](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html)

## New environment creation 
A new environment were created with this command
```
conda create env -n zaikina_env
```
Then the environment was activated by
```
conda activate zaikina_env
```
## Bioinformatics tools installation
For a proper installation, bioconda and conda-forge channels were used.
```
conda config --add channels bioconda
conda config --add channels conda-forge
```
Adding mentioned channels to config allowed to install tools in this manner without specifying channel:
```
conda install -y fastqc=0.11.9
conda install -y star=2.7.10b
conda install -y samtools=1.16.1 
conda install -y picard=2.27.5
conda install -y salmon=1.9.0
conda install -y bedtools=2.30.0
conda install -y multiqc=1.13
```
## Environment export 
Created environment was exported with this command:
```
conda env export --from-history > zaikina_env.yml
```
Then my environment was deactivated and removed:
```
conda deactivate 
conda remove -n zaikina_env --all
```
zaikina_env.yml:
```
name: zaikina_env
channels:
  - conda-forge
  - bioconda
  - defaults
dependencies:
  - fastqc=0.11.9
  - star=2.7.10b
  - samtools=1.16.1
  - picard=2.27.5
  - salmon=1.9.0
  - bedtools=2.30.0
  - multiqc=1.13
prefix: /home/rada/anaconda3/envs/zaikina_env
```
## Rebuild conda environment  
To rebuild environment from a file:
```
conda env create -f zaikina_env.yml
``` 
----------
## Docker:
* [3] Create a Dockerfile for a container with **all** required dependencies. Don't forget about comments; test that all tools are accessible and work inside the container. Hints:
 - If needed, grant rights to execute downloaded/compiled binaries using chmod (`chmod a+x BINARY_NAME`)
 - Move all executables to $PATH folders (e.g.`/usr/local/bin`) to make them accessible without specifying the full path.
 - Typical command to run a container interactively (`-it`) and delete on exit(`--rm`): `docker run --rm -it name:tag`
* [1] Use [hadolint](https://hadolint.github.io/hadolint/) and remove as many reported warnings as possible.
* [0.5] Add relevant [labels](https://docs.docker.com/engine/reference/builder/#label), e.g. maintainer, version, etc. ([hint](https://medium.com/@chamilad/lets-make-your-docker-image-better-than-90-of-existing-ones-8b1e5de950d))

## Dockerfile creation  
In a text editor, I created a Dockerfile with all commands that should be run to install required packages.
```
FROM ubuntu

RUN apt-get update \
  && apt-get install -y apt-utils \
  && apt-get install -y wget \
  && apt-get install -y unzip \
  && apt-get install -y python3-pip \
  && apt-get install -y openjdk-11-jre-headless

RUN touch /root/.bashrc

# fastqc installation
RUN wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.9.zip \
  && unzip fastqc_v0.11.9.zip \
      && rm fastqc_v0.11.9.zip \
      && chmod a+x FastQC/fastqc \
      && echo 'alias fastqc="/FastQC/fastqc"' >> ~/.bashrc

# star installation
RUN wget https://github.com/alexdobin/STAR/releases/download/2.7.10b/STAR_2.7.10b.zip \
  && unzip STAR_2.7.10b.zip \
      && rm STAR_2.7.10b.zip \
      && chmod a+x STAR_2.7.10b/Linux_x86_64_static/STAR \
      && mv STAR_2.7.10b/Linux_x86_64_static/STAR /bin/STAR \
      && rm -r STAR_2.7.10b
    
# samtools installation
RUN wget https://github.com/samtools/samtools/archive/refs/tags/1.16.1.zip -O ./samtools-1.16.1.zip \
  && unzip samtools-1.16.1.zip \
      && rm samtools-1.16.1.zip \
  && mv samtools-1.16.1/misc samtools \
      && rm -r samtools-1.16.1 \
      && echo 'alias samtools="/samtools/samtools.pl"' >> ~/.bashrc

# picard installation
RUN wget https://github.com/broadinstitute/picard/releases/download/2.27.5/picard.jar -O /bin/picard.jar \
  && chmod a+x /bin/picard.jar \
      && echo 'alias picard="java -jar /bin/picard.jar"' >> ~/.bashrc

# salmon installation
RUN wget https://github.com/COMBINE-lab/salmon/releases/download/v1.9.0/salmon-1.9.0_linux_x86_64.tar.gz \
  && tar -zxvf salmon-1.9.0_linux_x86_64.tar.gz \
  && rm salmon-1.9.0_linux_x86_64.tar.gz \
      && chmod a+x salmon-1.9.0_linux_x86_64/bin/salmon \
      && mv salmon-1.9.0_linux_x86_64/bin/salmon /bin/salmon \
      && rm -r salmon-1.9.0_linux_x86_64 

# bedtools installation
RUN wget https://github.com/arq5x/bedtools2/releases/download/v2.30.0/bedtools.static.binary -O /bin/bedtools.static.binary \
      && chmod a+x /bin/bedtools.static.binary \
      && echo 'alias bedtools="/bin/bedtools.static.binary"' >> ~/.bashrc
  
# multiqc installation
RUN pip install multiqc==1.13
```
## Docker build 
```
docker build -t bionf_tools .
```
`-t` - the image name
`.` - means that a Dockerfile is in a current directory 

## Docker run 
To run a container interactively (`-it`) and delete on exit(`--rm`)
```
docker run --rm -it bioinf_tools
```
## Dockerfile linter 
Linter specified that it is importnat to tag the version of an image explicitly.
In addition, this message was provided:
```
Pin versions in apt get install. Instead of `apt-get install <package>` use `apt-get install <package>=<version>`
Delete the apt-get lists after installing something
Avoid additional packages by specifying `--no-install-recommends`
```
I also added some labels at the beginning of the file with maintainer, version and description information.
So, the improved version of my Dockerfile looks like this:
```
# Base Image 
FROM ubuntu:20.04

# Metadata
LABEL maintainer=<zaikinaradmila@gmail.com>
LABEL version="1.0"
LABEL description="Docker container for bioinformatic tools"

# Required packages
RUN apt-get update \
  && apt-get install -y --no-install-recommends apt-utils=2.0.2ubuntu0.2  \
  && apt-get install -y --no-install-recommends wget=1.19.4-1ubuntu2.2 \
  && apt-get install -y --no-install-recommends unzip=6.0-25ubuntu1.1 \
  && apt-get install -y --no-install-recommends python3-pip=20.0.2-5ubuntu1.5 \
  && apt-get install -y --no-install-recommends openjdk-11-jre-headless=11.0.17+8-1ubuntu2~20.04 \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

RUN touch /root/.bashrc

# fastqc installation
RUN wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.9.zip \
  && unzip fastqc_v0.11.9.zip \
      && rm fastqc_v0.11.9.zip \
      && chmod a+x FastQC/fastqc \
      && echo 'alias fastqc="/FastQC/fastqc"' >> ~/.bashrc

# star installation
RUN wget https://github.com/alexdobin/STAR/releases/download/2.7.10b/STAR_2.7.10b.zip \
  && unzip STAR_2.7.10b.zip \
      && rm STAR_2.7.10b.zip \
      && chmod a+x STAR_2.7.10b/Linux_x86_64_static/STAR \
      && mv STAR_2.7.10b/Linux_x86_64_static/STAR /bin/STAR \
      && rm -r STAR_2.7.10b
    
# samtools installation
RUN wget https://github.com/samtools/samtools/archive/refs/tags/1.16.1.zip -O ./samtools-1.16.1.zip \
  && unzip samtools-1.16.1.zip \
      && rm samtools-1.16.1.zip \
  && mv samtools-1.16.1/misc samtools \
      && rm -r samtools-1.16.1 \
      && echo 'alias samtools="/samtools/samtools.pl"' >> ~/.bashrc

# picard installation
RUN wget https://github.com/broadinstitute/picard/releases/download/2.27.5/picard.jar -O /bin/picard.jar \
  && chmod a+x /bin/picard.jar \
      && echo 'alias picard="java -jar /bin/picard.jar"' >> ~/.bashrc

# salmon installation
RUN wget https://github.com/COMBINE-lab/salmon/releases/download/v1.9.0/salmon-1.9.0_linux_x86_64.tar.gz \
  && tar -zxvf salmon-1.9.0_linux_x86_64.tar.gz \
  && rm salmon-1.9.0_linux_x86_64.tar.gz \
      && chmod a+x salmon-1.9.0_linux_x86_64/bin/salmon \
      && mv salmon-1.9.0_linux_x86_64/bin/salmon /bin/salmon \
      && rm -r salmon-1.9.0_linux_x86_64 

# bedtools installation
RUN wget https://github.com/arq5x/bedtools2/releases/download/v2.30.0/bedtools.static.binary -O /bin/bedtools.static.binary \
      && chmod a+x /bin/bedtools.static.binary \
      && echo 'alias bedtools="/bin/bedtools.static.binary"' >> ~/.bashrc
  
# multiqc installation
RUN pip install multiqc==1.13
```

-----

## Extra points [1.5]

You will be awarded extra points for the following:
* [0.5] Using [multi-stage builds](https://docs.docker.com/build/building/multi-stage/) in Docker. E.g. to build STAR and copy only the executable to the final image.

------
* [0.75] Minimizing the size of the final Docker image. That is, removing all intermediates, unnecessary binaries/caches, etc. Don't forget to compare & report the final size before and after all the optimizations.

```
docker build --no-cache -t docker_extra .
```
The best I could get was:

Original Dockerfile size: 

Minimized Dockerfile size: 

-----
* [0.25] Create an extra Dockerfile that starts from [a conda base image](https://hub.docker.com/r/continuumio/anaconda3) and builds everything from your conda environment file. 

Hint: `conda env create --quiet -f environment.yml && conda clean -a` ([example](https://github.com/nf-core/clipseq/blob/master/Dockerfile))

Dockerfile for conda:

```
# Base Image
FROM continuumio/anaconda3

# Metadata
LABEL maintainer=<zaikinaradmila@gmail.com>

#
COPY zaikina_env.yml .

ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
&& apt-get install -y --no-install-recommends apt-utils \
&& apt-get install -y --no-install-recommends apt-transport-https \
&& conda env create --quiet -f zaikina_env.yml && conda clean -a \
```
