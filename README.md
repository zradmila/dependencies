# Dependencies management 

**Introduction**

## Theory [2]

As usual, we will start with a few theoretical questions:

* [0.5] What is Docker, and how it differs from dependencies management systems? From virtual machines?    
Docker is an open platform for developing, shipping, and running applications. Docker provides the ability to package and run an application in a loosely isolated environment called a container. The isolation and security allows to run many containers simultaneously on a given host. Containers are lightweight and contain everything needed to run the application, so there is no need to rely on what is currently installed on the host. It provides an easy way to share containers while working, and be sure that everyone who uses it gets the same container that works in the same way.

Docker is a technology for creating and managing containers
* [0.5] What are the advantages and disadvantages of using containers over other approaches?  
 **Advantages**  
 **Disadvantages**  

* [0.5] Explain how Docker works: what are Dockerfiles, how are containers created, and how are they run and destroyed?  

 **Dockerfile** is a text document that contains all the commands a user could call on the command line to assemble an image. Docker can build images automatically by reading the instructions from a Dockerfile.  
   
 **Image** is a read-only template with instructions for creating a Docker container. Often, an image is based on another image, with some additional customization. It is possible to create your own images or use those created by others and published in a registry. To build your own image, you create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.  

 **Container** is a runnable instance of an image. We can create, start, stop, move, or delete a container using the Docker API or CLI. You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.
By default, a container is relatively well isolated from other containers and its host machine. You can control how isolated a container’s network, storage, or other underlying subsystems are from other containers or from the host machine.
A container is defined by its image as well as any configuration options you provide to it when you create or start it. When a container is removed, any changes to its state that are not stored in persistent storage disappear.  

* [0.25] Name and describe at least one Docker competitor (i.e., a tool based on the same containerization technology).  

* [0.25] What is conda? How it differs from apt, yarn, and others?   
  **Conda** is an open source package management system and environment management system.   
  In comparison to other package manageres, conda can work locally. It can work for each user independently and does not require admin's permission.

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
conda activate zaikina env
```
## Bioinformatics tools installation
For a proper installation, bioconda and conda-forge channels were used.
```
conda install -c bioconda fastqc=0.11.9   
conda install -c bioconda star=2.7.10b    
conda install -c bioconda bedtools=v2.30.0     
conda install -c bioconda multiqc=1.13       
conda install -c conda-forge -c bioconda samtools=1.16.1   
conda install -c  bioconda picard=2.27.4  
conda install -c  bioconda salmon
```
I had to lower the version of picard because dowload just freezed. The same thing was with salmon tool, so I installed the available version of this tool.

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

## Rebuild conda environment  
To rebuild environment from a file:
```
conda env create -f zaikina_env.yml
``` 

**Docker**:
* [3] Create a Dockerfile for a container with **all** required dependencies. Don't forget about comments; test that all tools are accessible and work inside the container. Hints:
 - If needed, grant rights to execute downloaded/compiled binaries using chmod (`chmod a+x BINARY_NAME`)
 - Move all executables to $PATH folders (e.g.`/usr/local/bin`) to make them accessible without specifying the full path.
 - Typical command to run a container interactively (`-it`) and delete on exit(`--rm`): `docker run --rm -it name:tag`
* [1] Use [hadolint](https://hadolint.github.io/hadolint/) and remove as many reported warnings as possible.
* [0.5] Add relevant [labels](https://docs.docker.com/engine/reference/builder/#label), e.g. maintainer, version, etc. ([hint](https://medium.com/@chamilad/lets-make-your-docker-image-better-than-90-of-existing-ones-8b1e5de950d))

In a text editor, I created a Dockerfile with all command that should be run to install required packages.
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
docker build -t boinf_tools .
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

COPY zaikina_env.yml .

ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
&& apt-get install -y --no-install-recommends apt-utils \
&& apt-get install -y --no-install-recommends apt-transport-https \
&& conda env create --quiet -f zaikina_env.yml && conda clean -a \
```
