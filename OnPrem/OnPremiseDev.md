# On-Premise Development and F1 Deployment Guide

The SDAccel flow for F1 supports the following development models:
- Cloud based development on AWS EC2 cloud instances
- On-premise development on your own local workstations

In both cases, the final executables are deployed and executed on an AWS F1 instance. 

This guide provides step-by-step instructions for getting started with the on-premise flow and covers the following:
1. Installing and licensing SDAccel in your own environment
2. Building your design on-premise with SDAccel
3. Executing your design on F1

## Prequisites
Before going through the steps described in this document, the user should have completed at least once the steps described in the [AWS SDAccel README file](https://github.com/aws/aws-fpga-preview/blob/master/sdk/SDAccel/README.md).

## Requirements
The supported Operating Systems for SDaccel On-premise development are:
- Red Hat Enterprise Workstation/Server 7.2 and 7.3 (64-bit)
- Red Hat Enterprise Workstation 6.7 and 6.8 (64-bit)
- CentOS 6.8, CentOS 7.3 (64-bit)
- Ubuntu Linux 16.04.1 LTS (64-bit)

# 1. Installing and licensing SDAccel in your own environment

## Downloading the SDAccel Development Environment
In order to develop any SDAccel application on-premise, you will need to install the same version of SDAccel as deployed on AWS F1. The download link can be found here:
https://www.xilinx.com/member/forms/download/xef.html?filename=Xilinx_SDx_op_2017.1_sdx_0715_1_Lin64.bin&akdm=0

This requires a Xilinx login. If you do not have an existing Xilinx account, select "Create your account".

## Requesting a license

New Xilinx users will also need to obtain an on-premises license of Vivado. Follow these [instructions][Vivado download] to request for a node-locked or floating license: 

NEED TO CLARITY THIS PROCEDURE

## Cloning the aws-fpga Git repository
The AWS Github repository contains all the necessary platform definition files and setup scripts to run SDAccel and build a design for F1 instances. It also contains numerous examples that will help you learn more about SDAccel.  

Execute the following command on your local machine to clone the Github repository and configure the SDAccel environment:
```
    $ git clone https://github.com/aws/aws-fpga-preview.git
    $ cd aws-fpga-preview                                      
    $ source sdaccel_setup.sh
```

**Note**: Sourcing sdaccel_setup.sh may show some errors as it also tries to install runtime drivers which requires sudo access. These errors are nonintrusive, and you can ignore these error messages. 


# 2. Building your design on-premise with SDAccel

Once you have completed the previous step , the following commands will download the AWS F1 platform description to your local host.
 
THOMAS: This needs to go elsewhere... This guide uses an existing example to step you through the entire on-premise development flow. The example used in this guide is the Vector Addition with RTL Kernel example from SDAccel Github.  

This step will show you how to:
- Confirm you are able to run SDAccel on your local machine
- Generate executables which you can then deploy on the F1 instance.

The example used in this guide is complete and no further develoment is required,

You can run the exact same steps and command on-premise as on an AWS EC2 instance. 

## Starting the GUI
This step shows how to invoke the GUI. 
To run the SDAccel GUI do...

The following tsteps show how run from the command line...

## Running SW Emulation

The main goal of SW emulation is to ensure functional correctness and to partition the application into kernel(s) vs host. For CPU-based (SW) emulation, both the host code and the kernel(s) code are compiled to run on an x86 processor. The SW Emulation enables developer to iterate and refine the algorithms through fast compile, and iteration time is similar to software compile and run cycle on CPU.

The instructions below describe how to get started on SDAccel development using the SW Emulation:

```
    $ cd sdk/SDAccel/examples/xilinx/getting_started/host/helloworld_ocl/
    $ make clean
    $ make TARGETS=sw_emu DEVICES=$AWS_PLATFORM all
```

## Running HW Emulation

The SDAccel hardware emulation flow enables the developer to check the correctness of the logic generated for the custom kernels. This emulation flow invokes the hardware simulator in the SDAccel environment to test the functionality that will be executed on FPGA Custom Logic.

The instructions below describe how to get started on SDAccel development using the HW Emulation:

```
    $ cd sdk/SDAccel/examples/xilinx/getting_started/host/helloworld_ocl/
    $ make clean
    $ make TARGETS=hw_emu DEVICES=$AWS_PLATFORM all
```

## Building for HW Execution

The SDAccel build flow enables the developer to compile custom kernels.

The instructions below describe how to build for AWS FPGA Hardware:

```
    $ cd sdk/SDAccel/examples/xilinx/getting_started/host/helloworld_ocl/
    $ make clean
    $ make TARGETS=hw DEVICES=$AWS_PLATFORM all
```

The build process will generate the Host and Kernel binaries. 
Host executable: ./host.exe "UPDATE: is this the correct name, was only host"
Kernel executable: ./xclbin/vadd.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin

The binaries need to be uploaded to an AWS F1 instance to be executed.

# 3. Executing your design on F1

To execute a design build on-premise on F1 you first need to upload it to the AWS cloud. After that the steps are identical as if you had developped your design in the clound to begin with.

In this section, we will explain how to upload the design to the AWS cloud and link to the AWS SDAccel REAMDE for instructions on how to create an Amazon FPGA Image and execute your design

## Uploading the host and kernel binaries to AWS
COMMENT: probably needs more details here...
Upload data to the F1 instance, using scp with the same elements as the SSH login:
```
    $ scp -i ~/<pem-name>.pem <from-file> <login-id>@<host-ip>:/home/<login-id>/<to file>
```

- pem-name is "UPDATE: explain"
- from-file is "UPDATE: explain , should we have two lines each with the file already filled in"
- login-id is "UPDATE: explain"
- host-ip is "UPDATE: explain"
- to-file is "UPDATE: explain , should we have two lines each with the file already filled in"

## Executing your design on F1

[Instructions on how to create and AFI, ...](https://github.com/aws/aws-fpga-preview/blob/master/sdk/SDAccel/README.md#create-an-amazon-fpga-image-afi-for-your-kernel)

	
# Additional Resources

The [AWS SDAccel README].

Xilinx web portal for [Xilinx SDAccel documentation] and for [Xilinx SDAccel GitHub repository]

Links pointing to **latest** version of the user guides
1. [UG1023: SDAccel Environment User Guide][latest SDAccel Environment User Guide]
1. [UG1021: SDAccel Environment Tutorial: Getting Started Guide (including emulation/build/running on H/W flow)][latest UG1021]
1. [UG1207: SDAccel Environment Optimization Guide][latest SDAccel Environment Optimization Guide]
1. [UG949: UltraFast Design Methodology Guide for the Vivado Design Suite][latest UG949]

Links pointing to **2017.1** version of the user guides
1. [UG1023: SDAccel Environment User Guide][UG1023 2017.1]
1. [UG1021: SDAccel Environment Tutorial: Getting Started Guide (including emulation/build/running on H/W flow)][UG1021 2017.1]
1. [UG1207: SDAccel Environment Optimization Guide][UG1207 2017.1]
1. [UG1238: SDx Development Environment Release Notes, Installation, and Licensing Guide][UG1238 2017.1]

[SDAccel_landing_page]: https://www.xilinx.com/products/design-tools/software-zone/sdaccel.html
[VHLS_landing_page]: https://www.xilinx.com/products/design-tools/vivado/integration/esl-design.html
[Vivado_landing_page]: https://www.xilinx.com/products/design-tools/vivado.html

[latest SDAccel Environment User Guide]: https://www.xilinx.com/cgi-bin/docs/rdoc?v=latest;d=ug1023-sdaccel-user-guide.pdf
[latest UG1021]: https://www.xilinx.com/cgi-bin/docs/rdoc?v=latest;d=ug1021-sdaccel-intro-tutorial.pdf
[latest SDAccel Environment Optimization Guide]: https://www.xilinx.com/cgi-bin/docs/rdoc?v=latest;d=ug1207-sdaccel-optimization-guide.pdf
[latest UG949]: https://www.xilinx.com/cgi-bin/docs/rdoc?v=latest;d=ug949-vivado-design-methodology.pdf
[latest UG902]: https://www.xilinx.com/cgi-bin/docs/rdoc?v=latest;d=ug902-vivado-high-level-synthesis.pdf

[UG1023 2017.1]: https://www.xilinx.com/support/documentation/sw_manuals/xilinx2017_1/ug1023-sdaccel-user-guide.pdf
[UG1021 2017.1]: https://www.xilinx.com/support/documentation/sw_manuals/xilinx2017_1/ug1021-sdaccel-intro-tutorial.pdf
[UG1207 2017.1]: https://www.xilinx.com/support/documentation/sw_manuals/xilinx2017_1/ug1207-sdaccel-optimization-guide.pdf
[UG1238 2017.1]:http://www.xilinx.com/support/documentation/sw_manuals/xilinx2017_1/ug1238-sdx-rnil.pdf
[Xilinx SDAccel documentation]: https://www.xilinx.com/products/design-tools/software-zone/sdaccel.html#documentation
[Xilinx SDAccel GitHub repository]: https://github.com/Xilinx/SDAccel_Examples

[SDAccel download and License instructions]:https://github.com/aws/aws-fpga/blob/master/hdk/docs/on_premise_licensing_help.md
[Vivado download]:https://www.xilinx.com/products/design-tools/acceleration-zone/ef-vivado-sdx-vu9p-op-fl-nl.html
[SDAccel Download Page]: https://www.xilinx.com/registration/sign-in.html?oamProtectedResource=wh%3Dwww.xilinx.com%20wu%3D%2Fmember%2Fforms%2Fdownload%2Fxef.html%3Ffilename%3DXilinx_SDx_op_2017.1_sdx_0715_1_Lin64.bin%26akdm%3D0%20wo%3D1%20rh%3Dhttp%3A%2F%2Fwww.xilinx.com%20ru%3D%252Fmember%252Fforms%252Fdownload%252Fxef.html%20rq%3Dfilename%253DXilinx_SDx_op_2017.1_sdx_0715_1_Lin64.bin%2526akdm%253D0
[AWS SDAccel Readme]: ../README.md




# BACKUP

## Creating an Amazon FPGA Image (.awsxclbin)

At this point in the flow, the steps are the same as for cloud-based development. An Amazon FPGA Image needs to be created...



Once you have logged into the AWS F1 instance, use the following script and options your create an Amazon FPGA Image from your FPGA binary file (.xclbin) 

```
    $ SDACCEL_DIR/tools/create_sdaccel_afi.sh \
        -xclbin=vadd.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin \
        -o=<output_awsxclbin_filename> \
        -s3_bucket=<bucket-name> \
        -s3_dcp_key=<dcp-folder-name> \
        -s3_log <logs-folder-name>
```
- output_awsxclbin_filename is "UPDATE: explain"
- bucket-name is "UPDATE: explain"
- dcp-folder-name is "UPDATE: explain"
- logs-folder-name is "UPDATE: explain"

This command will generate the AWS binary file (.awsxclbin) file and an AFI (Amazon FPGA Image) ID file containing the AFI ID. The AFI ID is used to check the completion of the AFI generation process (as shown in the next step).  

## Prepare the executable file 	

The first step is to rename the newly created AWS binary back to the name of the SDAccel executable. 
- SDAccel Environment expects a file with a .xclbin file extension. 
- Your original SDAccel binary file (.xclbin) was copied into the AWS secured storage area as part of the previous step. 
- The newly generated AWS binary file (.awsxclbin) simply contains a link to your original file in the secured storage area.
- Using the newly created AWS binary (.awsxclbin) in place of the original SDAccel binary, ensures your actual IP never leaves the AWS secured storage area.

Rename the .awsxclbin to original .xclbin file.

> mv <generated>.awsxclbin vadd.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin

- awsxclbin_filename is the name you used in the previous step

the next step is to prepare a shell script, test.sh in this example, to execute the host executable. 
"UPDATE: we create a test file but then we don't use it???"
"UPDATE: needs clearer instructions"



c) Check Status of AFI generation process using AFI-ID
cat <timestamp>_afi_id.txt # Note the AFI-ID from this command 

#Checking the status
aws ec2 describe-fpga-images --fpga-image-ids <AFI ID> 
	
A finished FPGA image creation job will show "Available" State
	"State: { 
		   "Code" : Available" 
		}

		
## Executing the application on the F1 instance

Once available execute the application on AWS F1 instance
	
```
    $ sudo sh
    $ source /opt/Xilinx/SDx/2017.1.rte/setup.sh
    $ ./host
```

Device/Slot[0] (/dev/xdma0, 0:0:1d.0)
xclProbe found 1 FPGA slots with XDMA driver running
INFO: Importing ./vadd.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin
INFO: Loaded file
INFO: Created Binary
INFO: Built Program
All Device results match CPU results! 
TEST PASSED.	


