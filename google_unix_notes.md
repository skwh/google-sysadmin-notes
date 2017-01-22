Should address:

* internals: filesystems, sockets, inode, system calls enables sockets
* systems: process & memory management, unix process management, how processes can communicate
* system calls, signals, the kernel, command lines, shell, network & application tracing
* Kernels, libraries, system calls, memory management, permissions, name-base servers, file systems, client-server protocols, understanding the shell.

>"Know about processes, threads and concurrency issues. Know about locks and mutexes and semaphores and monitors and how they work. Know about deadlock and livelock and how to avoid them. Know what resources a processes needs, and a thread needs, and how context switching works, and how it's initiated by the operating system and underlying hardware. Know a little about scheduling. The world is rapidly moving towards multi-core, so know the fundamentals of "modern" concurrency constructs."

---

# UNIX notes

## kernel

>"The kernel is a program that constitutes the central core of a computer operating system. It has complete control over everything that occurs in the system."

>"The kernel provides basic services for all other parts of the operating system, typically including memory management, process management, file management and I/O (input/output) management (i.e., accessing the peripheral devices). These services are requested by other parts of the operating system or by application programs through a specified set of program interfaces referred to as system calls."

>"Process management, possibly the most obvious aspect of a kernel to the user, is the part of the kernel that ensures that each process obtains its turn to run on the processor and that the individual processes do not interfere with each other by writing to their areas of memory. A process, also referred to as a task, can be defined as an executing (i.e., running) instance of a program.

>The contents of a kernel vary considerably according to the operating system, but they typically include (1) a scheduler, which determines how the various processes share the kernel's processing time (including in what order), (2) a supervisor, which grants use of the computer to each process when it is scheduled, (3) an interrupt handler, which handles all requests from the various hardware devices (such as disk drives and the keyboard) that compete for the kernel's services and (4) a memory manager, which allocates the system's address spaces (i.e., locations in memory) among all users of the kernel's services.

## software interrupt

an interrupt is a signal to the kernel that an event has occured, which will then result in changes to the sequence of instructions that are executed by the CPU.

## filesystem

a filesystem is:

>"a way of storing information on a computer that usually consists of a hierarchy of directories (also referred to as the directory tree) that is used to organize files. Each hard disk drive (HDD) or other storage device as well as each partition (i.e., logically independent section of a HDD) can have a different type of filesystem if desired." -linfo

There are different types of filesystems. Each type of filesystem has different rules about how to control allocation of disk space and what kind of metadata to store about each file.

the most common types of filesystems on linux are:

### ext2

ext2 means "second extended filesystem," and is the most basic and portable of the native linux filesystems.

the original ext filesystem had problems with fragmentation and partition limits. ext2 fixed those problems.

### ext3

ext3 means "third extended filesystem," and adds a journaling capability to the filesystem.

journaling is a practice where the system keeps a log, or journal, of all of the operations that happen to it. this way, if there is an unclean shutdown of the computer, the filesystem state can easily be restored.

ext3 is also faster than ext2, because journaling increases the speed of the HDD write head.

because ext3 relies so heavily on ext2, most ext2 tools work with ext3 and so ext3 is extremely reliable.

### root filesystem

the root filesystem is the filesystem that is contained on the same partition as the root directory. it is under this filesystem that all the other directories are contained, and where the system is booted up.

>"The root filesystem should generally be small, because it contains critical files and a small, infrequently modified filesystem has a better chance of not becoming corrupted. A corrupted root filesystem will generally mean that the system becomes unbootable (i.e., unstartable) from the HDD, and must be booted by special means (e.g., from a boot floppy)." -linfo

## system calls

>"A system call, sometimes referred to as a kernel call, is a request in a Unix-like operating system made via a software interrupt by an active process for a service performed by the kernel."

a system call is a request by an active process for a service performed by the kernel, such as input output or process creation.

## threads

threads are different from processes in that threads run in shared memory space, whereas processes run in their own separate memory spaces. threads also exist within processes and share that processes' virtyual address space and resources. threads also have unique properties:

1. exception handlers
2. scheduling priority
3. thread local storage
4. unique identifier
5. a set of structures the system can use to save the thread context

the thread's context includes things like the thread's set of machine registers, the kernel stack, a thread environment block, and a user stack int he address space of the thread's process. threads can also have a security context

## processes

a process is a running instance of a program, also called a task.

processes are dynamic in that they vary based on which of their machine code instructions are being executed by the cpu.

every process consists of:

1. the system resources that are allocated to it;
2. a section of memory;
3. security attributes, such as its owner and its set of permissions;
4. and the processor state.

the processor state includes the contents of its registers and their physical memory addresses.

a process could also be defined as the execution context of a running program, i.e. all of the activity of the current time slot in the CPU.

because linux and unix are time sharing operating systems, processes only run one at a time, but the processor switches between them fast enough for the users to believe they are happening simultaneously.

each process is given a unique number: a process identification number (PID). this number is used by the system to reference the process. each process has a unique, non-negative integer PID. PIDs are not usually reused quickly in order to prevent possible errors.

when a process is running, it can spawn other processes. This is accomplished through the `fork` system call.

when a process spawns a new process, it first duplicates itself, which then transforms into the new process, which can also create new processes. processes are therefore related to each other in the same way that children are to grandchildren or in an OOP hierarchy.

the kernel is not a process.

### resources

a process utilizes a variety of system resources. this includes:

1. processor time during which the process' instructions are run
2. the memory to hold the proccess and its data
3. files within the filesystem
4. physical devices on the system

the OS keeps track of each process and its resources in order to ensure that one process or another does not monopolize processor time or memory space.

if a process is suspended, it becomes eligible for swapping. if it is swapped, it is taken out of memory and placed in the swap partition, freeing up memory for other processes.

### daemons

daemons are a special class of processes that run exclusively in the background. these processes are generally easy to recognize because their names will end in `d`.

daemons are usually launched automatically and stay in the background until their services are required.

examples of daemons include `sshd`, which runs the SSH service, `devfsd`, which manages device entries in the device filesystem, and `crond`, which runs processes at scheduled times.

on Windows, daemons are called Services.

## program

a program is an executable file which is held in storage. the executable file is a binary file, which has been compiled and can be read directly into the CPU.

programs are passive, until they are launched, when they become processes.

## concurrency

concurrency is the idea that a computer is executing multiple tasks at once independently. this is usually performed through context switching, where processes are switched between fast enough that it appears that they are executing simultaneously.

### multi-core / modern

on a CPU with multiple processors, or cores, each core runs a separate instance of the OS' scheduler, which can run each thread independently, and therefore at the same time (in parallel).

## locks

locking is a mechanism which restricts access to a file by allowing only one user or process access to it at any specific time. on unix and linux, open files are not automatically locked, and locks are considered advisory: they can be ignored at-will by other processes.

## mutexes

a mutex is a program object that allows multiple program threads to share the same resource, such as file access, but not simultaneously. when a process is started, a mutex is created with a unique name.

after this stage, any thread that needs the resource must lock the mutex from other threads whole it is using the resource. the mutex unlocks when the data is no longer needed or the processes or routine is finished.

## semaphores

a semaphores is a variable or abstract data type which is used to control access to a common resource by multiple processes in a concurrent system.

semaphores differ from mutexes in that mutexes are locked by the thread using the resource, which allows them to implement additional features. semaphores simply hold values which are given meaning by developers.

## monitors

in concurrent programming, a monitor is a synchronization construct that allows threads to have mutual exclusion and the ability to wait (block) for a certain condition to be true. monitors also have a mechanism for signalling other threads that their condition has been met.

monitors consist of mutexes and condition variables: a container of threads which are waiting for that condition to be met. 

## deadlocks / livelocks

a deadlock is when each member of a group of actions is waiting for some other member to release a lock.

this is mainly a problem in multiprocessing / parallel computing / distributed systems scenarios, when software locks (see above) are used to handled shared resources.

### handling deadlocks

most operating systems cannot prevent deadlocks and handle them in strange ways.

#### ignoring deadlocks

this assumes deadlocks will never occur.

#### detection

deadlocks are allowed to occur, and then the system finds them and corrects them. correcting usually means terminating one of the processes involved in the deadlock or reallocating the locked resources.

#### prevention

deadlock prevention works by ensuring that deadlock conditions are never met and therefore deadlocks cannot happen.

### livelocks

a livelock is when the state of each of the processes involves is constantly changing, and none of them progress anywhere.

this is especially probable with deadlock detection systems, as more than one process may take action to correct a deadlock and the detection may be repeatedly triggered.

livelocking is a special case of resource starvation.

### distributed deadlock

deadlocks which occur in distributed systems where concurrency control is being used.

## context switching

a context switch is the switching of the CPU between processes. this has to do with linux and unix being time sharing operating systems.

the context that is being switched between is the contents of the CPU's registers and program counter at any point in time.

in detail, context switching is described as this set of activities being performed on the CPU:

1. suspending the progression of one process and storing its state for that process somewhere in memory;
2. retrieving the context from memory and restoring it in the CPU's registers;
3. returning to the location indicated by the program counter in order to resume the process.

context switching is, in short, the kernel suspending progression of a process on the CPU and resuming progression of another process that had previously been suspended.

### cost

context switching is computationally intensive, therefore a major focus of the design of operating systems is to avoid unnecessary context switching.

## kernel mode

kernel mode is a privledged mode of the CPU in which only the kernel runs and which provides access to all memory locations and all other system resources. Other programs operate in user mode, but those programs can run portions of the kernel code via system calls.


### mode shifting

when a system call is executed, a mode shift occurs, shifting the CPU from user mode to kernel mode. this does not interfere with context switching because the process is still running in the CPU.


## scheduling

an OS' scheduler is a system within the kernel which decides what processes get run when, on which of the CPU's processors.

## signals

## inodes

an inode is a datastructure on linux which stores information about a file except its name and its actual data. (it stores metadata.)

when a file is created, it is associated with an inode number, which is an integer that is unique within the filesystem. the directory then associates the filenames with inodes.

whenever a program or user mentions a file by name, the system uses the name to look up the inode, and then uses the data in the inode to perform further operations with the file.

detaching file data from their names allows hard links to be implemented, that is, you can have multiple names for one file. so when a new file is created with the same name as another file, the filename simply points to the inode, rather than a complete duplicate of the data and its metadata.

the data contained in an inode includes:
1. the size of the file in bytes
2. the physical location of the file on disk (the addresses of the blocks of storage)
3. file's owner and group
4. file's access permissions
5. timestamps for creation, modification, and last accessed
6. reference count, telling how many pointers there are to the node

## sockets

socket has two meanings. the first refers to a socket in the sense of the third layer of the IPS (internet protocol suite). the relevant one here is more formally called a Unix Domain Socket, or Inter-Process Communication socket (IPC socket).

IPC sockets underlay a system for exchanging data between processes on the same host operating system. Like TCP, but without connecting to another computer.

In the case of IPC sockets, the stream of bytes is called the SOCK_STREAM, which consist of a reliable and ordered transmission of datagrams (SOCK_SEQPACKET, cf TCP), or an unreliable and unordered transmission (SOCK_DGRAM, cf UDP).

IPC sockets, like everything else on unix, are files, and so two processes can send information to the same socket through the use of inodes.

in order to send messages between processes, applications can use the `sendmsg()` and `recvmsg()` system calls.

## command line

a command line is the space to the right of the command prompt of an all text display mode on a computer monitor in which a user enters commands and data.

this provides a line of communication between the computer and its user which is soley based on textual input and output.

all text display mode, also called a Command Line Interface (CLI), can be provided by a console or a terminal window. a console is a display mode wherein the entire screen shows only text and no images. a terminal window is a console, except emulated inside a window of a GUI.

the command prompt is a short, automatically generated message at the beginning of the command line which has two functions:

1. inform a user that the system is ready for the next command
2. help the user plan and execute subsequent operations

when using the bash shell, the command prompt contains the name of the user, the name of the computer, and the name of the current directory:

`[andrew@localhost work]$`

command line, command prompt, and shell prompt are sometimes used interchangably.

## shells

a shell is a program that provides a text-only interface on unix and linux. it reads and executes the commands given to it.

the term shell derives from the idea that it is the outer layer of an operating system; it is the interface between the user and the guts of the operating system, like the kernel.

shells have multiple functions. they:

1. execute programs
2. substitute variables and file names in commands
3. perform IO
4. redirect the output from a program to another destination
5. user environment control
6. run shell-specific programming languages

on the last point, shells in unix are unusal in that they are both an interactive command language, and a scripting language.

### fun facts

to start a process in the background on a shell, type the name of the process, followed by an ampersand.

`gftp &`

to terminate a process running in the foreground, type CTRL+C, and to suspend a process running in the foreground, type CTRL+Z.

`bg` reactivates a background process, and `fg` puts a suspended program or a program that is running in the background, in the foreground.

## network tracing

## application tracing

## libraries

## memory management

## permissions

on unix/linux, every object has permission associated with it. this provides for a high level of security and stability.

every object has three types of permissions: read, write, and execute. each of these three types is defined for three types of users: the owner of the object, the group of the owner, and all other users. the owner is, by default, the user that created the object.

the root user has all three types of permissions for every object on the system. the root user can change permissions on any object and transfer ownership between any users on the system.

these permissions have different meanings for directories than for other types of files. for directories, read permissions mean that a user can see the names and information about objects contained in the directory. write permissions mean the user can write to the directory, and execute means the user can open objects within the directory.

each permission is defined by a bit, and because there are three groups of nine permissions, there are nine bits of permission information associated with each object in the system. each bit is either permitted or denied (on or off, 1 or 0, etc.)

these permissions are most often seen in a 10 character string representation when using the `ls -l` command. for example:

`-rwxrw-r--`

the first character designates the type of object: `-` for a regular file, and `d` for a directory. the next three characters are the read (r), write (w), and execute (x) permissions for the owner. the next three are for the group, and the last three for all other users. if a permission is denied, it is shown as a dash (-).

permissions are also shown more compactly as a group of three octal (base eight) digits. the three digits similarly stand for user permissions, group permissions, and etc permissions.

the permissions string above would be represented as the octal group `764`.

each octal digit has a different meaning:

(0) no access
1. execute only
2. write only
3. write and execute
4. read only
5. read and execute
6. read and write
7. read, write, and execute

## name-base server

## client-server protocols
