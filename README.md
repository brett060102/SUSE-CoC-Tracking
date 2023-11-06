# SUSE-CoC-Tracking
Tools for validating Hardening of SUSE cloud images.

## Overview
The provided tools will validate that a newly instantiated image
matches the results of the selected security profile for that image
and that after applying remediation for that profile, the resulting
system state matches the expected post remediation state.  Only the
Center for Internet Security (CIS) Level 1 is supported at this time.

The tool first runs the oscap tool with the CIS Level 1 profile and records
the result in the ./results directory.

The tool then performs two remediation runs using the CIS Level 1 profile.
Two runs are required, because some items that are corrected in the first
run enable additional items to be corrected in the second run.

The tool then performs a final check using the CIS Level 1 profile and
stores the result in the ./results directory.

The tool then compares the test results against the provided baselines
and reports an error if there are any differences.

## Requirements
Cloud VM instance on a Public Cloud Service Provider 
(currently only tested with Microsoft Azure) created from a
hardened SUSE image (has only been tested with 
suse:sles-sap-15-sp4-hardened:gen2:2023.08.28).

Image must be set up for passwordless ssh access.

## Steps to Perform Test
### step 1: clone repository on a system that can ssh to VM to be tested.
$ git clone https://github.com/brett060102/SUSE-CoC-Tracking.git

### step 2: cd to repository
$ cd SUSE-CoC-Tracking

### step 3: invoke bin/test-system with image URN and user@ip to remote system
i.e. $ ./bin/test-system suse:sles-sap-15-sp4-hardened:gen2:2023.08.28 azureuser@74.235.165.168

The list of available baselines are in ./baseline currently just suse:sles-sap-15-sp4-hardened:gen2:2023.08.28
Image URNs are specific to Microsoft Azure and can be found using "PINT - The Public Cloud Information Tracker"
at https://pint.suse.com/?resource=images&csp=microsoft

## Running Checks Locally on Test system
The hardening checks can be run on the system to be tested directly, First the software must be present
on the test system.This can be done by using "git archive" to copy the software to the system to be tested.

Install git if needed<br>
$ sudo zypper refresh<br>
$ sudo zypper install git-core<br>

Clone and copy to remote system<br>
$ git clone https://github.com/brett060102/SUSE-CoC-Tracking.git<br>
$ cd SUSE-CoC-Tracking<br>
$ git archive --format=tar main | ssh azureuser@IP_ADDRESS "(mkdir -p ./SUSE-CoC-Tracking; cd ./SUSE-CoC-Tracking; tar -xvf-)"<br>

Then ssh to remote system and run test<br>
$ ssh azureuser@IP_ADDRESS<br>
$ cd SUSE-CoC-Tracking<br>
$ ./bin/test-system suse:sles-sap-15-sp4-hardened:gen2:2023.08.28<br>

Or the git repository may be directly "cloned" to the remote system to be tested.
This does require having git installed on the remote system. Installing git on the remote system, does not
change the results of the initial system status check.

On system to be tested:

Install git if needed<br>
$ sudo zypper refresh<br>
$ sudo zypper install git-core<br>

Clone repository<br>
$ git clone https://github.com/brett060102/SUSE-CoC-Tracking.git

Run test<br>
$ cd ./SUSE-CoC-Tracking<br>
$ ./bin/test-system suse:sles-sap-15-sp4-hardened:gen2:2023.08.28<br>

## Checking Results Manually
The results of a run may also be checked manually.

$ cd ./SUSE-CoC-Tracking<bt>
Run compare tool:<br>
$ bin/compare-results suse:sles-sap-15-sp4-hardened:gen2:2023.08.28<br>

There should be no differences reported.