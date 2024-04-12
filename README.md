# Business Automation Manager Open Editions 8 standalone CeKit Module

This repo contains the base module that installs the product artifacts in the target OpenShift image.

does not need to be built manually, it is used as a cekit module  that will builds the base image that contains
the EAP with the application layer (business central, kieserver, etc.) on it without any kind of configuration, we call
it standalone-image, all the configuration will be made by the *-openshift repositories which contains the image
descriptors with all modules that will be installed. In this repo you will find the basics like which artifact
(kieserver, business central, etc) is being used to build the openshift images.

Let’s inspect the ibm-bamoe-8-businesscentral cekit module:



```yaml
schema_version: 1
name: "ibm-bamoe-8-businesscentral"
version: "1.0"
description: "IBM Business Central 8.0 installer"
labels:
- name: "org.jboss.product"
  value: "ibm-bamoe-businesscentral"
- name: "org.jboss.product.version"
  value: "8.0.5"
- name: "org.jboss.product.ibm-bamoe-businesscentral.version"
  value: "8.0.5"
envs:
- name: "JBOSS_PRODUCT"
  value: "ibm-bamoe-businesscentral"
- name: "IBM_BAMOE_BUSINESS_CENTRAL_VERSION"
  value: "8.0.5"
- name: "PRODUCT_VERSION"
  value: "8.0.5"
- name: "BUSINESS_CENTRAL_DISTRIBUTION_ZIP"
  value: "bamoe_business_central_distribution.zip"
- name: "BUSINESS_CENTRAL_DISTRIBUTION_EAP"
  value: "jboss-eap-7.4"
ports:
- value: 8001
artifacts:
- name: "bamoe_business_central_distribution.zip"
  # bamoe-8.0.5.redhat-211006-business-central-eap7-deployable.zip
  md5: "4194d7aa9613a52a1c5045e0236f94d5"
run:
  user: 185
  cmd:
  - "/opt/eap/bin/standalone.sh"
  - "-b"
  - "0.0.0.0"
  - "-c"
  - "standalone.xml"
execute:
- script: "install"

```

In the file above we set the most important configurations to which defines:
* the product
* the product version
* the product zip artifact, which will be deployed on the final OpenShift image.

Note the *BUSINESS_CENTRAL_DISTRIBUTION.ZIP* env, its value will be the artifact name that, when the build is completed,
is placed in the `/<images_source_dir>/rhpam-7-openshift-image/businesscentral/target/image` directory,
note that it is on the rpam-7-openshift-image repository. The **module.yaml** file above is a example of
the base module used to configure the product bits into the final OpenShift image. All OpenShift images have a
similar module descriptor, and these modules (the base modules) are one of the first module listed in the
 *-openshift image’s* image.yaml file.



This repo is used to build the final OpenShift images, it contains the CeKit module that holds the information 
about the version and the binary that ill be installed in the Container Image, each module contains:
* _install_: The script that will install the binary in the desired location and configure it properly.
* _ module.yaml_: The module definition, it holds the version, labels and binary information, this module needs to be \
updated for every new release.



### Update versions

Before each release, there is a need to update the product version on each repository that composes the Container
Images.
In this repo you will find the **scripts** directory which containers the `update-version.py` script which helps to
update the version to the next release interation smoothly.

This script requires python 3.


See its usage:
```bash
$ python scripts/update-version.py --help
usage: update-version.py [-h] [-v T_VERSION] [--confirm]

RHDM Version Manager

optional arguments:
  -h, --help    show this help message and exit
  -v T_VERSION  update everything to the next version
  --confirm     if not set, script will not update the rhdm modules. (Dry run)
```


There is two options to run it, a dry-run option which will only print for you the changes, useful if you want just to see
how the changes will looks like after the script is executed, and if the chages are correct, the `--confirm` flag
should be used.

 ##### Found an issue?
 Please submit an issue [here](https://issues.jboss.org/projects/KIECLOUD) or send us a email: bsig-cloud@redhat.com.

