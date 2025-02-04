For more information about SWAPHI-LS, please refer to the homepage http://swaphi-ls.sourceforge.net.

Introduction:
SWAPHI-LS is the first parallel Smith-Waterman algorithm exploiting Intel Xeon Phi clusters to accelerate the alignment of long DNA sequences. This algorithm is written in C++ (with a set of SIMD intrinsic functions), OpenMP and MPI. The performance evaluation revealed that our algorithm achieves very stable performance, and yields a performance of up to 30.1 GCUPS on a single Xeon Phi and up to 111.4 GCUPS on four Xeon Phis sharing a host (compiled by Intel C++ compiler version 14.0.1 along with OpenMPI version 1.6.5).

Citation:
(1) Yongchao Liu, Tuan-Tu Tran, Felix Lauenroth, Bertil Schmidt: "SWAPHI-LS: Smith-Waterman algorithm on Xeon Phi coprocessors for long DNA sequences". 2014 IEEE International Conference on Cluster Computing, 2014, pp.257-265 


Parameters:
Two executable binaries will be generated after compiling and linking: swaphi-ls and mpi-swaphi-ls. The program swaphi-ls does not rely on MPI library and targets a single Xeon Phi. The progrom mpi-swaphi-ls must be compiled with an MPI library and is designed for Xeon Phi clusters.
Input:

    -i < str> (query DNA sequence file [REQUIRED])
    -j < str> (subject DNA sequence file [REQUIRED])
    -k < int>(place the longer sequence horizontally, default = 1) 

Scoring scheme:

    -m < int> (match score, default = 1)
    -M < int> (mismatch penalty, default = 3)
    -g < int> (gap opening penalty, default = 5)
    -e < int> (gap extension penalty, default = 2) 

Tiling:

    -a < int> (the vertical tile size within each device, default = 16 [NO need to change])
    -b < int> (the horizonal tile size within each device, default = 0 [0 means auto])
    -c < int> (enable the tiling, default = 1)

    only applicable for the non-MPI-based version.
    -C < int> ((the vertical block size between devices, default = 131072)

    only applicable for the MPI-based version.

Compute:

    -t < int> (number of threads per Xeon Phi, deafult = 0 [0 means auto])
    -x < int>(index of the Xeon Phi used, default = 0)

    only applicable for the non-MPI-based version.
    -n < int>(simulate #int characters, deafult = 0 [SPEED TEST]) 

Installation and Usage:
Prerequisite

    Intel C/C++ compiler or any other C/C++ compiler that supports Xeon Phi coprocessors.
    A C/C++ MPI library (e.g. OpenMPI, MPICH, Intel MPI) that is compiled by the aforementioned C/C++ compiler. 

Compile:

Before compiling, please modify the corresponding Makefile to point to the correct compilers and libraries.

    To compile the non-MPI-based version, please type command "make -f Makefile.phi".
    To compile the MPI-based version, please type command "make -f Makefile.mphi".
    To compile both versions, please type command "make". 

Typical Usage:
Non-MPI-based program swaphi-ls

    export KMP_AFFINITY=balanced; swaphi-ls -i seq1.fa -j seq2.fa
    export KMP_AFFINITY=balanced; swaphi-ls -i seq1.fa.gz -j seq2.fa.gz -x 2 

MPI-based program mpi-swaphi-ls

    export KMP_AFFINITY=balanced; mpirun -np 4 mpi-swaphi-ls -i seq1.fa -j seq2.fa
    export KMP_AFFINITY=balanced; mpirun -hostfile host.file -np 4 mpi-swaphi-ls -i seq1.fa.gz -j seq2.fa.gz 

Configure hostfile for MPI-based program

when running on a Xeon Phi cluster, you must make sure that the number of MPI processes running on a node must not be more than the number of available Xeon Phis. This constraint can be ensured using a host file.

    An example of MPICH host file is as follows, where each node contains two Xeon Phis:

    compute-0-0:2
    compute-0-1:2
    compute-0-2:2
    compute-0-3:2
    An example of OpenMPI host file is as follows, where each node contains two Xeon Phis:

    compute-0-0 slots=2
    compute-0-1 slots=2
    compute-0-2 slots=2
    compute-0-3 slots=2

Example Executon:

	There are two DNA sequences in the "data" subdirectory of the source code.
After execute the command:
	./swaphi-ls -i data/seq1.fa -j data/seq2.fa

you will get the following console output, where the last line gives
the optimal local alignment score between the two sequences: one is in file
seq1.fa and the other in seq2.fa.

Start loading sequences
Query: sequence_1
	length: 500
Target: sequence_2
	length: 498
Finish loading sequences
Sequence loading takes 0.000796 seconds
Horizontal tile size: 16
Vertical tile size: 16
Number of horizontal tiles: 32
Number of vertical tiles: 32
User-specified number of threads: 236
Computation time on the Xeon Phi: 0.045923 seconds
Performance in GCUPS: 0.005708 GCUPS
Optimal local alignment score: 489


