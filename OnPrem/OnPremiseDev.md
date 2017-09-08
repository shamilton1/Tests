# On-Premise Development and F1 Deployment Guide

The SDAccel flow for F1 supports the following development models:
- Cloud based development on AWS EC2 clound instances
- On-premise development on your own local workstations

In both cases, the final executables are deployed and executed on an AWS F1 instance. 

This guide provides step-by-step instructions for getting started with the on-premise flow and covers the following:
1. Installing and licensing SDAccel in your own environment
2. Building your design on-premise with SDAccel
3. Executing your design on F1

# 1. Installing and licensing SDAccel in your own environment

## Prequisites
The supported Operating Systems for SDaccel On-premise development are:
- Red Hat Enterprise Workstation/Server 7.2 and 7.3 (64-bit)
- Red Hat Enterprise Workstation 6.7 and 6.8 (64-bit)
- CentOS 6.8, CentOS 7.3 (64-bit)
- Ubuntu Linux 16.04.1 LTS (64-bit)

## Downloading the SDAccel Development Environment & License
In order to develop any SDAccel Application on-premise, you will need to install the same version of SDAccel as deployed on AWS F1. The download link can be found [here](https://github.com/aws/aws-fpga/blob/69e8d41270cb8f6506c3f25e7a20745bc52b20a1/hdk/docs/on_premise_licensing_help.md#downloading-the-development-tool-from-xilinx)

This requires a Xilinx login. If you do not have an existing Xilinx account, select "Create your account".

## Requesting a license

New Xilinx users will also need to obtain an on-premises license of Vivado. Follow these [instructions][Vivado download] to request for a node-locked or floating license: 

## Clone the aws-fpga Git repository
The AWS Github repository contains all the necessary files and script to run SDAccel and build a design for F1 instances. It also contains numerous examples that will help you learn more about SDAccel.  

Execute the following command on your local machine to clone the Github repository.
> $ git clone https://github.com/aws/aws-fpga-preview.git   "UPDATE: POINTING TO PREVIEW PAGE"



THOMAS: This needs to go elsewhere... This guide uses an existing example to step you through the entire on-premise development flow. The example used in this guide is the Vector Addition with RTL Kernel example from SDAccel Github. 

# 2. Building your design on-premise with SDAccel

    
"UPDATE: have we already done the example dirs? this was in the title previously and I removed it"

The SDAccel Environment targets a specific platform. The platform used on the AWS F1 instance must be downloaded from AWS. Once you have completed the previous step and downloaded the Git repository, the following commands will downloand the AWS F1 platform description to your local host.
 
> $ cd aws-fpga-preview                                      
> $ source sdaccel_setup.sh


**Note**: Sourcing sdaccel_setup.sh may show some errors as it also tries to install runtime drivers which requires sudo access. These errors are nonintrusive, and you can ignore these error messages. 

	
# Running On-premise
The example used in this guide is complete and no further develoment is required, however this step will:
- Confirm you are able to run SDAccel on your local machine
- Generate executables which you can then deploy on the F1 instance.

Following on from the commands issued in the previous step, execute the following commands in your local shell.
 
> cd sdk/SDAccel/examples/xilinx/getting_started/rtl_kernel/rtl_vadd

> make all TARGETS=hw DEVICES=$AWS_PLATFORM

	
The build process will generate the Host and Kernel executables. 
Host executable: ./host.exe "UPDATE: is this the correct name, was only host"
Kernel executable: ./xclbin/vadd.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin


# Uploading the Host and Kernel executables	
The next step is to transfer both the host and kernel executables to the AWS F1 instance. 		
Upload data to the F1 instance, using scp with the same elements as the SSH login:
> scp -i ~/<pem-name>.pem <from-file> <login-id>@<host-ip>:/home/<login-id>/<to file>

- pem-name is "UPDATE: explain"
- from-file is "UPDATE: explain , should we have two lines each with the file already filled in"
- login-id is "UPDATE: explain"
- host-ip is "UPDATE: explain"
- to-file is "UPDATE: explain , should we have two lines each with the file already filled in"

# Login to the F1 instance
**Note**:The remainder of this guide assumes you have already setup your AMI with CLI, S3 bucket and runtime driver. 
- If this is **not** the case, please skip the rest of this guide and review the section "Create an Amazon FPGA Image (AFI) for your kernel" in the [README.md][AWS SDAccel Readme] file for the remaining instructions.
- if your AMI is **already configured** with an S3 bucket, runtime driver and CLI, proceed with the remaining steps in this guide.

"UPDATE: need instrunctions on login link, and to use your AWS user name and pword"

# Creates an AWS FPGA binary file (.awsxclbin)
Once you have logged into the AWS F1 instance, use the following script and options your convert FPGA binary file (.xclbin) to an AWS binary file (.awsxclbin). 


> $ SDACCEL_DIR/tools/create_sdaccel_afi.sh \

> -xclbin=vadd.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin \

> -o=<output_awsxclbin_filename> \

> -s3_bucket=<bucket-name> \

> -s3_dcp_key=<dcp-folder-name> \

> -s3_log <logs-folder-name>

- output_awsxclbin_filename is "UPDATE: explain"
- bucket-name is "UPDATE: explain"
- dcp-folder-name is "UPDATE: explain"
- logs-folder-name is "UPDATE: explain"

This command will generate the AWS binary file (.awsxclbin) fil and an AFI (Amazon FPGA Image) ID file containing the AFI ID. The AFI ID is used to check the completion of the AFI generation process (as shown in the next step).  

# Prepare the executable file 	

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

source /opt/Xilinx/SDx/2017.1.rte/setup.sh
./host

c) Check Status of AFI generation process using AFI-ID
cat <timestamp>_afi_id.txt # Note the AFI-ID from this command 

#Checking the status
aws ec2 describe-fpga-images --fpga-image-ids <AFI ID> 
	
A finished FPGA image creation job will show "Available" State
	"State: { 
		   "Code" : Available" 
		}

		
Step 10: Once available execute the application on AWS F1 instance
	
[centos@ip-172-31-79-92]$ sudo ./test.sh
Device/Slot[0] (/dev/xdma0, 0:0:1d.0)
xclProbe found 1 FPGA slots with XDMA driver running
INFO: Importing ./vadd.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin
INFO: Loaded file
INFO: Created Binary
INFO: Built Program
All Device results match CPU results! 
TEST PASSED.	


	

	
	
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
