# FAQ

## What is Granular Cloning (GC)?

GC is a software-level execution enhancement to running replicated ensembles of simulation runs (e.g., Monte Carlo simulations), often leading to a significant speedup. Details can be found in the paper [Granular Cloning: Intra-Object Parallelism in Ensemble Studies](https://ccl.northwestern.edu/2018/p165-pecher.pdf).

## What is the main idea behind GC?

GC generalizes a concept called Simulation Cloning to arbitrary objects and interactions between more than two objects. When many similar simulation runs have to be executed, we should avoid computing and storing memory for sample paths that are invariant among groups of runs. Only divergent state - arising from random input or different scenarios - is explicitly computed by GC. The high-level idea is illustrated in the bottom half of this picture ("GC Method"):  
![SE vs GC](https://media-exp1.licdn.com/media-proxy/ext?w=800&h=800&f=n&hash=L64WeDsZ3OkE434L9HYxYboqPlg%3D&ora=1%2CaFBCTXdkRmpGL2lvQUFBPQ%2CxAVta5g-0R6jnhodx1Ey9KGTqAGj6E5DQJHUA3L0CHH05IbfPWjqLMXYcLaooUBHf30FjQAzfu61EjXhF460L43tLt50jJXmJ5H5agYUbhl4lWdI)

(The top half illustrates another execution enhancement I developed and is described [here](https://www.sciencedirect.com/science/article/pii/S1877050915011795/pdf?md5=03dca768e337b2fc0bc55382bab3127d&pid=1-s2.0-S1877050915011795-main.pdf).)

## Why are there only two source files and why does it violate well-known software design principles?

There were only two source files in order to streamline the artifact submission process for the original publication. I wrote the original code with the intent of being the only person to read and extend it. Clearly, there are many deviations from recommended software design principles in the current state. Time-permitting, I'll clean up the code in the future for ease of understanding and reuse.

## What kind of simulation models can GC be used for?

GC is applicable to both timestepped and event-driven execution engines. In distributed simulations, it can be used with both conservative and optimistic synchronization protocols. 

## What else have you planned for the future development of GC?

Possibilities include:
- Simple APIs for different simulation paradigms
- More extensive performance logging functionality
- Compatibility with various coordinate systems for simulating objects in space
- Automatic generation of state divergence (e.g., for sensitivity analysis)
- Enhanced data structure for groups of versions (including thread-safe options for parallel simulations on GPUs) in order to increase the performance further
- Enhanced real-time analytics on ensemble objects
- Relaxing accuracy in certain instances for even further speedup
- Tests for all of the above features
- Config.ini file for the cloning parameters

## How much faster is GC relative to traditional Simulation Cloning?

A performance model and several empirical results can be found in the [publication](https://ccl.northwestern.edu/2018/p165-pecher.pdf).

## Acknowledgements

I would like to wholeheartedly thank my advisor [Prof. Richard Fujimoto](https://www.cc.gatech.edu/~fujimoto/) for his guidance and Prof. Maria Hybinette for [pioneering Simulation Cloning](https://dl.acm.org/citation.cfm?id=508370). Only by standing on their shoulders could GC exist. I'd also like to thank [ACM-SIGSIM](https://www.acm.org/special-interest-groups/sigs/sigsim), [NSF](https://www.nsf.gov/) and [AFOSR](https://www.wpafb.af.mil/afrl/afosr/) for their support. 

## ENVIRONMENT/EXECUTION PLATFORM

MSMPI v8.1 ( see https://msdn.microsoft.com/en-us/library/bb524831(v=vs.85).aspx )
in conjuction with the default C++ compiler in 
[Microsoft Visual Studio Community 2017](https://www.visualstudio.com/downloads/)

[This website](https://blogs.technet.microsoft.com/windowshpc/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program/) shows simple installation steps and how one can verify that MSMPI working properly:

##SCRIPTS

To replicate the results of Figures 5-6, please compile the contents of the serial_cloning folder (SerialGranularCloning.cpp) with a C++11 compatible compiler. 
Running it will show the speedup in console_out after less than one minute, for the appropriate parameters being set as follows prior to compiling and running:
For Figure 5, the speedup figures for
- the x axis can be obtained by altering Line 73
static unsigned int DUMMY_ACCESSES = x; //x can be 0,1,..., 32
- the actual colored plots can be obtrained by altering Line 654:
	const int NUM_CELLS = y; //y can be 10000 (red), 100000 (green), 1000 (blue)

For Figure 6, the speedup figures are obtained by altering the following variable on Line 658
	const double DECISION_POINTS_START = z; //for blue z is 0.0, for red it is 0.5, and for green it is 0.8
while also altering the x axis values on Line 73
static unsigned int DUMMY_ACCESSES = x; //x can be 0, 4, 32


To replicate the results of Tables 1-4:
Please 
(i) import the attached source files of the distributed_cloning folder into a Visual Studio, Visual C++ > Windows Desktop > Windows Console application, and
(ii) Build > Build Solution, followed by 
(iii) going into the Debug folder of the solution with the Command Command Prompt (> cd <Debug Folder File System Address>) and 
(iv) executing the program with >mpiexec -n 2 ITLUM.exe
Execution time will take several hours and print the speedup figures in the Command Prompt as soon as the results for a scenario are available
"dummy" in the output refers to the number of additional memory accesses in the kernel (artificial uniform sampling), 
"cells" to the number of cells in the simulation, 
"cells_by_ticks_params" refers to the cell-to-timestamp ratio, and
"decision-start" refers to the execution progress threshold (in%) after which variation is introduced into the simulation.





