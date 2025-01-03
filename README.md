<img src='images/ted.png'> 

# SMI-TED Inference for Smiles <!-- omit from toc -->

[![License MIT](https://img.shields.io/github/license/acceleratedscience/openad_service_utils)](https://opensource.org/licenses/MIT)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![Docs](https://img.shields.io/badge/website-live-brightgreen)](https://acceleratedscience.github.io/openad-docs/) <br>
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![macOS](https://img.shields.io/badge/mac%20os-000000?style=for-the-badge&logo=macos&logoColor=F0F0F0)
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)

## About: <!-- omit from toc -->
<About Text needed>

SMILES-based Transformer Encoder-Decoder (SMILES-TED) is an encoder-decoder model pre-trained on a curated dataset of 91 million SMILES samples sourced from PubChem, equivalent to 4 billion molecular tokens. SMI-TED supports various complex tasks, including quantum property prediction, with two main variants ( 289 M and 8 × 289 M ).

This repository provides a Python-based tool to host the SMI-TED capablities via a REST API. Once the API is set up, use the OpenAD Toolkit to easily access the inference functions of SMI-TED. 

More information on SMI-TED can be found at:<br> 
- https://huggingface.co/ibm/materials.smi-ted
- https://github.com/IBM/materials/tree/main
- https://arxiv.org/abs/2407.20267

More information on the OpenAD Toolkit and OpenAD Service Utilities:
- https://accelerate.science/projects/openad
- https://github.com/acceleratedscience/open-ad-toolkit
- https://github.com/acceleratedscience/openad_service_utils

--- 

## Deployment Options <!-- omit from toc -->

<!-- toc -->

- [Deployment locally using a Python virtual environment](#deployment-locally-using-a-python-virtual-environment)

- [Deployment locally via container](#deployment-locally-via-container)

- [Deployment On OpenShift](#deployment-lon-openshift)

- [Deployment via Sky Pilot](#deployment-via-sky-pilot)

<!-- tocstop -->
Note: All of these deployment options will allow you to access SMI-TED functions using the [OpenAD Toolkit](https://github.com/acceleratedscience/open-ad-toolkit).  You may want to first install the Toolkit in its own Python environment before proceeding with deploying the SMI-TED utility. <br>

--- 

# Deployment locally using a Python virtual environment 
<br>
You will need a Python level of 3.11 & to follow these installation directions:<br>
<br>

1. Use your favorite Python environment manager (e.g., Conda, Pyenv) to create a new Python 3.11.10 environment <br>

2. Activate the new environment and install the required Python modules per the "Getting Started" instructions at this site (ignore the requirement to use Python 3.9): <br>
   https://github.com/IBM/materials/<br>
   
3. Install the OpenAD Service Utilities in the new environment with the following command:<br>
   `pip install git+https://github.com/acceleratedscience/openad_service_utils.git@nested_implementation`<br>

4. Clone this repo into a new directory: <br>
   `git clone https://github.com/acceleratedscience/openad_smi_ted`<br>
   
5. Change directory to `openad_smi_ted` then start the server with the following command:<br>
   `python app.py`<br>

6. In a new terminal session start the OpenAD Toolkit:<br>
   `openad`<br>

7. At the OpenAD Toolkit command line execute the following command to create a new service for accessing the local server started in step 5:<br>
   `catalog model service from remote 'http://127.0.0.1:8080/' as sm`<br>

8. At the OpenAD Toolkit command line execute the following command to view the available commands:<br>
   `sm ?`

***Note***: The first time you request a particular property (e.g., "qm8-e1-cam") the OpenAD Service Utility will take some time to download and locally cache the correct models. Requests will proceed much faster once models are cached locally.  

# Deployment locally via container
<br>
run on the command line `mkdir -p ~/.openad_models`

***Note:*** <br>
Initially downloading models may take some time, this will be prompted by your first request. To pre-load models you can run the following <br><br>
`mkdir -p ~/.openad_models/properties/molecules && aws s3 sync s3://ad-prod-biomed/molecules/small_molecules/ /tmp/.openad_models/properties/molecules/small_molecules --no-sign-request --exact-timestamps`
<br>
it does require installing the AWS cli which can be found here..

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html

then using Podman or Docker run the following in the same directory as the compose.yaml file:
### step 1:
`(podman or docker) compose create`<br>
### step 2:
`(podman or docker) start`<br>

the service will start on poert `8080` change this in the compose file if you wish it to run on another port.
### Step 3:
In openad run the following command
`catalog model service from remote 'http://127.0.0.1:8080/' as sm`

### Notes

- The container used is https://quay.io/ibmdpdev/bmfm_sm_properties:latest

- You can use the compose.yaml file rather than download the entire repository

https://github.com/acceleratedscience/bmfm-sm/blob/main/compose.yaml

- This has been run soccessfully on Mac OS with Podman and Rosetta.



# Deployment On OpenShift
Helm Charts are available for OpenShift including Auto scaling of services.

See the helm-chart directory in the repository

This currently does not support aynchronous requests, you can enable it if you deploy a shared filesystem, or only have a single pod running.


# Deployment via Sky Pilot
<br>
Support for skypilot on AWS is available. you must have a valid aws account setup with appropriate accound settings to allow sky pilot

### In openad running the following at the OpenAD prompt or Magic Command
### Step 1:
`catalog model service from 'git@github.com:acceleratedscience/bmfm-sm.git' as sm`<br>
### Step 2: 
`model service up sm` <br>

to stop the service run `model service down` in openad.

***Note*** you will now have to wait until the service is available use `sky status` to see if the service is up and provisioned
