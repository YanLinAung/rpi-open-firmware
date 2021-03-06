
TARGET_ARM_CHAINLOADER = arm_chainloader.bin

SRCS = \
	start.s \
	drivers/uart.c \
	../lib/xprintf.c \
	firmware_rendezvous.c \
	main.c

ARCH = armv6zk

BUILD_DIR = build
TARGET_BUILD_DIR = $(BUILD_DIR)/$(ARCH)-objects
PRODUCT_DIRECTORY = $(BUILD_DIR)

NO_COLOR=""
OK_COLOR=""
ERROR_COLOR=""
WARN_COLOR=""

.PHONY: default all clean create_build_directory device

default: $(TARGET_ARM_CHAINLOADER)

OBJ := $(addprefix $(TARGET_BUILD_DIR)/, $(addsuffix .o, $(basename $(SRCS))))

CROSS_COMPILE = arm-none-eabi-
CC = $(CROSS_COMPILE)gcc
AS = $(CC)
OBJCOPY = $(CROSS_COMPILE)objcopy
LINKFLAGS = -nostdlib -march=$(ARCH) -Wl,--build-id=none -T linker.lds
COMMON_FLAGS = -c -nostdlib -nostartfiles -ffreestanding -march=$(ARCH) -I../ -I./ -mfpu=vfp -mfloat-abi=hard -mtune=arm1176jzf-s
CFLAGS = $(COMMON_FLAGS) -std=c11 
ASFLAGS = $(COMMON_FLAGS) -x assembler-with-cpp

HEADERS := \
	$(shell find . -type f -name '*.h') \
	$(shell find . -type f -name '*.hpp')

create_build_directory:
	@mkdir -p $(TARGET_BUILD_DIR)
	@mkdir -p $(PRODUCT_DIRECTORY)

CREATE_SUBDIR = \
	@DIR="$(dir $@)"; \
	if [ ! -d $$DIR ]; then mkdir -p $$DIR; fi

#
# rules to build c/asm files.
# 
$(TARGET_BUILD_DIR)/%.o: %.c $(HEADERS)
	$(CREATE_SUBDIR)
	@echo $(WARN_COLOR)CC  $(NO_COLOR) $@
	@$(CC) $(CFLAGS) $< -o $@

$(TARGET_BUILD_DIR)/%.o: %.s $(HEADERS)
	$(CREATE_SUBDIR)
	@echo $(WARN_COLOR)AS  $(NO_COLOR) $@
	@$(AS) $(ASFLAGS) $< -o $@

.PRECIOUS: $(OBJ)

$(TARGET_ARM_CHAINLOADER): create_build_directory $(OBJ)
	@echo $(WARN_COLOR)LD  $(NO_COLOR) $@.elf
	@$(CC) $(LINKFLAGS) $(OBJ) -o $(PRODUCT_DIRECTORY)/$@.elf -lgcc
	@echo $(WARN_COLOR)OBJ$(NO_COLOR) $@
	@$(OBJCOPY) -O binary $(PRODUCT_DIRECTORY)/$@.elf $(PRODUCT_DIRECTORY)/$@

clean:
	@echo $(ERROR_COLOR)CLEAN$(NO_COLOR)
	@-rm -rf ./$(BUILD_DIR)