_______________________________
STAGE 3: OS STARTUP CODE TEST
_______________________________

- When the XSM machine starts, it executes the ROM Code present in page 0 of memory.
- The ROM Code is hard-coded into the machine and is also called the Boot ROM.
- The Boot ROM performs the following operations:
  - Loads block 0 of the disk to page 1 of memory (physical address 512).
  - Sets the value of the IP (Instruction Pointer) register to 512 so that execution begins at page 1.

_______________________________
TASKS PERFORMED IN THIS STAGE
_______________________________

- A simple assembly program was written to print "HELLO_WORLD" using the XSM instruction set.
- The program was then loaded into block 0 of the disk using the XFS-Interface as the OS Startup Code.
- The OS Startup Code is loaded from block 0 to memory page 1 by the ROM Code when the machine starts.
- After the ROM Code loads and transfers control to page 1, the OS Startup Code executes and prints "HELLO_WORLD" on the screen.

_______________________________
ASSEMBLY CODE USED
_______________________________

    MOV R0, "HELLO_WORLD"
    MOV R16, R0
    PORT P1, R16
    OUT
    HALT

- The program moves the string "HELLO_WORLD" into R0.
- It then moves this value into R16.
- Using the PORT instruction, the value is sent to output port P1.
- The OUT instruction prints the string to the screen.
- The HALT instruction stops execution.

_______________________________
LOADING OS STARTUP CODE TO DISK
_______________________________

- The program was saved as:
  `$HOME/myexpos/spl/spl_progs/helloworld.xsm`
- It was loaded into block 0 of the disk using XFS-Interface with the following commands:

  ```
  cd $HOME/myexpos/xfs-interface
  ./xfs-interface
  # load --os $HOME/myexpos/spl/spl_progs/helloworld.xsm
  # exit
  ```

- The `--os` option ensures the file is loaded into Block 0 of the XFS disk.

_______________________________
RUNNING THE MACHINE
_______________________________

- The machine was started using:
  ```
  cd $HOME/myexpos/xsm
  ./xsm
  ```
- The machine successfully executed the OS Startup Code and printed:
  ```
  HELLO_WORLD
  Machine is halting.
  ```

_______________________________
CONSEQUENCES & OBSERVATIONS
_______________________________

- The XSM simulator executes assembly language programs directly, unlike real systems that require binary execution.
- If the OS Startup Code is loaded into any page other than Page 1, XSM will not work properly.
  - This is because after the execution of the ROM Code, the IP register is set to 512 (start of Page 1).
  - If the OS Startup Code is not present at Page 1, the system will crash due to an exception.


