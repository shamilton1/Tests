high performance matrix multiplication
======================

This README file contains the following sections:

1. OVERVIEW
2. HOW TO DOWLOAD THE REPOSITORY
3. SOFTWARE TOOLS AND SYSTEM REQUIREMENTS
4. DESIGN FILE HIERARCHY
5. COMPILATION AND EXECUTION ON FPGA ACCELERATOR CARD
6. EXECUTION IN CLOUD ENVIRONMENTS
7. SUPPORT
8. LICENSE AND CONTRIBUTING TO THE REPOSITORY
9. ACKNOWLEDGEMENTS
10. REVISION HISTORY


## 1. OVERVIEW

This example implements a high performance matrix multiplication of
two input matrices (A*B=C). The matrix multiplication kernel operates
on matrices of type int16 and produces int16 results. Internally, the
kernel has a systolic array of 2048 DSP units and is attached to two
DDR banks. The DSP array runs at 400 MHz whereas the logic around the
array runs at 300 MHz.

The design is targeting exection on an SDAccel supported FPGA
acceleration card. The hostcode is compiled into the
"high_perf_mat_mult" executable. The executable takes 3 arguments,
namely number of rows of matrix A, number of columns of matrix B, and
the common dimension representing number of columns in matrix A and
number of rows in matrix B.

```
high_perf_mat_mult <rowsA> <colsB> <commonDim>
```

The testbench of the example reports the kernel execution time, the
total number of operations (sum of matrix element multiplications and
additions), as well as the efficiency expressed by number of
operations per second. Please note, the testbench also compares the
kernel results with a pure sofware matrix multiplication and reports
potential differences.

The test is based on an encrypted RTL kernel. This kernel can also be
configured to run with int8 data values, which effectively doubles the
number of operations and throughput.


## 2. HOW TO DOWNLOAD THE REPOSITORY
To get a local copy of the SDAccel example repository, clone this repository to the local system with the following command:
```
git clone https://github.com/Xilinx/SDAccel_Examples examples
```
where examples is the name of the directory where the repository will be stored on the local system.This command needs to be executed only once to retrieve the latest version of all SDAccel examples. The only required software is a local installation of git.

## 3. SOFTWARE AND SYSTEM REQUIREMENTS
Board | Device Name | Software Version
------|-------------|-----------------
Xilinx KU115|xilinx:xil-accel-rd-ku115:4ddr-xpr|SDAccel 2017.1
AWS VU9P|xilinx:aws-vu9p-f1_4ddr-xpr-2pr:4.0|SDAccel 2017.1


*NOTE:* The board/device used for compilation can be changed by adding the DEVICES variable to the make command as shown below
```
make DEVICES=<device name>
```
where the *DEVICES* variable accepts either 1 device from the table above or a comma separated list of device names.

## 4. DESIGN FILE HIERARCHY
Application code is located in the src directory. Accelerator binary files will be compiled to the xclbin directory. The xclbin directory is required by the Makefile and its contents will be filled during compilation. A listing of all the files in this example is shown below

```
.gitignore
README.md
description.json
src
src/kernelShigh_perf_mat_mult_0.xo
src/ku115
src/ku115/ku115-constraints-pblock-1kernel.tcl
src/ku115/presynth.tcl
src/xilinx_aws-vu9p-f1
src/xilinx_aws-vu9p-f1/f1-constraints-pblock-1kernel.tcl
src/xilinx_aws-vu9p-f1/presynth.tcl
src/xilinx_aws-vu9p-f1/postopt.tcl
src/high_perf_mat_mult.cpp
Makefile
```

## 5. COMPILATION AND EXECUTION ON FPGA ACCELERATOR CARD

The command to compile the application for execution on the FPGA acceleration board is
```
make all
```
The default target for the makefile is to compile for hardware. Therefore, setting the TARGETS option is not required.
*NOTE:* Compilation for application execution in hardware generates custom logic to implement the functionality of the kernels in an application.
It is typical for hardware compile times to range from 3 to 5 hours.

One the executable and the FPGA board image (xclbin) has been created, the application can be executed to multiply two square matrixes of size 512 by typing 
```
./high_perf_mat_mult 512 512 512
```
Please refer to the next section for more details regarding running on cloud based environments.

## 6. Execution in Cloud Environments
FPGA acceleration boards have been deployed to the cloud. For information on how to execute the example within a specific cloud, take a look at the following guides.
* [AWS F1 Application Execution on Xilinx Virtex UltraScale Devices] (Coming Soon)
* [Nimbix Application Execution on Xilinx Kintex UltraScale Devices]
* [IBM SuperVessel Research Cloud on Xilinx Virtex Devices]


## 7. SUPPORT
For more information about SDAccel check the [SDAccel User Guides][]

For questions and to get help on this project or your own projects, visit the [SDAccel Forums][].

To execute this example using the SDAccel GUI, follow the setup instructions in [SDAccel GUI README][]


## 8. LICENSE AND CONTRIBUTING TO THE REPOSITORY
The source for this project is licensed under the [3-Clause BSD License][]

To contribute to this project, follow the guidelines in the [Repository Contribution README][]

## 9. ACKNOWLEDGEMENTS
This example is written by developers at
- [Xilinx](http://www.xilinx.com)

## 10. REVISION HISTORY
Date | README Version | Description
-----|----------------|------------
June2017|1.0|Initial Xilinx Release
Sept2017|1.0|Error Correction

[3-Clause BSD License]: ../../LICENSE.txt
[SDAccel Forums]: https://forums.xilinx.com/t5/SDAccel/bd-p/SDx
[SDAccel User Guides]: http://www.xilinx.com/support/documentation-navigation/development-tools/software-development/sdaccel.html?resultsTablePreSelect=documenttype:SeeAll#documentation
[Nimbix Getting Started Guide]: http://www.xilinx.com/support/documentation/sw_manuals/xilinx2016_2/ug1240-sdaccel-nimbix-getting-started.pdf
[Walkthrough Video]: http://bcove.me/6pp0o482
[Nimbix Application Submission README]: ../../utility/nimbix/README.md
[Repository Contribution README]: ../../CONTRIBUTING.md
[SDaccel GUI README]: ../../GUIREADME.md
[AWS F1 Application Execution on Xilinx Virtex UltraScale Devices]: ../../README.md
[Nimbix Application Execution on Xilinx Kintex UltraScale Devices]: ../../utility/nimbix/README.md
[IBM SuperVessel Research Cloud on Xilinx Virtex Devices]: http://bcove.me/6pp0o482
