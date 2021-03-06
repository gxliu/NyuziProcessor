# Nyuzi Processor

Nyuzi is an experimental multicore GPGPU processor implemented in 
SystemVerilog. It supports vector floating point, fine grained hardware 
multithreading, and a coherent L1/L2 cache hierarchy. It is fully 
synthesizable and has been validated on FPGA. This project also includes 
a C++ toolchain based on LLVM, an emulator, software libraries, and RTL 
verification tests. It is useful as a platform for microarchitecture 
experimentation, performance modeling, and parallel software development.   

License: Apache 2.0    
Documentation: https://github.com/jbush001/NyuziProcessor/wiki  
Mailing list: https://groups.google.com/forum/#!forum/nyuzi-processor-dev  
Blog: http://latchup.blogspot.com/

# Getting Started

These instructions explain how to get the design working in Verilog simulation.
This environment allows cycle-accurate modeling of the hardware without an FPGA. 

## Required Software

The following sections explain how to install these packages.

- GCC 4.8+ or Apple Clang 4.2+
- Python 2.7
- [Verilator 3.864+](http://www.veripool.org/projects/verilator/wiki/Installing).  
- Perl 5.x+ (required by Verilator)
- Nyuzi cross compiler toolchain: https://github.com/jbush001/NyuziToolchain 
- libsdl 2.0
- ImageMagick

## Optional Software:

- Emacs v23.2+, for 
   [AUTOWIRE/AUTOINST](http://www.veripool.org/projects/verilog-mode/wiki/Verilog-mode_veritedium).
- Java (J2SE 6+) for visualizer app 
- [GTKWave](http://gtkwave.sourceforge.net/) for analyzing waveform files 

## Building on Linux

First, build the Nyuzi toolchain following instructions in https://github.com/jbush001/NyuziToolchain 

Next, you will need Verilator.  Many package managers have Verilator, but it 
may be out of date. It can be installed as follows (assuming Ubuntu):

    sudo apt-get install verilator
    verilator --version.

Bug fixes in at least version 3.864 are necessary for it to run properly 
(some of the bugs are subtle, so it may appear to work at first but then 
fail in odd ways if you are out of date). If you don't have a recent 
version, build from source using these instructions:

http://www.veripool.org/projects/verilator/wiki/Installing

On Linux, you can install the remaining dependencies using the built-in 
package manager (apt-get, yum, etc). I've only tested this on Ubuntu, for 
which the instructions are below. You may need to tweak the package names 
for other distros:

    sudo apt-get install gcc g++ python perl emacs openjdk-7-jdk gtkwave imagemagick libsdl2-dev

    git clone git@github.com:jbush001/NyuziProcessor.git
    cd NyuziProcessor
    make
    make test
    
To run 3D renderer (in emulator)

    cd software/apps/sceneview
    make run
    

## Building on MacOS

On Mavericks and later, the command line compiler can be installed by typing

    xcode-select --install 
    
It will also be installed automatically if you download XCode from the Mac App Store.

Build the Nyuzi toolchain following instructions in https://github.com/jbush001/NyuziToolchain 

You will need to build Verilator from source using instructions here:

http://www.veripool.org/projects/verilator/wiki/Installing

MacOS has many of the required packages by default, the exceptions being
Imagemagick and SDL. To install the remaining packages, I would recommend
a package manager like [MacPorts](https://www.macports.org/). The command
line for that would be:

    sudo port install imagemagick libsdl2

    git clone git@github.com:jbush001/NyuziProcessor.git
    cd NyuziProcessor
    make
    make test

To run 3D renderer (in emulator)

    cd software/sceneview
    make run

## Building on Windows

I have not tested this on Windows. Many of the libraries are already cross platform, so
it should theoretically be possible.

# Running on FPGA

This currently only works under Linux.  It uses Terasic's [DE2-115 evaluation board](http://www.terasic.com.tw/cgi-bin/page/archive.pl?Language=English&No=502).
In addition to the packages listed above, this also requires 
[Quartus II FPGA design software](http://dl.altera.com/?edition=web).

## Setup

1. This loads programs onto the board over the serial port, so your development
machine must be connected to the FPGA board using a serial cable. 

2. The environment variable SERIAL_PORT must be set to the path to the serial 
device. For a Prolific USB based dongle, for example, the path is. 

        export SERIAL_PORT="/dev/ttyUSB0"

    For a different serial device, you will need to figure
    out the device path.

3. Ensure you can access the serial port without being root:

        sudo usermod -a -G dialout $USER
    
4. Make sure the FPGA board is in JTAG mode by setting SW19 to 'RUN'

On some distributions of Linux, the Altera tools have trouble talking to USB if not 
run as root. This can be remedied by creating a file 
/etc/udev/rules.d/99-custom.rules and adding the following line:

    ATTRS{idVendor}=="09fb" , MODE="0660" , GROUP="plugdev" 

## Running

The build system is command line based and does not use the Quartus GUI.

1. Synthesize the design (ensure quartus binary directory is in your PATH, by
   default installed in ~/altera/[version]/quartus/bin/)

        cd rtl/fpga/de2-115
        make

2. Load the configuration bitstream onto the FPGA.

        make program 

3. Press key 0 on the lower right hand of the board to reset the processor
4. Load program into memory and execute it using the runit script as below.

        cd ../../../tests/fpga/blinky
        ./runit.sh

Programs can be reloaded by repeating steps 3 & 4. The bitstream does not need
to be reloaded as long as the board is powered (it will be lost if it is turned off,
however). 



