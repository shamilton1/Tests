# 1. Setup and testing your environment

In this step you will learn how to login to AWS, start an F1 instance, source the SDAccel environment and run a simple check example to confirm everything is properly running.

## Start an AWS F1 instance
Follow the [instructions outlined in this video](https://www.xilinx.com/video/software/introduction-to-amazon-ec2-f1.html) to start an AWS F1 instance loaded with the [FPGA Developer AMI](https://aws.amazon.com/marketplace/pp/B06VVYBLZZ)

## Load the SDAccel environment on AWS F1
Follow the instructions outlined in the [AWS Github README](https://github.com/aws/aws-fpga-preview/blob/master/sdk/SDAccel/README.md). 
These instructions will show you how to:
- Clone the AWS FPGA Github repository
- Load the SDAccel environment
- Compile and run your design for Software Emulation
- Compile and run your design for Hardware Emulation
- Compile your design for Hardware Execution
- Create an Amazon FPGA Image (AFI) for your Hardware Execution build
- Execute your design with a registered AFI

