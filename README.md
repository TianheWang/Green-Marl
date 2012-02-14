1 Introduction
====================================
 
Green-Marl [1] is a domain-specific language that is specially designed for
graph data analysis. For the further information for the Green-Marl language,
refer to the language specification draft [2], which can also  be found in
this directory in the source package.

'gm_comp' is a compiler for Green-Marl. It reads a Green-Marl file and
generates an equivalent, efficient and parallelized  C++ implementation, i.e.
.cc file.  More specifically, the compiler produces a C++ function for each
Green-Marl procedure.  The generated c++ functions can be compiled with gcc
and therefore can be merged into any user application that are compilable
with gcc.

The C++ codes that are generated by 'gm_comp' assume the following libraries:

  * gcc (with builtin atomic functions)
  * gcc (with OpenMp support)
  * a custom graph library and runtime (gm_graph) 

The first two are supported by any recent gcc distributions (version 4.2 or higher); 
the third one is included in this source package.
    
'gm_comp' is also able to generate codes for a completely different target environment (See Section 5).



2 The Package 
====================================

2-1 Obtaining source package
-------------------------------------

The source code repository of gm_comp, a Green-Marl compiler is as follows:
    
    http://github.com/stanford-ppl/Green-Marl/

From there, you can get the current source code package in two ways.

* as a zip file (not recommended) :
If you're not familiar with _git_ and do not want to learn it for the time being, 
you can still download the package as a zip file. Go to the above http address,
find the button that is labeled as "zip". Pushing the button,  zipped source package
will be downloaded momentarily.  Note that you have to download the zip package and 
unzip it by yourself, whenever you want to fetch a fresh version. .

* via git client:
If you already know how to fetch via git, please go ahead from the above repository. 

   + git is the name of client program, which allows you to download the source code package
   from the repository and to maintain it up-to-date. The git client program (git) should have 
   been already installed in any modern linux/unix/cygwin system.

   + Go to any local directory and do the following command; 
   It will create a new sub-directory (named Green-Marl) and download the source code there.

            git clone git://github.com/stanford-ppl/Green-Marl.git
   
  + You can always download most up-to-date package by doing the follwing command at the directory.

            git pull

  + (Note) Since current Makefile is far from perfect in detecting dependencies, after pulling a new source
  it is desired to completely re-build the compiler (or gm_graph library) by doing make clean and then doing make.
  
2-2 Directories
-------------------------------------

The following are the descriptions of the directories in the pacakge. $(top) denotes the 
top directory where you put your source package

    $(top)/src :  Compiler source code
    $(top)/obj :  Object (.o) files during compilation (to be generated)
    $(top)/test:  Test routines for compiler development
    $(top)/bin :  Compiler binary (gm_comp) (to be generated)
    $(top)/etc :  Extra files for enhanced your experience
    $(top)/doc :  Documents
    
    $(top)/apps:  Sample Green-Marl applications and runtime library
    $(top)/apps/src                  : Sample Green-Marl programs
    $(top)/apps/output_cpp           : Directory for Green-Marl to C++ 
    $(top)/apps/output_cpp/generated : C++ codes generated by the gm_comp (to be generated)
    $(top)/apps/output_cpp/src       : C++ 'main' code for sample applications
    $(top)/apps/output_cpp/data      : Directory for data files  (to be generated)
    $(top)/apps/output_cpp/bin       : Directory for compiled binaries (to be generated)
    $(top)/apps/output_cpp/gm_graph  : gm_graph (C++ runtime and graph library)
    $(top)/apps/output_cpp/gm_graph/src  : source  files (.cc) for gm_graph
    $(top)/apps/output_cpp/gm_graph/inc  : header  files (.h)  for gm_graph
    $(top)/apps/output_cpp/gm_graph/lib  : gm_graph library (.a) (to be generated)
    $(top)/apps/output_gps           : Directory for Green-Marl to GPS


3 The compiler
====================================

3-1 System Requirements
-------------------------------------

The compiler(gm_comp) and runtime(gm_graph) requires the following environments:

 * POSIX environment: linux or unix, (not tested with cygwin)
 * x86 instruction-set architecture (i386 or x86_64)
 * gcc (version >= 4.2), which supports OpenMp
 * g++ 
 * GNU flex and bison 

3-2 Directory Generation
-------------------------------------

The following steps generates a set of empty directories that will be used during
compilation:

    cd $(top)
    ./make_dirs.sh

3-3 Build the Compiler
-------------------------------------

The following steps build up 'gm_comp', a Green-Marl compiler:

    cd $(top)/src
    make                  %% check if $(top)/bin/gm_comp has been created successfully.
    cd $(top)/bin 
    ./gm_comp -h          %% this command shows available compiler options

A more detailed documentation about the gm_comp compiler can be found in [doc/gm_comp.md](Green-Marl/blob/master/doc/gm_comp.md) in this package

3-4 Installing Syntax Highlighting for vi
-------------------------------------

Vi users can enable syntax highlighting of Green-Marl programs (.gm files) 
through the following steps:

    cp $(top)/etc/vim/syntax/greenmarl.vim ~/.vim/syntax/
    cp $(top)/etc/vim/ftdetect/greenmarl.vim ~/.vim/ftdetect/


4 Compiling and Executing Sample Applications
====================================

4-1 The Applications
-------------------------------------

Four Green-Marl sample programs are included in the package. The codes can be found
in the following location:
    $(top)/apps/src/ 
    
All of the programs are classic graph algorithms implemented in Green-Marl. Take a
look at each program (*.gm) and see how each algorithm is described intuitively with Green-Marl.

 (a) Betweenness Centrality (bc.gm): this computes (estimated) betweenness centrality of each node in the given
  graph. As a matter of fact, this version of the algorithm gets an estimated value by performing BFS only from
  the given set of nodes, instead of all the nodes.
  
 (b) Conductance (conductance.gm): this computes the conductance of the given subset of nodes in a graph. Here,
  the subset is represented with 'membership' property; i.e., each node has the ID of the group where it belongs.

 (c) PageRank (pagerank.gm): this computes pagerank of each node in the given graph.

 (d) Strongly connected components (kosaraju.gm) : this finds strongly connected components of a given graph, 
  using Kosaraju's algorithm. The original algorithm performs two DFS traversal of a graph -- one on the original 
  graph, and the other on its transposed graph. However, this version replaces the second DFS with BFS, 
  in order to improve performance.


4-2 Compiling Green-Marl Programs with gm_comp
-------------------------------------

The following steps compiles sample Green-Marl programs into C++:

    cd $(top)/apps
    
    %%%% The following file contains the names of Green-Marl programs to be compiled (w/o file extension). 
    %%%% The actual Green-Marl programs are located at $(top)/apps/srcs.
    cat Programs.mk  
    
    %%%% The following invokes gm_comp and generates c++ implementation (.cc, .h file)
    %%%% out of Green-Marl programs (.gm file)
    make gen
    
    %%%% Source codes are generated in the following location.
    %%%% Check if source codes are successfully generated.
    cd $(top)/apps/output_cpp/generated/     


4-3 Compiling Graph and Runtime Library
-------------------------------------

Every code generated by gm_comp assumes a specific runtime/graph library, 
namely gm_graph. The source codes of gm_graph library is also included in this package 
at the following locations:

    $(top)/apps/output_cpp/gm_graph/src
    $(top)/apps/output_cpp/gm_graph/inc

gm_graph library can be built through the following steps:

    cd $(top)/apps/
    make lib
    
    %%%% Check libgmgraph.a has been successfully created.
    ls $(top)/apps/output_cpp/gm_graph/lib  

Note that this step is required only once for each package release.  A more
detailed documentation about gm_graph library can be found in doc_gm_graph.txt


4-4 Compiling the Generated C++ Files
-------------------------------------

Before we go, let us remind you that all the codes generated by gm_comp are C++
'functions' that can be invoked by 'main' application. Thus, this package
provides examplar 'main functions' for the sample Green-Marl programs. 
Each main file of programs is named as ${program}_main.cc
   
All the 'main functions' do the same things: (1) it reads out the graph
(custom binary format) from the given filename, (2) prepares other input data, and 
(3) invokes the function generated by gm_comp; internally, the function is executed 
using given number of threads. 

Note that the user is free to create his/her own main functions in any case.

Now we can compile the generated code through the following steps:

    cd $(top)/apps/
    make bin 
    cd $(top)/apps/output_cpp/bin   %% check if executables are successfully generated

4-5 Short Cut
-------------------------------------

In fact, there is a short cut that does all of 4.2~4.4 at one command:

    cd $(top)/apps/
    make all      %% this does all of 'make lib', 'make gen', and 'make bin' in order.


4-6 Executing sample programs
-------------------------------------

To execute sample programs, we need at least one graph instance. For this
purpose, let us first synthesis a graph using an external tool named
graph_gen. graph_gen is a simple tool that generates syntactic graphs and
stores it using binary files.  One can get graph_gen from (TODO:). Here, we
assume that the graph_gen executable has been successfully built and 
copied into $(top)/apps/output_cpp/bin/.

     (TODO)
     * Currently graph_gen is created together when gm_graph is built
     * We will make graph_gen a separate package; one routine is polluted by GPL. 
 
Now, do the following steps and execute sample programs:

    cd $(top)/apps/output_cpp
    
    %%%% Synthesize a uniform random graph (1m nodes, 8m edges).
    bin/graph_gen 1000000 8000000 data/u1m_8m.bin 0
    
    %%%% Run conductance algorithm on the uniform graph with one thread.
    bin/conduct data/u1m_8m.bin 1
    
    %%%% Run conductance algorithm on the uniorm graph with 8 threads.
    bin/conduct data/u1m_8m.bin 8
    
    %%%% Try remaining sample applications (pagerank, bc, kosaraju)
    %%%% All of them have the same following command-line arguments
    %%%%   <program name> <graph name> <# of threads>
        

4-7 Adding your own Green-Marl Program
-------------------------------------

Refer to [tutorial document](Green-Marl/blob/master/doc/tutorial.md) which provides a walk-through to write your own Green-Marl
program, to compile it, and to execute it.


5 Generating GPS output
====================================

The usage model of the Green-Marl language is not restricted to the cache-coherent
shared-memory multi-processor environment. A program written in Green-Marl can be
compiled into another program that runs on a completely different target environment.

'gm_comp' can translate (a subset of) Green-Marl programs into Java codes
that run on a Pregel[3]-like framework. Pregel[3] is a framework that enables
distributed execution of graph algorithms that are written on its API.
However, the way Pregel API is designed is far from the way graph algorithms are 
normally designed; often the user should re-design his/her algorithm completely,
in order to comply with the API.

Green-Marl aims to solve this problem: the user can write his/her algorithm
in a natural way, while the compiler automatically re-write the algorithm
into the API.

Currently, gm_comp can translate a certain subset of Green-Marl programs into
GPS application; GPS is a custom replicate of Pregel[3] with a few enhancements.  
This GPS back-end of gm_comp is still in early development stage.

To see how gm_comp-GPS works, try following steps: 

     cd $(top)/apps
     make env=gps PROGS="gps_test gps_test2"
     
     %%%% Now check the generated (java) files, and see that how differently they look
     %%%% to the C++ implementation.
     cd $(top)/apps/output_gps/generate/
     ls *.java

Note that a simple Green-Marl code (e.g. gps_test.gm) are turned into a very
complicated java class; all these methods are required to comply with
Pregel(GPS) API.  The compiler automatically generates all these stuffs.


6 License
====================================

Copyright (c) 2011-2012 Stanford University, unless otherwise specified.
All rights reserved.

This software was developed by the Pervasive Parallelism Laboratory of
Stanford University, California, USA.

Permission to use, copy, modify, and distribute this software in source
or binary form for any purpose with or without fee is hereby granted,
provided that the following conditions are met:

   1. Redistributions of source code must retain the above copyright
      notice, this list of conditions and the following disclaimer.

   2. Redistributions in binary form must reproduce the above copyright
      notice, this list of conditions and the following disclaimer in the
      documentation and/or other materials provided with the distribution.

   3. Neither the name of Stanford University nor the names of its
      contributors may be used to endorse or promote products derived
      from this software without specific prior written permission.


THIS SOFTWARE IS PROVIDED BY THE REGENTS AND CONTRIBUTORS ``AS IS'' AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
ARE DISCLAIMED. IN NO EVENT SHALL THE REGENTS OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY
OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF
SUCH DAMAGE.



7 References
====================================

    [1] S. Hong et al, "Green-Marl: A DSL for easy and efficient graph analysis", ASPLOS 2012
    [2] Green-Marl language specification (draft ver. 0.3)
    [3] G. Malewicz et al, "Pregel: A System for Large Scale Graph Processing", SIGMOD 2010.

