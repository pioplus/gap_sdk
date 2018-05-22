[![GreenWaves Technologies][GWT-logo]][GWT-link]

[GWT-logo]: https://github.com/GreenWaves-Technologies/gap_sdk/blob/master/logo.png
[GWT-link]: https://greenwaves-technologies.com/

# Setting up the GAP8 SDK

## About

The GAP8 SDK allows you to compile and execute applications on the GAP8 IoT Application Processor. This SDK is an extract of the necessary elements from the pulp-sdk (https://github.com/pulp-platform/pulp-sdk) produced by the PULP project, to provide a development environment for the GAP8 series processors.

We provide you with all the necessary tools and two different operating systems for GAP8:

*   Tools
    -   GAP8 RISCV GNU toolchain: a pre-compiled toolchain inherited from RISC V project with support for our extensions to the RISC-V Instruction Set Architecture.
    -   PLPBRIDGE: a tool from the PULP project which allows communication between your PC and a GAP8 processor. Using this tool you can:
        * Program / control GAP8
        * Debug your application using GDB
        * Program the GAPuino flash memory with applications

*   Operating Systems
    -   PULP OS - The open source embedded RTOS produced by the PULP project
    -   Arm® Mbed™ OS - Arm Mbed OS is an open source embedded operating system. GreenWaves Technologies has ported it to GAP8.

##  Getting started with the GAP8 SDK

In the first release of the SDK we officially support Ubuntu 16.04 64 bit only.

These instructions were developed using a fresh Ubuntu 16.04 Xenial 64-Bit virtual machine from https://www.osboxes.org/ubuntu/#ubuntu-16-04-info

The following packages needed to be installed:

~~~~~shell
sudo apt-get install -y build-essential git libftdi-dev libftdi1 doxygen python3-pip libsdl2-dev
sudo ln -s /usr/bin/libftdi-config /usr/bin/libftdi1-config
~~~~~

The debug bridge uses a python3 application. Python3 is already included in the Ubuntu image above however one extra package was required:

~~~~~shell
pip3 install pyelftools
~~~~~

For the USB serial connection GAPuino uses an FDDI 2 port serial to USB controller. This needs to be set up (the driver is installed in the apt-get install step above).

Add your username to the dialout group:

~~~~~shell
sudo usermod -a -G dialout <username>
~~~~~

Logout from your session and login again. Now create a udev rule for gapuino ftdi2232 usb chip

~~~~~shell
touch 90-ftdi_gapuino.rules
echo 'ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6010", MODE="0666", GROUP="dialout"'> 90-ftdi_gapuino.rules
sudo mv 90-ftdi_gapuino.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules && sudo udevadm trigger
~~~~~

If you are using a Virtual Machine make sure that you give control of the FTDI device to your virtual machine. Plug the GAPuino into your USB port and then allow the virtual machine to access it. For example, for VirtualBox go to Devices->USB and select the device.

Please also make sure that your Virtual Machine USB emulation matches your PC USB version. A mismatch causes the USB interface to be very slow.

The following instructions assume that you install the GAP SDK into your home directory. If you want to put it somewhere else then please modify them accordingly.

Now clone the GAP8 SDK and the GAP8/RISC-V toolchain:

~~~~~shell
git clone https://github.com/GreenWaves-Technologies/gap_sdk.git
git clone https://github.com/GreenWaves-Technologies/gap_riscv_toolchain.git
~~~~~

Install the toolchain:

~~~~~shell
cd ~/gap_riscv_toolchain
./install.sh
~~~~~

Initialize and download all the sub projects of the SDK:

~~~~~shell
cd ~/gap_sdk
git submodule update --init --recursive
~~~~~

Build the GAP8 SDK:

~~~~~shell
make all
~~~~~

The following command configures the shell environment correctly for the GAP8 SDK. It must be done for each terminal session:

~~~~~shell
source ~/gap_sdk/sourceme.sh
~~~~~

Tip: You can add an "alias" command as following in your .bashrc file:

~~~~~shell
alias GAP_SDK='cd ~/gap_sdk && source sourceme.sh'
~~~~~

Typing GAP_SDK will now change to the gap_sdk directory and execute the source command.

Finally try a test project:

~~~~~shell
cd ~/gap_sdk/examples/pulp-examples/helloworld
make clean all run
~~~~~

After the build you should see:
~~~~~
Entering main controller
Entering cluster on core 0
There are 8 cores available here.
[clusterID: 0x 0] Hello from core 0
[clusterID: 0x 0] Hello from core 1
[clusterID: 0x 0] Hello from core 2
[clusterID: 0x 0] Hello from core 3
[clusterID: 0x 0] Hello from core 4
[clusterID: 0x 0] Hello from core 5
[clusterID: 0x 0] Hello from core 6
[clusterID: 0x 0] Hello from core 7
Leaving cluster on core 0
[clusterID: 0x20] Hello from core 0
Test success: Leaving main controller
Detected end of application, exiting with status: 0
~~~~~

## Debugging Programs

The JTAG bridge allows for printf's in your code to generate messages on your PC. GAP8 also has support for gdb server over JTAG for both pulp-os and mbed-os programs.

To enable gdbserver run mode execute the make target gdbserver:

~~~~~shell
make clean all gdbserver
~~~~~

The program will load and gdbserver will be started. Connect to the target with gdb from another shell:

~~~~~shell
riscv32-unknown-elf-gdb BUILD/GAP8/GCC_RISCV/test
~~~~~

Once gdb has loaded connect to the gdbserver on the target:

~~~~~
(gdb) target remote localhost:1234
Remote debugging using localhost:1234
~~~~~

## Documentation

You can read the documentation by opening gap_doc.html in the docs folder in your browser:

~~~~~shell
firefox docs/gap_doc.html
~~~~~

If you would like PDF versions of the reference manuals you can do:

~~~~~shell
cd docs
make pdf
~~~~~

## Getting help

Please log any issues you have with the SDK in the github project.

We have also created a Google group for you to ask questions and find out more about the GAP8 SDK. You can join this group at https://bit.ly/2L36qLJ

## Trademarks

Arm® Mbed™ OS are registered trademarks or trademarks of Arm Limited (or its subsidiaries) in the US and/or elsewhere.
