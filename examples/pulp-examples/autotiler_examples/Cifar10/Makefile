# Copyright (C) 2017 GreenWaves Technologies
# All rights reserved.

# This software may be modified and distributed under the terms
# of the BSD license.  See the LICENSE file for details.

pulpChip = GAP
PULP_APP = Cifar10
TILER_PATH = $(GAP_SDK_HOME)/tools/autotiler
TILER_LIB = $(TILER_PATH)/lib/libtile.a
TILER_INC = $(TILER_PATH)/include
TILER_GENERATOR_PATH = $(TILER_PATH)/generators
CIFAR_GEN_PATH = $(TILER_GENERATOR_PATH)/CNN/generator
CIFAR_KER_PATH = $(TILER_GENERATOR_PATH)/CNN/kernels

PULP_APP_SRCS = Cifar10.c Cifar10Kernels.c Cifar10KernelsInit.c $(CIFAR_KER_PATH)/src/CNN_BiasReLULinear_BasicKernels.c $(CIFAR_KER_PATH)/src/CNN_Conv_BasicKernels.c $(CIFAR_KER_PATH)/src/CNN_Pooling_BasicKernels.c
PULP_APP_SRCS += $(CIFAR_KER_PATH)/src/CNN_HwCE.c
RM=rm -f

#There is no HWCE in the new autotiler lib for now
USE_HARDWARE_CE = -DRT_HAS_HWCE=1

PULP_CFLAGS += -O3 $(USE_HARDWARE_CE)
PULP_CFLAGS += -mno-memcpy -fno-tree-loop-distribute-patterns  -fdata-sections -ffunction-sections
PULP_LDFLAGS +=  -flto -Wl,--gc-sections
# The generated code outputs a maybe-uninitialized error which is rather difficult to suppress
# in a clean way.
PULP_CFLAGS += -Wall -Wno-maybe-uninitialized -Wno-unused-but-set-variable
PULP_CFLAGS += -I$(TILER_INC) -I$(CIFAR_KER_PATH)/include

# The double colon allows us to force this to occur before the imported all target
# Link model generation to all step
all:: model

# Build the code generator
GenTile:
	gcc -o GenCifar10 $(USE_HARDWARE_CE) -I$(TILER_INC) -I$(CIFAR_GEN_PATH)/include Cifar10Model.c $(CIFAR_GEN_PATH)/src/CNN_Generators.c $(TILER_LIB)

# Run the code generator
Cifar10Kernels.c: GenTile
	./GenCifar10

model: Cifar10Kernels.c

clean::
	$(RM) GenCifar10 Cifar10Kernels.* Cifar10KernelsInit.*

.PHONY: model clean

include $(GAP_SDK_HOME)/tools/rules/pulp_rules.mk

