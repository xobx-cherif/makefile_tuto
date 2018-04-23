# Makefile tutorial

in this little short tutorial i will try to take you in a journey to explain some basic Make files commands. and how to write your own make file for small and medium size projects. on another hand by understanding the most usefull and used syntax of make file you will be able to modify make files that are already written by someone else.
Most often, the makefile directs make on how to compile and link a program. Using C/C++ as an example, when a C/C++ source file is changed, it must be recompiled. If a header file has changed, each C/C++ source file that includes the header file must be recompiled to be safe. Each compilation produces an object file corresponding to the source file. Finally, if any source file has been recompiled, all the object files, whether newly made or saved from previous compilations, must be linked together to produce the new executable program. These instructions with their dependencies are specified in a makefile. If none of the files that are prerequisites have been changed since the last time the program was compiled, no actions take place. For large software projects, using Makefiles can substantially reduce build times if only a few source files have changed.

first of all let's start by detailling some of the scarry terms and syntax used in makefiles.

## Variables
 A variable is a name defined in a makefile to represent a string of text, called the variable's value.
example:
```bash
    FILE=hello
    VAR=10
    print:
    	mkdir -p $(FILE)
    	@echo file $(FILE) is created
    	@echo the variable value is $(VAR)
```

in this example the output will be :

	file hello is created
	variable value is 10
it is abuvious in this example that we declared two variable FILE and VAR with the values hello and 10 respectively.
first we make a new folder with the File varible value name and we print that the file was created then we print the value of the VAR.
## Rules
A rule appears in the makefile and says when and how to remake certain files, called the rule’s targets (most often only one per rule). It lists the other files that are the prerequisites of the target, and the recipe to use to create or update the target. 
in the previous example **print:** is a rule wich have no dependency. lets see another example
example:

```bash
    FILE=hello
    print:create
    	@echo file $(FILE) is created
    create:
    	mkdir -p $(FILE)
    delete:
        -rmdir $(FILE)
```
Lets go step by step through this example:
first of all we created a varibale with hello value. Then we defined three rules the first rule dependency is create this mean that we have a rule that depends on another one so its dependency will be executed before the rule itself in our case we will create a folder using the create rule then we print using @echo that the file was created. wait what about the third rule, so make by default will strat by executing the first rule in our case it is the **print:** and we can create other rules and call them when we execute the make command on the terminal in this example the **delete:** rule will just delete the folder we created we can call that rule just using the following command:
```bash
make delete
```
## A simple make file
At this point we can start making some simple make file. yeah that's what i said now you have basic knowledge about how to make a simple make file.Let's start by writing a makefile that is as simple as possible. This makefile just produces a binary from a single input C file.

lets start by writing the following c code no fancy stuffs just a function that will print hello make !! :
```c
#include <stdio.h>
int main(){
    printf("hello make !!\n");
    return 0;
}
```
now basically if we need to compile this file we just use gcc and type the following command on the terminal :
```bash
gcc helloMake.c -o helloMake
```
Right! so now we will try to make a make file that will give us the same output ! try to think a little bit what stuffs we will use because till now all we know variables rules and rules dependancies. well this will be enough for a very simple makefile like the one we will need for this basic example. so lets check how would it look like

```bash
hello: helloMake
	@echo compilation done!!
	./helloMake
	
helloMake:
	gcc helloMake.c -o helloMake
delete:
	-rm helloMake
```
See it is not complicated basic we execute automatically the same commands we used previously to compile the c file then execute the resulting file !!
so here the first rule **hello** will call its dependency **helloMake:** wich will compile ou source file then it will execute the resulting file. this is a very basic make file but it is good as a start.
# Let's go deeper
in the previous example we made a very basic make file for our c file wich is far from being similar to the make files that you will find in real projects c codes. basicly the benefit of using a make file is to automate alot of boring stuffs.
let's change a little bit our code.  basicly let's write a header file for our program and a c file for our function that prints the hello make !!. and a c file that will contain our main function that will call the print function okey !!. well we are still in basic c program we may not see the benefit of doing this with a tiny c code but this is very usefull in large and medium size source codes. well the aim here is just to demonstrate how to deal with multiple c files where we have to generate more than one **.o** file (objet file) and header files okeyy !!! so lets start.
so let's make the **.c** files and the **.h** file as the following :

This is our **header** file lets call it **hellomake.h**
```c
/*
example include file
*/

void myPrintHelloMake(void);
```
this is our **c file** that contains the print function lets call it **hellofunc.c**
```c
#include <stdio.h>
#include <hellomake.h>

void myPrintHelloMake(void) {

  printf("Hello makefiles!\n");

  return ;
}
```
and finaly this is our **c file** that contains the main function and lets call it **hellomake.c**
```c
#include <hellomake.h>

int main() {
  // call a function in another file
  myPrintHelloMake();

  return(0);
}
```

let's now try to write a makefile for this code

```bash
CC=gcc
CFLAGS=-I.

hellomake: hellomake.o hellofunc.o
     $(CC) -o hellomake hellomake.o hellofunc.o $(CFLAGS)
```
Pretty simple right ?. We've defined some constants CC and CFLAGS. It turns out these are special constants that communicate to make how we want to compile the files hellomake.c and hellofunc.c. In particular, the macro **CC** is the C compiler to use, and **CFLAGS** is the list of flags to pass to the compilation command. By putting the object files **hellomake.o** and **hellofunc.o** in the dependency list and in the rule, make knows it must first compile the .c versions individually, and then build the executable hellomake.
**Rq:** the -I. Flag is used to indicate the path to include file

Using this form of makefile is sufficient for most small scale projects. Here we can also see the first and important magic piece of the make puzzle. When you run make, make will compare the modification date on the output file(s) to the modification date of the input file(s), and if and only if the input file(s) are newer than the output file(s), it will run the commands associated with the target.
Now, let us make the example a bit more complicated:

```bash
CC=gcc
CFLAGS= -I.

exec: program
	@echo compilation done!!
	./program

program: hellofunc.o hellomake.o
	$(CC) $(CFLAGS) -o program hellofunc.o hellomake.o

hellomake.o: hellomake.c
	$(CC) $(CFLAGS) -c hellomake.c
hellofunc.o: hellofunc.c
	$(CC) $(CFLAGS) -c hellofunc.c
clean:
	-rm -f hellofunc.o
	-rm -f hellomake.o
	-rm -f program
```

Well nothing new we always define some rules and some dependencies we compile both the hellomake and the hellofunc files we get our **object files** we link and compile to get our final program see easy !! :D

Using this form of makefile is sufficient for most small scale projects. However, there is one thing missing: dependency on the include files. If youmake a change to hellomake.h, for example, make would not recompile the .c files, even though they needed to be. In order to fix this, we need to tell make that all .c files depend on certain .h files. We can do this by writing a simple rule and adding it to the makefile. also here there is some repeated stuffs that we want to get rid of them and automate some stuffs.
so let's see the following example it will get more complicated here but don't worry we will go through it together :

```bash
CC=gcc
CFLAGS= -I.
DEPS = hellomake.h
exec: program
	@echo compilation done!!
	./program

program: hellofunc.o hellomake.o
	$(CC) $(CFLAGS) -o $@ $^

%.o: %.c $(DEPS)
	$(CC) -c -o $@ $< $(CFLAGS)
clean:
	-rm -f hellofunc.o
	-rm -f hellomake.o
	-rm -f program
```
DOn't be scared it is pretty easy :D !
so let's start with the first bloc
```bash
program: hellofunc.o hellomake.o
	$(CC) $(CFLAGS) -o $@ $^
```
So the variables you know their role writing **$(CC)** is equivalent to writing **GCC** right ! okey
Lets now analyse that scary line **$(CC) $(CFLAGS) -o $@ $^** basicly we have two new operators here the **$@** and **$^** well lets go back to how we said that we write a **rule**. Basicly it is formated in this way **rule : dependancies** right so **$@** means whatever is written before the **:** in the rule and **@^** whatever is written in the rule after the two points **:** see easy :D.
so writting **$(CC) $(CFLAGS) -o $@ $^** is equivalent to writting **$(CC) $(CFLAGS) -o program hellofunc.o hellomake.o**

Now let's see the second scary bloc i guess now it is less scarry right ? :D
```bash
%.o: %.c $(DEPS)
	$(CC) -c -o $@ $< $(CFLAGS)
```
first we created the macro **DEPS**, which is the set of **.h files** on which the **.c files depend** to solve the problem we mentioned earlier about not recompiling the **object files** when **updating the header file**. Then we define a rule that applies to all files ending in the **.o** suffix using **%.o**. The rule says that the .o file depends upon the **.c** version of the file and the **.h** files included in the **DEPS** macro. The rule then says that to generate the **.o** file, make needs to **compile the .c file using the compiler defined in the CC macro**. The **-c flag** says to generate the **object file**, the **-o $@** says to put the output of the compilation in the file named on the left side of the **:** , the **$<** is new for us here and it means the first item in the dependencies list which is **%.c** in our case. And using this generic way we can have as much as we want of .o objects in dependency list of the **program :** rule without writing as many rules as their number cool right :D

Lets automate more stuffs :D yaaay see now everything seems more easy.
Well what about if we remove the other anoying stuffs like the **.c files** names and also the **.o files** names. so let's see the following make file and go through it step by step as we did with the others and have more funnn!!

```bash
CC=gcc
CFLAGS= -I.

DEPS = hellomake.h

C_FILES = $(wildcard *.c)
O_FILES = $(C_FILES:%.c=%.o)


exec: program
	@echo compilation done!!
	./$^

program: $(O_FILES)
	$(CC) $(CFLAGS) -o $@ $^

%.o: %.c $(DEPS)
	$(CC) -c $< -o $@ $(CFLAGS)
clean:
	-rm -f $(O_FILES)
	-rm -f program
```
So let's see here we added some new stuffs i guess they are not to complicated for you now right !!
but let's try to see those new stuffs together. The most important two lines here are the new top lines i guess you already noticed them.
```bash
C_FILES = $(wildcard *.c)
O_FILES = $(C_FILES:%.c=%.o)
```
Well the **C_FILES** macro will only contain the **list** of all the **.c files** in the **current directory** (all the .c files names). now let's imagine that we have an array that contain all the **.c** files **name** so that's basicly **C_FILES** in our case. The second macro **O_FILES** all what it will contain is the **.c** files names but we change the **.c** to **.o** using the **C_FILES:%.c=%.o**. a final change that we made is that in **program: hellofunc.o hellomake.o** we replaced the **.o** named by **$(O_FILES)**.

## Let's go more structured
so now let's make our code more structured well i suggest to use the following scheme
 
    |--code folder
    |--Makefile
    |    |----src
    |    |    |--------hellomake.c
    |    |    |--------hellofunc.c
    |    |----include
    |         |--------hellomake.h
so at this point we will make two folders one for the **c files** and the second for the **header files** and through the make file we are going to create another folder **build** where we store our object files.
so lets now make our **Makefile** more pretty !!

```bash
CC=gcc
CFLAGS= -Iinclude -Wall
C_FILES = $(wildcard src/*.c)
H_FILES = $(wildcard include/*.h)
O_FILES = $(C_FILES:src/%.c=build/%.o)
.DEFAULT=all
.PHONY: all clean
all: program
	@echo files builded succesefully !!
	./$^
program: $(O_FILES)
	$(CC) $(CCFLAG) -o $@ $^
build:
	@mkdir -p build
build/%.o: src/%.c $(H_FILES) | build
	$(CC) $(CFLAGS) -c $< -o $@ 
clean:
	-rm -f build/$(O_FILES)
	-rm -rf build
	-rm -f program
	@echo all is clean now .......!!!!!
```
So the first thing to notice here is taht we did the same thing to the **header files** as we did previously for the **c files** also here we changed the **-I.** flag to indictae for our compiler where to find the **header files** in our case they are in **include file** this is why we changer the flag to **-Iinclude**. Also we added a new flag the **-Wall** this one is just used to print some of the compilation warnings.
We also added the following new **rules**
```bash
build:
	@mkdir -p build
```
This rule is just used to make a new folder called **build** if it doesn't exist where we will put our **Objet files**.

```bash
build/%.o: src/%.c $(H_FILES) | build
	$(CC) $(CFLAGS) -c $< -o $@ 
```
You will also notice the usage of a **| before the dependency on "build"** above. This means it is an **"order-only dependency"**. This way make will ensure that the directory exists, but will not look at its timestamp to determine if anything needs to be rebuilt. Since the modification date of a directory changes whenever a file inside it is modified, we need this to make sure **files aren't rebuilt unnecessarily**.
I also changed the usage of **$^ to $<**. **$<** expands to the **first dependency in the list**. We don't want to pass **build** to gcc when building.
Now we can consider this example complete yeeeess !!! :D. Modifying it to build other types of files should be easy if you've followed how it was constructed above. One thing that is pretty popular though, is having your build script produce prettier output. So let us expand our example to do that as well, and take the opportunity to learn some more concepts in make.
```bash
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

.DEFAULT:all
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
```
What this does, is that if **VERBOSE** is set to YES when you call make, then it will output all information on what commands are run. If not, that information will be suppressed. To be able to do this, we have to introduce some new variables. First, we **replace** all relevant usages of **@** with the new variable **$(AT)**. **$(AT)** is only set to the real **@** if we are in non-verbose mode. To do this we use the operator **:=**. The difference between **:=** and **=** in make is a bit complicated and a cause of many bugs. **:= forces all variables on the right-hand side to be expanded immediately, while using = makes sure they are expanded at the last possible moment**. Perhaps this is easier to illustrate with a small examples:

```bash
FOO := $(BAR)
BAR = bar

output: input
	$(FOO) hello world
```
Running make output here would be equal to running the command hello world, since **$(BAR)** is not defined when **$(FOO)** is set. **$(FOO) will thus be an empty string**.

```bash
FOO = $(BAR)
BAR = bar

output: input
	$(FOO) hello world
```
Running make output here would be equal to running the command **bar hello world**. **$(BAR)** is still not defined when **$(FOO)** is set. However, since it is available when **$(FOO)** is expanded in the output rule, it will expand to **$(BAR)**, which will in turn expand to "bar".

It's time guys to stop here. Well at this point i am satisfied about the makefile we did together. I hope that you enjoyed this journey and that i helped a little bit in making that scary syntax more easy and understandable for you. If you want to learn more about the makefile i would say that the best resource is the make manual. See you another time !!










    


