
# Fundamentals of Computer Systems (FoCS) - Master Notes

## 1. Digital Data Representation

**Core Axiom:** Data + Context = Information.

### The Binary System

- **Bit:** BInary digiT (0 or 1). Physical representation: voltage levels or polarity.
    
- **Byte:** 8 bits.
    
- **Word:** 2 bytes (16 bits).
    
- **Capacity Formula:** $N$ bits = $2^N$ possible combinations.
    
- **Accuracy vs. Cost:** More bits = higher accuracy (e.g., 24-bit color vs 8-bit) but requires more storage, longer processing time, and higher latency.
    

### Number Bases & Conversion

- **Decimal (Base 10):** Human standard.
    
- **Binary (Base 2):** Machine standard.
    
- **Hexadecimal (Base 16):** Human-readable shorthand for binary ($1 \text{ hex digit} = 4 \text{ bits}$).
    
    - $10-15$ represented as $A-F$.
        
    - **Notation:** Suffix ($2, 10, 16$) or Prefix ($\%, \#, \$$).
        

### Error Handling

- **Parity Checking:** Adding a bit to ensure the total number of 1s is even (Even Parity) or odd.
    
    - **Fatal Flaw:** If 2 bits flip, parity appears correct. It detects 1-bit errors but cannot locate or fix them.
        
- **Error Correction:** Organizes data into rows and columns (matrix) to pinpoint the exact bit that failed.
    

## 2. Memory Architecture

The "Memory Wall" is the constant struggle between size, cost, and speed (latency).

### The Hierarchy (Fastest/Smallest to Slowest/Largest)

1. **Registers:** In the CPU. Single-cycle access.
    
2. **Cache (SRAM):** Static RAM. No refresh needed. Expensive.
    
    - **L1:** Fastest, embedded in CPU.
        
    - **L2/L3:** Shared across cores, larger but slower than L1.
        
    - **Hit vs. Miss:** 95% hit rate in modern systems. A "Miss" forces a slow fetch from RAM.
        
3. **Primary Memory (DRAM):** Dynamic RAM. Requires constant electrical refresh. 50x slower than Cache.
    
4. **Secondary Storage:** HDD (Magnetic/Mechanical) or SSD (Flash/Floating Gate). Non-volatile.
    

### Storage Comparison

|Feature|HDD|SSD|
|---|---|---|
|**Technology**|Rotating platters, actuators|Flash memory, no moving parts|
|**Access**|Sequential/Fragmented|Random/Instant|
|**Reliability**|Mechanical failure risk|Resilient, silent, low power|

### Virtual Memory

- **Definition:** Logical memory that allows execution of programs larger than physical RAM.
    
- **Mechanism:** Swaps "pages" of data between RAM and Disk.
    
- **Abstraction:** The programmer sees a contiguous memory space, even if it is physically scattered across different media.
    

## 3. Operating Systems (OS) 101

The OS is the resource manager sitting between Hardware and the User/Apps.

### Bootstrapping (The Startup)

1. **BIOS (Basic I/O System):** Firmware in ROM.
    
2. **POST (Power-On Self-Test):** Checks hardware config.
    
3. **Bootloader:** BIOS finds the bootable device and transfers execution to the OS (e.g., NTLDR, LILO).
    

### Process Management

- **Program:** Passive code on disk.
    
- **Process:** Active instance in memory.
    
- **Resources Owned:** Machine code, Data/Stack/Heap, File handles, Security attributes, CPU Context (registers).
    
- **States:** `Created` $\to$ `Waiting` $\to$ `Running` $\leftrightarrow$ `Blocked` $\to$ `Terminated`.
    

### CPU Scheduling Algorithms

- **FCFS (First Come First Served):** Simple but creates long wait times.
    
- **SJF (Shortest Job First):** Optimal average wait time, but requires "guessing" job length.
    
- **Round Robin (RR):** Time-slicing (10-100ms). Fair, but high overhead if slices are too short.
    
- **Priority:** Higher priority tasks pre-empt lower ones.
    

## 4. Linux Mastery (The BASH Shell)

### Navigation & Paths

- **Absolute:** Starts from `/` (root).
    
- **Relative:** Starts from current directory.
    
- **Special Symbols:**
    
    - `.` : Current directory.
        
    - `..` : Parent directory.
        
    - `~` : Home directory.
        
    - `*` : Wildcard for any characters.
        

### Essential Commands

|Command|Action|
|---|---|
|`pwd`|Print Working Directory.|
|`ls -l`|Long listing (permissions, size).|
|`touch`|Create empty file.|
|`cat`|Concatenate/View files.|
|`head -n X`|View first X lines.|
|`tail -n X`|View last X lines.|
|`mv`|Move or **Rename** files.|
|`cp -i`|Copy (interactively ask before overwrite).|
|`rm -r`|Recursive delete (required for directories).|
|`ps`|View running processes.|
|`kill -9`|Force-terminate a process by PID.|
|`man`|Open manual/help page.|

### Shell Scripting Fundamentals

- **File extension:** `.sh`. Run via `bash scriptname.sh`.
    
- **Variables:** Assign `VAR="value"`, Access `$VAR`.
    
- **User Input:** `read VARIABLE_NAME`.
    
- **Command Line Args:** `$1` (first arg), `$2` (second arg), etc.
    
- **Calculations:** - Integers: `$(($VAR1 + $VAR2))`
    
    - Floating point: Use pipe to `bc` (Basic Calculator) $\to$ `echo "2.5 * 3" | bc`.
        

### Control Structures

#### If Statements (Numeric)

```
if [ $x -eq 10 ] # -eq, -ne, -lt, -gt, -le, -ge
then
    echo "Equal"
fi
```

#### If Statements (Strings)

```
if [[ $string == "blue" ]] # Use double brackets for strings
then
    echo "Matches"
fi
```

#### While Loops

```
x=1
while [ $x -le 5 ]
do
    echo "Iterating"
    (( x++ )) # Increment
done
```

### Benchmarking Strategy

To measure system performance, wrap a command in a loop and capture the time:

```
date +'%M %S %N' # Minutes, Seconds, Nanoseconds
# [While Loop carrying out calculations]
date +'%M %S %N'
```