# How to write a Makefile
				
 ## Manual compilation
	To manually compile a project and produce an executable follow the following instructions:

	$ gcc -I . -c main.c  ->  main.o
	$ gcc -I . -c module.c ->  module.o
	$ gcc main.o module.o -o target_bin -> target binary
	(-I is used to include the current directory (.) as a header file location.)


 ## General syntax of a Makefile

	The general syntax of a Makefile rule is as follows:

	arget: dependency1 dependency2 ...
	[TAB] action1
	[TAB] action2
    ...


 General remarkes 
	 By convention, all variable names used in a Makefile are in upper-case
	is CC = gcc, which can then be used later on as ${CC} or $(CC)
 	if no target specified for make, make is defaulted to target the first target in 
 	the Makefile Makefiles use  as the comment-start marker
 	Each make line is executed in a separate sub-shell environment. Therefore, a 
 	command like cd newdir will not affect the next lines


 Simple make file for our project (dependency tree structure)	
	
all: main.o module.o 				dependencies of target all 	
	gcc main.o module.o -o target_bin	 action to make target all	
main.o: main.c module.h 			dependencies fo target main.o 	
	gcc -I . -c main.c 			action to make target main.o ,
module.o: module.c module.h														
	gcc -I . -c module.c 			-I indicate header file locations	
clean:						This target has no dependencies						
	rm -rf *.o 													
	rm target_bin


 How does make utilize the timestamp of files	
	
	if	make found a dependency with a newer timestamp than the target, it will 
	remake that target and all the targets that are depending on it. 
	for example imagine we change the source file module.c. Then we tried to 
	rebuild the program usking make. make will notice that the dependency file 
	module.c has a newer timestamp than its target module.o. Therefore, it will 
	remake the target module.o. However, the target all depends on module.o, as 
	such make will remake the target all again which will result in a new target_bin 
	binary file. Notice that make did not need to remake the main.o target because 
	it has a new timestamp than its dependencies and such make saves compiling time. 
	special targets are not files and such their related actions are always executed 
	if they are specified.


 Dealing with assignment operator	
	
	Simple assignment (:=)	
	A simple assignment expression is evaluated only once, at the very first occurance. 
	After that, every time the variable is encountered it will be replaced by the value 
	based on the first evaluation. For example: when a CC :=${GCC} ${FLAGS} first 
	encountered, CC us set ti gcc -W and every time ${CC} occurs in file, it is replaced 
	by gcc -will		
	
	Recursive assignment(=)	
	A Recursive assignment expression is evaluated everytime the variable is encountered 
	in the code. For example CC = ${GCC} {FLAGS} will be converted to gcc -W only when an
	action like ${CC} file.c is executed. However, if the variable GCC reassignment i.e 
	GCC=c++ then the ${CC} will be converted to c++ -W after the reassignment. 
	
	Conditional assignment (?=)	
	Conditional assignment assigns a value to a variable only if it does not have a value	
	
	Appending (+=)	
	CC = gcc 	
	CC += -w 	
	CC now has the value gcc -W


 Using patterns and special variables	
	
	When wildcard % appears in the dependency list, it is replaced with	
	the same string that was used to perform substitution in the target.
	
	Inside actions we can use:	
		$@ to represent the full target naem of the current target 	
		$? returns the dependencies that are newer than the current target 	
		$* returns the text that corresponds to % in the target 	
		$< returns the name of the first dependency 	
		$^ returns the names of all the dependencies with space as the delimiter


 Action modifiers	
	
	- (minus) Prefixing an action with - tells make to ignore any error occurs	
	in that line. By default, execution of a Makefile stops when any command returns 
	a non-zero (error) value. 	
	@ (at) suppresses the standard print-action-to-standard-output behaviour of make  
	For exampele, @echo OutputMessage will print "OutputMessage" and suppresses 	
	printing the action echo OutputMessage. 


 Using PHONY to avoid file-target name conflicts	
	
	If the project directory contains a file with same names as a special target 	
	in the Makefile (i.e. all, clean), that will result in a conflict and make will	
	produce an error. Using .PHONY directive to specify which targets are not ot be  
	considered as files, for instance, .PHONY: all clean


 Check make execution before the actual building (dry run)	
	
	At times, maybe when developing the Makefile, we may want to trace the make 	
	execution (and view the logged messages) without actually running the actions,  
	which is time consuming. Simply use make -n to do a “dry run”.

 Nested Makefiles	
	
	To run a multiple make files in different directories, first  change directory 	
	and then invoke make. Using the environment variable $(MAKE) gives greater 	
	flexibility to run multiple Makefiles. For example, $(MAKE) enables passing the 
	-n option for the "dry run"	
	subdir:	
		cd subdir %% $(MAKE)


 Using the shell command output in a variable 	
	
	Sometimes we need to use the output from one command/action in other places in the 	
	Makefile — for example, checking versions/locations of installed libraries, or other 
	files required for compilation. We can obtain the shell output using the shell 		
	command. For example, to return a list of files in the current directory into a 	
	variable, we would run: LS_OUT = $(shell ls).
The dependencies of the target all 	
		gcc main.o module.o -o target_bin	