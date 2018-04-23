CC=gcc
CFLAGS= -g -Iinclude -Wall
# The -v option can also be used to display detailed information about the exact
#sequence of commands used to compile and link a program.
# The -g option is for debug compiling
# the -Wall is to print the important warnings
C_FILES = $(wildcard src/*.c)
H_FILES = $(wildcard include/*.h)
O_FILES = $(C_FILES:src/%.c=build/%.o)


#this part is used to silent or activate messages printed
CC_VERBOSE = $(CC)
CC_NO_VERBOSE = @echo "Building $@..."; $(CC)

ifeq ($(VERBOSE),YES)
  V_CC = $(CC_VERBOSE)
  AT := 
#:= forces all variables on the right-hand side to be expanded immediately,
# while using = makes sure they are expanded at the last possible moment
else
  V_CC = $(CC_NO_VERBOSE)
  AT := @
endif


.DEFAULT: all
.PHONY: all clean
all: program
	$(AT)echo files builded succesefully !!
	$(AT)echo ------------------------------------------------------------
	$(AT)echo executing resulted program
	$(AT)echo ------------------------------------------------------------
	$(AT)./$^
program: $(O_FILES)
	$(V_CC) $(CCFLAG) -o $@ $^
build:
	$(AT)mkdir -p build
build/%.o: src/%.c $(H_FILES) | build
	$(V_CC) $(CFLAGS) -c $< -o $@ 
clean:
	$(AT)echo Removing object files
	$(AT)-rm -f build/$(O_FILES)
	$(AT)echo Removing build directory
	$(AT)-rm -rf build
	$(AT)echo Removing the executable
	$(AT)-rm -f program
	$(AT)echo all is clean now .......!!!!!
