# Working with remote servers

## Theory [2]

* [0.4] What are [computer ports](https://www.cloudflare.com/learning/network-layer/what-is-a-computer-port/) on a high level? How many ports are there on a typical computer?
* [0.4] What is the difference between http, https, ssh, and other protocols? In what sense are they similar? Name default ports for several data transfer protocols.
* [0.4] Explain briefly: (1) what is IP, (2) what IPs are called 'white'/public, (3) and what happens when you enter 'google.com' into the web browser. 
* [0.4] What is Nginx? How does it work on the high level? List several alternative web servers.
* [0.4] What is SSH, and for what is it typically used? Explain two ways to authenticate in an SSH server in detail.
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
