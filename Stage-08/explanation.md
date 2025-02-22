_______________________________
Stage 8: Timer Interrupt Handler Implementation
_______________________________

- In this stage, we implement a simple timer interrupt handler for the XSM simulator. The interrupt will occur every 20 instructions in user mode, and the handler will print "TIMER" before returning control to the user program.

_______________________________
Modifications to OS Startup Code
_______________________________

- **Load Timer Interrupt Routine into Memory:**
  - The timer interrupt routine is stored in disk blocks 17 and 18.
  - During OS startup, it is loaded into memory pages 4 and 5.
  - The timer interrupt routine must be loaded into memory at the correct physical address for it to function properly.
  
- **Steps to Load the Interrupt Routine:**
  - Add the following instructions to load the interrupt routine:

    ```
    loadi(4, 17);
    loadi(5, 18);
    ```

- **Setting Up Timer Interrupt:**
  - The timer interrupt routine is written to print "TIMER" and return to the user program.

    ```
    print "TIMER";
    ireturn;
    ```

  - This is a basic interrupt handler that prints a message and returns to the user program.

_______________________________
Timer Interrupt Routine
_______________________________

- **Write the Timer Interrupt Routine:**
  - Save the routine in the UNIX machine at the following location:

    ```
    $HOME/myexpos/spl/spl_progs/sample_timer.spl
    ```

- **Compile the Routine:**
  - Compile the routine using the SPL compiler.

- **Load the Compiled XSM Code into XSM Disk:**
  - Use the XFS Interface to load the compiled code. Run the following commands:

    ```
    cd $HOME/myexpos/xfs-interface
    ./xfs-interface
    # Load the interrupt routine
    # load --int=timer $HOME/spl/spl_progs/sample_timer.xsm
    # Exit the XFS Interface
    # exit
    ```

- **Recompile and Reload OS Startup Code:**
  - Recompile and reload the OS Startup code to include the new timer interrupt routine.

_______________________________
Running the XSM Machine
_______________________________

- **Run XSM Machine with Timer Enabled:**
  - Run the XSM machine with the timer enabled by executing:

    ```
    cd $HOME/myexpos/xsm
    ./xsm --timer 2
    ```

_______________________________
Outcome
_______________________________

- Once the steps are completed, the XSM machine will run with the timer enabled. Every 20 instructions executed in user mode will trigger the interrupt, causing the message "TIMER" to be printed.

_______________________________
Consequences
_______________________________

- This stage introduces the basics of interrupt handling in the operating system, specifically focusing on timer interrupts. This is foundational for more complex scheduling and interrupt management tasks in future stages.
