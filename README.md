# Custom block example: Polarimetric SAR-Processing
## Introduction

This repository is intended as an example of how to bring your own custom processing
[block](https://docs.up42.com/getting-started/core-concepts.html#blocks) to the [UP42 platform](https://up42.com). 
Instructions on how to set up, dockerize and push your block to UP42 are provided below or in the 
[UP42 documentation: Push your first custom block](https://docs.up42.com/getting-started/first-custom-block.html#).

The repository contains the code implementing a processing block that performs 
[polarimetric](https://en.wikipedia.org/wiki/Polarimetry) processing of 
[Sentinel-1](https://earth.esa.int/web/guest/missions/esa-operational-eo-missions/sentinel-1) Level-1 GRD 
(**G**round **R**ange **D**etection) data via the [ESA SNAP toolbox](http://step.esa.int/main/toolboxes/snap/). 
Sentinel-1 is a [**S**ynthetic **A**perture **R**adar](https://www.sandia.gov/radar/what_is_sar/index.html) (SAR) 
Earth-Observation satellite. The block functionality and performed processing steps are described in more detail in 
the [UP42 documentation: SNAP polarimetric block](https://docs.up42.com/up42-blocks/processing/snap-polarimetric.html).

**Block Input**: Sentinel-1 Level-1 GRD file in SAFE format   
**Block Output**: [GeoTIFF](https://en.wikipedia.org/wiki/GeoTIFF) file


## Requirements

This example requires the **Mac or Ubuntu bash**, an example using **Windows** will be provided shortly.
In order to bring this example block or your own custom block to the UP42 platform the following tools are required:


 - [UP42](https://up42.com) account -  [Sign up for free!]((https://up42.com))
 - [Python 3.7](https://python.org/downloads)
 - A virtual environment manager e.g. [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/)
 - [git](https://git-scm.com/)
 - [docker engine](https://docs.docker.com/engine/)
 - [GNU make](https://www.gnu.org/software/make/)
 
 
## Instructions

The following step-by-step instructions will guide you through setting up, dockerizing and pushing the example custom 
block to UP42.

### Clone the repository

```bash
git clone https://github.com/up42/snap-polarimetric.git
```

Then navigate to the folder via `cd snap-polarimetric`.

### Installing the required libraries

First create a new virtual environment called `up42-snap`, for example by using 
[virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/):

```bash
mkvirtualenv --python=$(which python3.7) up42-snap
```

Activate the new environment:

```bash
workon up42-snap
```

Install the necessary Python libraries via:

```bash
make install
```

## Testing the block locally

Before uploading the block to the UP42 platform, we encourage you to run the following local tests and validations to 
ensure that the block works as expected, conforms to the UP42 specifications and can could successfully applied in an 
UP42 workflow.

### Run the unit tests

By successfully running the implemented Python unit tests you can ensure that the block processing functionality works 
as expected. This project uses [pytest](https://docs.pytest.org/en/latest/) for testing, which was installed in 
the previous step. Run the unit tests via:

```bash
make test
```

### Validate the manifest

Then test if the block manifest is valid. The 
[UP42manifest.json](https://github.com/up42/snap-polarimetric/blob/master/blocks/snap-polarimetric/UP42Manifest.json)
file contains the block capabilities. They define what kind of data a block accepts and provides, which parameters 
can be used with the block etc. See the 
[UP42 block capabilities documentation](https://docs.up42.com/reference/capabilities.html?highlight=capabilities). 
Validate the manifest via:

```bash
make validate
```

### Run the end-to-end test

In order to run the final end-to-end (e2e) test the block code needs to be dockerized (put in a container that later on
would be uploaded to UP42). The end-to-end test makes sure the block's output actually conforms to the platform's 
requirements. 

First build the docker image locally.

```
make build
```

Then run the e2e-test via:

```bash
# WARNING: For this test to run you need to set sufficient memory and disk capacity in your docker setup. 
# Go to Docker > Preferences > Advanced and increase Memory to >8GB and Swap to >2GB. 
# This tests will take a significant amount of time to complete on a standard machine! Please be patient.

make e2e
```


## Pushing the block to the UP42 platform

First login to he UP42 docker registry. `me@example.com` needs to be replaced by your **UP42 username**, 
which is the email address you use on the UP42 website.

```bash
make login USER=me@example.com
```

In order to push the block to the UP42 platform, you need to build the block Docker container with your **UP42 USER-ID**. 
To get your USER-ID, go to the [UP42 custom-blocks menu](https://console.up42.com/custom-blocks). 
Click on "`PUSH a BLOCK to THE PLATFORM`" and copy your USERID from the command shown on the last line at 
"`Push the image to the UP42 Docker registry`". The USERID will look similar to this: 
`registry.up42.com/63uayd50-z2h1-3461-38zq-1739481rjwia`

Pass the USER-ID to the build command:

```bash
make build UID=<UID>

# As an example:
# make build UID=registry.up42.com/63uayd50-z2h1-3461-38zq-1739481rjwia
```

Now you can finally push the image to the UP42 docker registry, again passing in your USER-ID:

```bash
make push UID=<UID>
```

**Success!** The block will now appear in the [UP42 custom blocks menu](https://console.up42.com/custom-blocks/) and can
be selected under the "Custom blocks" tab when building a workflow.


### Optional: Updating an existing custom block

If you want to update a custom block on UP42, you need to tag the Docker container with an updated version tag:
The default docker tag is `snap-polarimetric:latest`.

```bash
docker tag <block-name>:<old_version-number> <block-name>:<new_version-number> 

# As an example:
# docker tag snap-polarimetric:latest snap-polarimetric:1.0
```

Then build the block container with the updated tag:

```bash
make build UID=<UID> DOCKER_TAG=<docker tag>

# As an example:
make build UID=registry.up42.com/63uayd50-z2h1-3461-38zq-1739481rjwia DOCKER_TAG=snap-polarimetric:1.0
```


## Support

Open a github issue in this repository or send us an email at [support@up42.com](mailto:support@up42.com), 
we are happy to answer your questions!
