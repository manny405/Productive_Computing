
Introduction to Productive Computing
====================================

Productive scientific computing is the capability to produce more results in less time. Issues that may reduce productivity for new users include installing new software, setting up their computing environment correctly, and debugging. This document will provide an introduction to the software that are universally used for scientific computing in order to keep productivity high. Background information on computer environments with examples will be discussed first so the reader will have a good foundation. The purpose of this document is to inform the reader how to properly interact with the softwares available to them to be productive quickly in complex, scientific computing environments. 


Environment Variables
---------------------
Environment variables are analogous to variables that you may define while writing a piece of code. For example, in Python, you can say::
    
    >>> my_string = "Hello World"
    >>> print(my_string)
    "Hello World"
    
Environment variables are variables that live in your computing environment, and are associated with specific processes, such as your shell. Let's begin with an example. Open any ``bash`` terminal and type::

    $ echo "Hello World"
    Hello World
    $ MY_VARIABLE="Hello World"
    $ echo $MY_VARIABLE
    Hello World

``echo`` is analogous to the ``print`` function in ``bash``. You can see from the first command that ``echo PATH`` will just return the string ``PATH``. In order to access the values that are stored as environment variables, we need to add ``$`` before the name of the variable in bash. 

You may not know it, but there are many environment variables that have been pre-defined in your shell. In the same terminal, type::

    $ echo PATH
    PATH
    $ echo $PATH
    /Users/ibier/Software/anaconda3/bin:/usr/bin

The ``PATH`` environment variable is a colon separated list of directories. Your specific value for ``PATH`` may differ from mine. This is because you may be using a different computing environment than what I have setup on my laptop. Every computer, computer cluster, or supercomputer that you use will have a different environment, so it's important to pay attention to your environment variables.

So now that we know environment variables exist and we know how to access them, what are they used for? Environment variables can have many purposes, but the main idea is that they change the way that your computer will execute software. For example, by changing the ``PATH`` environment variable, you can change whether your computer will use Python 2 or Python 3. 

The ``PATH`` environment variable determines where you computer will look for executables. Executables, for example, are any command you type in  your terminal. ``ls``, ``cd``, ``pwd``, ``mpirun``, and ``python`` are all executibles. You can see the actual location of these executables on your computer by using the ``which`` command in your terminal::

    $ which cd
    /usr/bin/cd
    
When you specify an executable, the computer begins searching for it in the first directory provided in the ``PATH`` environment variable. If the computer doesn't find the correct executable there, it looks in the next directory in the list, and so on, until it finds it. If the computer does not find it, the ``which`` command returns nothing. Let's take a look at another example from my terminal::

    $ echo $PATH
    /Users/ibier/Software/anaconda3/bin:/usr/bin
    $ which python
    /Users/ibier/Software/anaconda3/bin/python
    $ PATH=/Users/ibier/Software/anaconda2/bin:$PATH
    $ echo $PATH
    /Users/ibier/Software/anaconda2/bin:/Users/ibier/Software/anaconda3/bin:/usr/bin
    $ which python
    /Users/ibier/Software/anaconda2/bin/python

By adding another directory to the beginning of the ``PATH`` environment variable, I've changed my Anaconda distribution from Python 3 to Python 2. This is because the computer found a Python executable in the ``anaconda2`` folder before finding Python in the ``anaconda3`` folder. Also, take a look at how I modified the ``PATH``. When I added the ``anaconda2`` directory to the ``PATH``, I ended the command with ``$PATH``. This is because I do not want to overwrite my entire ``PATH`` environment variable, I only want to append a new directory to the beginning. If you do overwrite your entire ``PATH``, you may lose the ability to  even use ``ls`` or ``cd``.

Let's walk through one last simple example to demonstrate how environment variables change that way the computer behaves. A character you may see ``bash`` users type all the time is ``~``. This key is linked to an environment variable named ``HOME``. Generally, you do not want to change the ``HOME`` environment variable, but let's take a look at what happens when we do::

    $ echo ~
    /Users/ibier
    $ cd ~
    $ pwd
    /Users/ibier
    $ HOME=~/Documents
    $ echo ~
    /Users/ibier/Documents
    $ cd ~
    $ pwd
    /Users/ibier/Documents

Using the knowledge you gained from the previous examples, it should be clear what has happened here. When I changed ``HOME``, it also changed the behavior of ``cd ~`` because ``~`` is equal to ``HOME``. This could be potentially disasterous.  For example, say I wanted to reference a script at the location ``/Users/ibier/test.py`` using ``python ~/test.py``. However, now say that I changed ``HOME`` to ``~/Documents`` which contains a different ``test.py``. I could waste a lot of time trying to debug my ``/Users/ibier/test.py`` program when in fact, the only error is that my environment variable is incorrect. Incorrect environment variables often lead to many errors that can be difficult to debug when doing scientific computing.

Environment variables are useful for easily switching between different softwares. However, if you need to use many softwares all together, changing the environment variables manually becomes a big headache, especially since changing variables manually is prone to error. Luckily, many people have also had this headache and have created intelligent solutions that we can use. 


Environment Modules
-------------------

Environment modules is one of the most universally used pieces of software in the world of scientific computing. You will find environment modules on every desktop, computer cluster, and supercomputer that we use. You may even install it on your own laptop easily if your using Linux or Windows Subsystem for Linux. Environment modules will manage your environment for you by appending directories and automatically removing paths from environment variables. 


``ssh`` to one of our local desktops (or any computer cluster) and type::

    $ module av

This will show you all of the ``modules`` available to you. Similarly, you can see all modules you are currently using by typing::

    $ module list

You can load a module into your computing environment by typing::

    $ module load intel

where ``intel`` is the desired module. After you are done, you can remove it by type::

    $ module unload intel

Remember, all the environment modules are doing are modifying environment variables. There's no magic involved. 

When anyone installs a new software, they should install it in a place that makes it available to everyone and create a module file to go along with it. By adhering to this **best practice**, only one person should ever have to go through the headache of installing new software. If you need to create a modulefile for yourself, any experienced system administrator should be able to help you. 


bashrc
------
The ``bashrc`` file, or on Mac the ``bash_profile``, exists in your home directory with a dot infront of it. It can always be accessed quickly by running::

    vi ~/.bashrc

The ``bashrc`` file is executed everytime that you log into your computer and everytime you start a new bash terminal. This file is used to set your standard environment variables, aliases, etc., which is the same as saying setting up your computing environment. The **best practice** when using a new resource is to for a standard ``bashrc`` file for the machine to load in the basics for a new resource. Also, I encourage you to look at the bashrc file and try to understand what environment variables and aliases are being used. 


Slurm
-----

Computer Cluster Architecture
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Before talking about job scheduling, let's begin with computer cluster architecture. Computer clusters are composed of many computers, called nodes, that have been networked together to communicate the execution of calculations. The cluster typically consists of the **head** node and **compute** nodes. The **head** node is the node that users log into. The **head** node also controls the scheduling of calculations to the **compute** nodes. The **compute** nodes are where calculations are supposed to take place. Users will not have access directly to the **compute** nodes so they will have to send production compute jobs to the **compute** nodes using the job scheduler. **Slurm** is one of the most popular job schedulers in the scientific computing community . This is because it's free, open-source, well documented, and easy to use.


Slurm Introduction
^^^^^^^^^^^^^^^^^^
Let's begin learning about Slurm with a couple commands. Log into a computer that uses Slurm to schedule jobs and run::

    >>> sinfo
    PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
    gpu*         up   infinite      7    mix c[002-003,006,009,016,018,028]
    gpu*         up   infinite     17  alloc c[004,007-008,010,013-015,017,019-027]
    gpu*         up   infinite      3   down c[005,011-012]
    cpu          up 7-00:00:00     25    mix d[007-008,
    cpu          up 7-00:00:00     43  alloc d[001-006,
    debug        up      10:00      2   idle e[001-002]
    idle         up 7-00:00:00      2   idle e[001-002]
    highmem      up 7-00:00:00      2  alloc e[003-004]

This computer cluster has been partitioned into five different partitiones, gpu, cpu, debug, idle, and highmem. Nodes in the same partition will have the same computer hardware, but nodes in different partitiones may not. For example, nodes in the ``gpu`` partition have GPUs installed, but nodes in the ``cpu`` partition do not. We can find out more about the resources that a specific node has by typing::

    >>> scontrol show node d001
    NodeName=d001 Arch=x86_64 CoresPerSocket=14
       CPUAlloc=56 CPUErr=0 CPUTot=56 CPULoad=0.01
       AvailableFeatures=(null)
       ActiveFeatures=(null)
       Gres=(null)
       NodeAddr=d001 NodeHostName=d001 Version=16.05
       OS=Linux RealMemory=128682 AllocMem=128682 FreeMem=110373 Sockets=2 Boards=1
       State=ALLOCATED ThreadsPerCore=2 TmpDisk=0 Weight=1 Owner=N/A MCS_label=N/A
       BootTime=2019-06-06T13:56:05 SlurmdStartTime=2019-06-06T13:58:34
       CapWatts=n/a
       CurrentWatts=0 LowestJoules=0 ConsumedJoules=0
       ExtSensorsJoules=n/s ExtSensorsWatts=0 ExtSensorsTemp=n/s
       
The most important information here is that the has 56 CPUs and 128 GB of RAM. These parameters limit the number of calculations the node can handle at one time. For example, it could be running 56, 1 core jobs or a single 56 core 
calculation.  

Let's go back and look again at the output of sinfo::
    
    PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
    cpu          up 7-00:00:00     43  alloc d[001-006,
    debug        up      10:00      2   idle e[001-002]

Looking at the STATE of the partition will tell you the inromation about nodes. In this case, all of the nodes in the ``cpu`` partition are allocated and the 2 nodes in the ``debug`` partition are both idle. Additionally, looking at the TIMELIMIT tells you how long jobs can run in each parititon. The ``debug``partition says that jobs may only run for 10 minutes. This partition is used for testing that your production calculations work before submitting it to the ``cpu`` partition, which may have a long queue.

Let's submit our first job to the Slurm schedule::

    >>> echo '#!/bin/bash
    #SBATCH -J test_job # Job name
    #SBATCH -n 1 # Number of total cores
    #SBATCH -N 1 # Number of nodes
    #SBATCH --mem-per-cpu=500 # Memory pool for all cores in MB (see also --mem-per-cpu)
    #SBATCH -o j_%j.out # File to which STDOUT will be written %j is the job
    #SBATCH -p debug
    
    echo "Hello Slurm"' > submit.sh
    >>> sbatch submit.sh

the ``sbatch`` command sends ``submit.sh`` to the Slurm jobs scheduler. The scheduler will interpret the lines beginning with ``#SBATCH`` in order to know how many resources the job requests and what partition to send the calculation. Once the Slurm scheduler detects that the requested resources are free, it will execute this script on the compute node. Note that all the lines begin with ``#``, which indicates a comment to the compute node, except for ``echo "Hello Slurm"``. So, all that will happen is the compute node will write ``"Hello Slurm"`` to STDOUT, which in this case will be the file ``j_%j`` where ``%j`` is the job id. 

To see the jobs that are currently being executed you can type::

    >>> squeue 
    >>> squeue -p cpu
    >>> squeue -p debug
    >>> squeue -u <insert your username here>

Please look at the Slurm online documentation for more information. https://slurm.schedmd.com/squeue.html

We may now combine Slurm submission scripts with module files by adding ``module load`` statements to the submission script. This is extremely powerful. It enables you to modify the computing environment on the compute node extremely easily for specific calculations. Also, it doesn't change anything about your current environment variables. It is **best practice**
to include all ``module load`` statements necessary for running the calculation, even if you have loaded these modules into your current environment already. 


Github
------
Github stores your code on the cloud. Github gives you an easy way to push your code to the cloud, which is almost universally installed on Linux computers, and an easy to pull your code from the cloud onto any new device. Sign-up for a Github account if you have not done so already. Then, watch a Youtube video that gives an introduction to what each git command does. Also, take notes during the video and begin compiling useful commands into a textfile. If you ever need to remember a command, you should check this file. 


FileZilla
----------

FileZilla is a great software for transfering files from external computers using a simple GUI interface. You can store the computers you would like to connect to under ``File->Site Manager``. Download and install FileZilla. It should very natural to begin using this software on your own. 


Visual Studio Code
------------------

Visual Studio Code is a integrated development environment (IDE) developed by Microsoft. Visual Studio Code has plug-ins for developing almost any type of code. Most import for us, it has an extension that can connect to the file system of external computers making developement much easier on these computers. The extension is called *SSH FS*. After watching a video online about how to get started using Visual Studio Code, install the extension *SSH FS*, authored by Kelvin Schoofs. Then, using ``cmd+shift+p`` on Mac or ``ctrl+shift+p`` on windows, type::

    create a SSH FS configuration
    
and click on the first option that comes up. Then, using the ``Global settings.json`` option, click ``Save``. Then add the 
``Host, Root, and Username`` and for the Password always use ``Prompt``. You may also add a private key.  Click ``Save. When you switch back to the ``Explorer``, you will see ``SSH FILE SYSTEMS`` at the bottom of the ``Explorer`` window. You should be able to see the new connection available. Connect and you will be able to navigate and open the files on the remote computer as if they were on your desktop. Please note that when you edit a file, the file is not resynced with the remote computer until you save the file.

When using Visual Studio Code, you may also open a terminal under the terminal menu at the top of the window. You can have a file on the remote server open in the editor with a terminal in the same directory all in one window.  

Windows Subsystem for Linux
---------------------------
Windows subsystem for Linux is a Microsoft supported solution for computing in a Linux operating system on your windows machine. Please follow the instructions on the official Window's documents webpage https://docs.microsoft.com/en-us/windows/wsl/install-win10 to begin using this great piece of software.


Conclusion and How to Get Help
------------------------------
The purpose of this document is to get you up and running on the complex computing environments you will find on computer clusters. You should now understand environment variables and the Slurm scheduler. Also, you have learned best practices that will save computer headaches. 

Lastly, if you do run into an error, do not panick. Errors are normal, and figuring out what is causing them is a good way to learn. First thing you should read the error carefully. Usually, it will try to tell you what's going on. If you have not seen the error before, and you can't figure out the meaning, try to Google the error message. If you still can't decifer 
the meaning, seek help.


