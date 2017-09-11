# SDAccel RTL Kernel Tutorial

This tutorial explains the procedure to run the SDAccel flow using an RTL Kernel. 

The steps for using an RTL Kernel on the AWS F1 instance are:
1. Create a Xilinx Object File (.xo) from the RTL. 
1. Run SDAccel with Host code and the Xilinx Object file.
1. Executing your design on F1. 

This tutorial uses the vadd_kernel example from the Github. 

## Cloning the aws-fpga Git repository
The AWS Github repository contains the example used in this tutorial.  

Execute the following commands on your local machine to clone the Github repository and configure the SDAccel environment:
```
    $ git clone https://github.com/aws/aws-fpga-preview.git
    $ cd aws-fpga-preview                                      
    $ source sdaccel_setup.sh
```

**Note**: Sourcing sdaccel_setup.sh may show some errors as it also tries to install runtime drivers which requires sudo access. These errors are non intrusive, and you can ignore these error messages. 


# 1. Create a Xilinx Object File (.xo) from the RTL 

The process for capturing an arbitrary RTL design as a Xilinx object file is as follows:
1. The **RTL Kernel Wizard** is used to create a Vivado project with legal SDAccel top-level interfaces.
1. The user then adds their RTL to the Vivado project and connects their arbitrary RTL to the top-level interfaces.
1. The complete design is then packaged into a Xilinx object file (.xo).

This tutorial uses an example from the GitHub repository, where steps 1 and 2 are already completed. 

For more details on Step 1, a QuickTake video explaining how to use the RTL Kernel wizard is provided [here][RTL Kernel Wizard]. 

Step 2 is the standard RTL design practise of connecting the top-level of your existing RTL to the new top-level interfaces required by SDAccel (and created for you by the RTL Kernel Wizard). This may involving adding IP blocks to convert from your current interfaces to the AXI4 and AXI4-Lite interfaces on the new top-level.

## Package the RTL into a Xilinx Object File

The RTL design is packaged into a Xilinx object file (.xo) using the Vivado **package_xo** command. This command uses the following options:
- **xo_path** is the output .xo file path and name.
- **kernel_name** is name of the top-level RTL.
- **ip_directory** is the name of the RTL-IP directory
- **kernel_xml** is the name of the Kernel XML file. 

### 1. Invoke Vivado in the example directory
To begin, change directory into the **vadd_kernel** example directory.

```
    $ cd ..UDAY to where ...
    $ vivado -tcl mode
```
You are now ready to package the RTL into a Xilinx object file.

### 2. Execute the package_xo command

Before the Xilinx object file is generated, the RTL design must be packaged into a Vivado IP. The project already includes a utility Tcl script which performs this. The output of the Tcl script, the packaged IP, is saved to the directory packaged\_kernel\_ $PATH, where **$PATH** is specified by the you. This is performed by setting the value of Tcl variable **suffix**.

Within the Vivado command line interface, execute the following commands:

```

	Vivado% set suffix rtl_ip
	Vivado% source scripts/package_kernel.tcl 
	Vivado% package_xo 	-xo_path ./src/rtl_vadd.xo -kernel_name krnl_vadd_rtl -ip_directory ./packaged_kernel_rtl_ip -kernel_xml ./src/kernel.xml
	Vivado% exit
```
The Xilinx object file is now present as ./src/rtl_vadd.xo.


# Run SDAccel with Host code and the Xilinx Object file.

The host code for this example is provided in the example, src/host.cpp and src/host.h. The kernel in this example is the Xilinx file created in the previous step, ./src/rtl_vadd.xo.

The remainder of this tutorial uses the command line interface to verify the RTL kernel with the host code and build the design. If you wish to use the GUI for these steps, please refer to [Using the SDAccel GUI].

## Running SW Emulation

**When using RTL kernels, the SW Emulation flow cannot be used**. 

In SW Emulation, both the host code and the kernel coded are compiled into x86 executables. In the case of RTL kernels, there is no kernel code which cab be compile. 

For RTL kernels, verification is performed using HW Emulation.

## Running HW Emulation

The SDAccel hardware emulation flow enables the developer to check the correctness of the logic generated for the custom kernels. This emulation flow invokes the hardware simulator in the SDAccel environment to test the functionality that will be executed on FPGA Custom Logic.

The instructions below describe how to get started on SDAccel development using the HW Emulation:

```
    $ cd sdk/SDAccel/examples/xilinx/getting_started/host/helloworld_ocl/  UDAY, update path
    $ make clean
    $ make TARGETS=hw_emu DEVICES=$AWS_PLATFORM all
```
Once the verification completes, you have confirmed the correct operation of the RTL kernel with the host code.

## Building for HW Execution

The SDAccel build flow enables the developer to compile custom kernels.

The instructions below describe how to build for AWS FPGA Hardware:

```
    $ cd sdk/SDAccel/examples/xilinx/getting_started/host/helloworld_ocl/
    $ make clean
    $ make TARGETS=hw DEVICES=$AWS_PLATFORM all
```

The build process will generate the Host and Kernel binaries. 
Host executable: ./host.exe 
Kernel executable: ./xclbin/vadd.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin   UDay confirm path

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

[UG1023 2017.1]: https://www.xilinx.com/support/documentation/sw_manuals/xilinx2017_1/ug1023-sdaccel-user-guide.pdf
[UG1021 2017.1]: https://www.xilinx.com/support/documentation/sw_manuals/xilinx2017_1/ug1021-sdaccel-intro-tutorial.pdf
[UG1207 2017.1]: https://www.xilinx.com/support/documentation/sw_manuals/xilinx2017_1/ug1207-sdaccel-optimization-guide.pdf
[UG1238 2017.1]:http://www.xilinx.com/support/documentation/sw_manuals/xilinx2017_1/ug1238-sdx-rnil.pdf
[Xilinx SDAccel documentation]: https://www.xilinx.com/products/design-tools/software-zone/sdaccel.html#documentation
[Xilinx SDAccel GitHub repository]: https://github.com/Xilinx/SDAccel_Examples

[AWS SDAccel Readme]: ../README.md
[RTL Kernel Wizard]:https://www.youtube.com/watch?v=IZQ1A2lPXZk
[Using the SDAccel GUI]: ./SDAccel_GUI.md