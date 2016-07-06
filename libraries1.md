#**Libraries**


##**Why use libraries**

Libraries save time and effort when you develop software.
​
##**What?**

Libraries are collections of reusable sections of code. They contain
methods, functions and classes that tackle common tasks in efficient and
effective ways. Libraries can be used for a range of tasks, from common
mathematical computations all the  way to modelling specific scientific
processes.
​
##**Dynamic vs Static?**

Static Libraries (`.lib`/`.a`): When using static libraries your final
compiled application **contains the machine code** of all functions,
methods or classes from the libraries that are linked with your
application.
​
Dynamic Libraries (`.dll`/`.so`): When using dynamic libraries the final
compiled code **contains a pointer** to the memory address of where the
functions, methods and classes in the libraries that are included in
your application. These can be included by dynamic linking which occurs
at compile time or they can be included by dynamic load/unload and so
linked only at the time the software is executed.
​
Static and dynamic libraries both have advantages and disadvantages:
​
-   Static libraries are good when you wish to release software as the
    libraries are defined by and included in the distribution by you
    the developer. The user does not have to install any libraries for
    your distribution to work.
​
-   Static libraries are good because you do not have to worry about
    updates to libraries causing incompatibilities in your code. This is
    because the version of the library that you need is already included
    in your executable and any updates will not affect this.
​
-   Dynamically linked libraries are good because your executables are
    much smaller. This is because instead of including the machine code
    for all the code from all of the libraries used in your code in the
    executable, as you would in a static library, dynamic libraries only
    contain a pointer to the memory location of the compiled version of
    the library, resulting in a much smaller executable.
​
-   Dynamically linked libraries are good because   the memory address of
    the pointer is the same after an update as it was before so there is
    no need to recompile your code after each update. However with a
    static library you have to recompile your source code to include
    these updates.

##**How to Create Dynamic/Static Linking?**

​In this example, you
will be using and creating many files with the same file name but with
different file extensions, so it useful to know what each of the file
extensions mean. This also helps if you want to google further
information. The file extensions are:
​
1.  `.c` means the file is source code created in the programing language
    c – this can be either a main program or the source code for a
    library.
​
2.  `.h` means the file is a header file and this gives the name and the
    arguments for all the code sections in the library with the same
    file name.
​
3.  `.so` means it is a shared objective library file which can be read at
    runtime.
​
4.  `.a` means the file is an archive library and is linked at
    compile time.
​
5.  `.o` means the file is an object file which is an intermediary file
    created during the compile process. It is not directly executable but
    has all the segments of information needed to create an executable from all the sources - this includes header and
    library files.
​
6.  `.exe` or no extension means the file is executable.
​
Shared libraries are used by default when they are available. By
specifying the `-static` command you can force the use of static
libraries.  

​
STATIC LINKING:
```
$ gcc -o mycode mycode.c -l1 -l2 -static
```

In this example `-l1` is searching for a library named `lib1.a` and
`-l2` is searching for a library named `lib2.a`. The `.a` extension
indicates that a file is a static library or archive and is a binary
file that contains objective code.
​

SHARED LINKING:
```
$ gcc -o mycode mycode.c -l1 -l2
```
​
In this example `-l1` is searching for a library named `lib1.so` and
`-l2` is searching for a library named `lib2.so`. The `.so` extension
indicates that a file is a shared library and is a binary file that
contains objective code.
​
##**Example 1: Compiling Libraries**

In this example we will create a very basic library and discuss the
different ways you can load them both dynamically and statically in your
code. Though you will rarely have to compile the libraries themselves,
it should help to better understand what is happening when you load
libraries into your own code.
​
Firstly we will create our library. This is done by creating two files
for the library, `libsquare.c` and `libsquare.h`, as shown below.  

​LIBSQUARE.C:  

```
int square(int x) {return x*x;}
```

LIBSQUARE.H:  

```
​int square(int x);
```


We can now begin to create our libraries. We will **start by creating
the static library** by inputting the following commands:

```
gcc -c libsquare.c -o libsquare.o
```

Here we use the GNU C compiler to compile our library's source code into
object code. The `-c` switch tells the compiler to compile the code. The
`libsquare.c` tells the compiler which code to compile. The `-o libsquare.o` tells the compiler what to name the outputted compiled
code. We can now create the static library with the following command.

```
$ ar rcs libsquare.a libsquare.o
```

The `ar` command is used to create static libraries (archives). The `rcs`
operands tell the archiver to create the archive and add the
`libsquare.o` object file to it. The final static library produced will
be names `libsquare.a`.

​We will **now create the dynamic or shared library** and will start by
entering the following command:

```
$ gcc -c libsquare.c -fpic -o libsquare.o
```

The main difference in the creation of the object file for the shared
library is the `-fpic` switch. This switch generates position
independent code which is needed for the creation of shared libraries.
`–fpic` or `–fPIC` can be used when generating the objective file. The
`–fPIC` choice always works, but may produce larger code than `–fpic`
while using the `-fpic` option usually generates smaller and faster code
that will have platform-dependent limitations. Now we have the object
file we can create a shared library with the following command:
```
$ gcc libsquare.o -o libsquare.so -shared
```

This command simply compiles the object code created in the previous
step as a shared library file. The `-shared` switch is use to indicate
that you are generating a shared library.
​
##**Compiling Statically & Dynamically With the GNU Compiler**
​We must now create the source code for our program. For the purposes of
this example we name the file `demo.c`. Our code will simply call the
square function in our square library on the number 5 and print the
returned value.
​

DEMO.C:
```
#include "libsquare.h"  
#include <stdio.h>  

int main(void) {  
    int num;  
    num = square(5);  
    printf("5 squared %d.n", num);  
    return 0;  
}  
```
​When compiling your program, by default the linker will search for
shared libraries as opposed to static ones. This can be done with the
following relatively straightforward compiler command.
```
$ gcc -L$PWD -o demo_dynamic demo.c -lsquare
```
In this example, `-lsquare` is searching for a library named `lib1.so`
and the `-L\$PWD` tells the linker to search in the current working for
the library. With libraries that are preinstalled on the HPC the
`L\$PWD` switch will not be needed as the information on where to find
the library will be held in the `ldconfig` file. For our example, however,
we must tell the linker where to find the library.
​
To run the new executable, we must update the library path with the
following command in order to tell executable where to look for the
library files.
```
$ LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PWD
```
This command simply appends the current working directory to the library
path. We can now run the executable and should receive the expected
output.
```
$ ./demo_dynamic  
5 squared 25.
```
We can show the effect of using a dynamic shared library by renaming the
library file so that the linker cannot find it. When we attempt to run
the executable we will get an error message stating that there was a
problem locating the library.
```
$ mv libsquare.so libsquarebak.so  
$ ./demo_dynamic  
 ./demo: error while loading shared libraries: libsquare.so: cannot
 open shared object file: No such file or directory
```
We can resolve this by simply renaming the library back to its original
title.
```
$ mv libsquarebak.so libsquare.so  
$ ./demo_dynamic
```
Now we can try compiling our code using the static library. This means
that the compiled code from the library will be included within the
executable. This is done using the following command:
```​
$ gcc -L$PWD -o demo_static demo.c -lsquare -static
```
Notice how the command is almost exactly the same as the command for
compiling our code with the shared library, with the only difference
being the `-static` switch that tells the compiler to link to static
libraries instead of shared where possible.
```
$ ./demo_static  
5 squared 25.
```
Running the executable gives the expected result. However, when we
rename the libraries so that the executable cannot find them we find
that the executable still runs perfectly.
```
$ mv libsquare.a libsquarebak.a  
$ mv libsquare.so libsquarebak.so  
$ ./demo\_static  
5 squared 25.
```
This shows that we have successfully created an executable that links to
a static library.
```
$ ./demo_dynamic  
./demo: error while loading shared libraries: libsquare.so: cannot
open shared object file: No such file or directory
```
This shows that the dynamic version does not work while the static does.
​
##**Compiling Statically & Dynamically With the Intel Compiler**

The process is the same whichever compiler you use. If you wanted to do
the same example with the intel compiler you would just replace `gcc`
with `icc`. It is generally best to use the same compiler (or family of
compilers) for programs and libraries that will be executed together.
​
##**Example 2: GSL (GNU Scientific Library) C**

This is a more complex example that demonstrates ***?………..?*** In this example
you will be able to read and write vector data.
​
Example C Program of **writing** a vector to a file

​
VECTOR\_WRITE.C:
```
#include stdio.h;  
#include gsl/gsl_vector.h;  

int main (void) {

    int i;  
    gsl_vector * v = gsl_vector_alloc (100);

    for (i = 0; i < 100; i++) {  
            gsl_vector_set (v, i, 1.23 + i);  
        }

    FILE * f = fopen ("test.dat", "w");  
    gsl_vector_fprintf (f, v, "%.5g");  
    fclose (f);  

    gsl_vector_free (v);  
    return 0;  
}
```
Example C Program of **reading** a vector from a file
​

VECTOR\_READ.C:
```
#include &lt;stdio.h&gt;  
#include &lt;gsl/gsl_vector.h&gt;  

int main (void) {

    int i;  
    gsl_vector * v = gsl_vector_alloc (10);

    FILE * f = fopen ("test.dat", "r");  
    gsl_vector_fscanf (f, v);  
    fclose (f);

    for (i = 0; i &lt; 10; i++) {  
        printf ("%g\n", gsl_vector_get(v, i));  
    }

    gsl_vector_free (v);  
    return 0;  
}
```
In the example code, the GSL library is specified by naming the exact
header file using `#include gsl/headerfile.h`. This can also be
given by using the more general reference `#include gsl` which is
less efficient – more noticeably for applications with many libraries.

To load the GSL module the command is needed:
```
$ module load gsl
```
By the default the compiler is Intel but this can be changed to the GNU
compiler with a simple command:
```
$ module switch intel gnu
```
This is how to compile the example codes with the GNU compiler:
```
$ gcc -o vector_read_dynamic vector_read.c -lgsl -lgslcblas  
$ gcc -o vector_write_dynamic vector_write.c -lgsl –lgslcblas
```
This method uses dynamic linking so the GSL module needs to be loaded
whenever the executable is run. The flags at the end of the commands,
`-lgsl` `–lgslcblas` tells the linker the names of the libraries that needs
to be linked and by default the `.so` shared libraries are linked.

To statically link to the libraries, the codes need to be compiled with
the gnu compiler like so:
```
$ gcc -o vector_read_static vector.c -lgsl -lgslcblas -static  
$ gcc -o vecto_write_static vectoropen.c -lgsl -lgslcblas –static
```
This is very similar to the dynamic link compilation, but with the added
`–static` flag to tell the linker to use the static `.a` libraries and
implement them in the binary executable. This means the GSL module does
not need to be loaded in order to run the programs.

Intel compilers can also be used to compile the code. This is done
almost exactly the same way as it is with the gnu compiler but instead
the `icc` command is used instead of `gcc` so it will be something like
this:
```
$ icc –o vector vector.c –lgsl –lgslcblas
```
##**Example 3: FFTW C**

**Example is lost – may be able to use
http://micro.stanford.edu/wiki/Install\_FFTW3**

This example teaches you how to load two different modules and include
their libraries into your code's executable statically and then test
that this is the case. This is necessary as the FFTW module contains
only static libraries.

This is a basic a C example that performs a fast Fourier transform on a
1-dimensional array of floating point numbers. The FFTW module contains
only static libraries so we can only create an executable with these. To
load the FFTW module type:
```
$ module load fftw
```
We will be compiling the example code with the GNU C compiler, so we
must switch the Intel compiler out for this using the following command:
```
$ module switch intel gnu
```
Now that we have the environment set up **we can begin running this
example**. Begin by downloading the source code from ***here.***

Create a new directory save the source code in there under the name
`example.c`. From within this directory we can compile the code using
the following command:
```
$ gcc example.c -o example -lfftw3 -lm
```
The command `gcc` is the GNU C Compiler and will compile the file
`example.c`. The `-o example` tag tells the compiler to name the output
executable `example`. The `-lfftw3` tag will search your
`LD_PATH_LIBRARY` for either `libfftw3.a` or `libfftw3.so` libraries and
then link them to your code. In this case FFTW contains only static
libraries so when compiling your code the outputted executable will
contain a static FFTW library. Similarly the tag `-lm` will search for
either `libm.a` or `libm.so` which are the mathematics libraries.

You now have an executable program ready to perform fast fourier
transformations on data. The only part we are missing is the data.
Create a new text file and open it using the following commands:
```
$ touch data.txt && gedit data.txt
```
You can enter your own floating point numbers separated each by a new
line in here now or simply use my example data below.

> 1.419662
>
> 1.411450
>
> 1.413796
>
> 1.419662
>
> 1.419662
>
> 1.419662
>
> 1.419662
>
> 1.419662
>
> 1.434327
>
> 1.437846
>
> 1.437846
>
> 1.437846
>
> 1.441366
>
> 1.441366
>
> 1.437846
>
> 1.435500

We can now run the executable using the following command:
```
$ ./example -n 16 data.txt
```
The command `./example` will run the executable you created. The tag `-n
16` is program specific and in this case indicates that there are 16
floating point numbers. The `data.txt` tag is also program specific and
tells the program that the text file you created is where the data you
want to perform the calculations is.

After hitting return your code should now perform a fast fourier
transform on the set of floating point values you supplied in your text
file. Because your executable was compiled using static libraries, the
libraries themselves are not needed to run the executable. To test this
we can unload the FFTW library and attempt to run the executable without
it.
```
$ module unload fftw  
$ ./example -n 16 data.txt
```
You should find that the executable runs without any errors without
needing the FFTW module to be loaded into the environment due to the use
of static libraries.

##**FFTW C Intel Example**

This is a basic a C example that performs a fast fourier transform on a
1-dimensional array of floating point numbers. The FFTW module contains
only static libraries so we can only create and executable using these.
To load the FFTW module type:
```
$ module load fftw
```
We will be compiling the example code with the Intel C compiler, so we
must ensure that this is the currently loaded compiler. If it isn't,
then we can switch the GNU compiler out for this using the following
command:
```
$ module switch gnu intel
```
Now that we have the environment set up we can begin running this
example. Begin by downloading the source code from ***here.***

Create a new directory save the source code in there under the name
`example.c`. From within this directory we can compile the code using
the following command:
```
$ icc example.c -o example -lfftw3 -lm
```
The command `icc` is the Intel C Compiler and will compile the file
`example.c`. The `-o example` tag tells the compiler to name the output
executable `example`. The `-lfftw3` tag will search your
`LD_PATH_LIBRARY` for either `libfftw3.a` or `libfftw3.so` libraries and
then link them to your code. In this case FFTW contains only static
libraries so when compiling your code the outputted executable will
contain a static FFTW library. Similarly the tag `-lm` will search for
either `libm.a` or `libm.so` which are the mathematics libraries.

You now have an executable program ready to perform fast fourier
transformations on data. The only part we are missing is the data.
Create a new text file and open it using the following commands:
```
$ touch data.txt && gedit data.txt
```
You can enter your own floating point numbers separated each by a new
line in here now or simply use my example data below.

> 1.419662
>
> 1.411450
>
> 1.413796
>
> 1.419662
>
> 1.419662
>
> 1.419662
>
> 1.419662
>
> 1.419662
>
> 1.434327
>
> 1.437846
>
> 1.437846
>
> 1.437846
>
> 1.441366
>
> 1.441366
>
> 1.437846
>
> 1.435500

We can now run the executable using the following command:
```
$ ./example -n 16 data.txt
```
The command `./example` will run the executable you created. The tag `-n
16` is program specific and in this case indicates that there are 16
floating point numbers. The `data.txt` tag is also program specific and
tells the program that the text file you created is where the data you
want to perform the calculations is.

After hitting return your code should now perform a fast fourier
transform on the set of floating point values you supplied in your text
file. Because your executable was compiled using static libraries, the
libraries themselves are not needed to run the executable. To test this
we can unload the FFTW library and attempt to run the executable without
it.
```
$ module unload fftw  
$ ./example -n 16 data.txt
```
You should find that the executable runs without any errors without
needing the FFTW module to be loaded into the environment due to the use
of static libraries

In doing this example you have learned how to load two different modules
and and include their libraries into your code's executable statically
and then test that this is the case.

##**FFTW Fortran GNU Example**

This is a basic Fortran example for loading and using the FFTW module.
This is done exactly the same way as the C example where the module is
loaded by using
```
$ module load fftw
```
The example code shows that the FFTW is used with the following line
```
include “fftw3.f”
```
Similarly to the C code example we will be compiling with the GNU
compiler so the default Intel module will need to be switch to GNU.
```
$ module switch intel gnu
```
The command to compile the code is as follows:
```
$ gfortran -I/$FFTW_HOME/include examplef.f -o example -lfftw3 -lm
```
The `-I` option is needed here to direct the compiler to the relevant
include directory for `fftw3`. The library flags at the end `-lfftw3` and
`-lm` are exactly the same as the ones used for the C example to link the
FFTW and math libraries needed.

To run the executable produced during the compilation and linking just
used the following command
```
$ ./examplef
```
This should output some results to the terminal. The FFTW lib folder
only contains static `.a` libraries so the functionalities needed will
be implemented with the binary executable and will not need to
dynamically link to the library. This means that the code can then run
successfully without loading the FFTW and math libraries.

##**FFTW Fortran Intel Example**

To use the FFTW library with Intel compilers, the suffix of the Fortran
code needs to `.f90` or it won’t compile. The following command should be
used:
```
$ ifort exampleintelf.f90 -o exampleintelf -lfftw3 -lm
```
As you can see the `-I` option is not needed here as the linker knows
where to look for the relevant ‘fftw3.f’ file in the FFTW include
directory.
