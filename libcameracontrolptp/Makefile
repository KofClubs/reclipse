
ROOT_DIR ?= .
OUT ?= ../out
OUT_DIR := ${OUT}/lib

CC := gcc
SYS := $(shell $(CC) -dumpmachine)

override INCLUDES += -I${ROOT_DIR}/include
override INCLUDES += -I${ROOT_DIR}/ports

ifneq (, $(findstring linux, $(SYS)))
# Linux
	tg_so = ${OUT_DIR}/libcameracontrolptp.so
	SHARED_LIB_FLAG := --share
	LIB_PATH :=
else ifneq (, $(findstring cygwin, $(SYS)))
# Cygwin
	tg_so = ${OUT_DIR}/libcameracontrolptp.dll
	SHARED_LIB_FLAG := --share
	LIB_PATH :=
else ifneq (, $(findstring apple, $(SYS)))
# macOS
	tg_so = ${OUT_DIR}/libcameracontrolptp.dylib
	SHARED_LIB_FLAG := -dynamiclib
	LIB_PATH := -L$(shell brew --prefix)/lib
	override INCLUDES += -I$(shell brew --prefix)/include
else
#	exit 1
endif

sources_usb:= ${ROOT_DIR}/ports/ports_usb_impl.cpp
lib_usb:= -lusb-1.0

sources_so :=
sources_so += ${sources_usb}
sources_so += ${ROOT_DIR}/ports/ports_ptp_impl.cpp
sources_so += ${ROOT_DIR}/sources/socc_ptp.cpp
sources_so += ${ROOT_DIR}/sources/parser.cpp
OBJ_DIR := .obj
OBJECTS := $(addprefix $(OBJ_DIR)/, $(notdir $(sources_so:.cpp=.o)))

.PHONY: clean

all: ${OUT_DIR} $(OBJ_DIR) ${tg_so}

objs: $(OBJ_DIR) $(OBJECTS)

${OUT_DIR}:
	mkdir -p ${OUT_DIR}

${tg_so}: $(OBJECTS)
	${CC} $^ $(SHARED_LIB_FLAG) -o $@ $(LIB_PATH) ${lib_usb} -pthread -lstdc++

$(OBJ_DIR)/%.o : **/%.cpp
	$(CC) $(CFLAGS) -fPIC -g $(INCLUDES) -o $@ -c $<

$(OBJ_DIR):
	mkdir -p $(OBJ_DIR)

clean:
	rm -rf ${tg_so} $(OBJ_DIR)
