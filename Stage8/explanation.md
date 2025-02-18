Stage 8 - Timer Interrupt Handler Implementation

Objective:
In this stage, we focus on implementing a simple timer interrupt handler for the XSM simulator. The interrupt will occur after every 20 instructions, and our handler will print "TIMER" before returning control to the user program.

Modifications to OS Startup Code:
1. Modify the OS startup code to load the timer interrupt routine from disk blocks 17 and 18 into memory pages 4 and 5. The instructions for this are:
   
   - loadi(4, 17);
   - loadi(5, 18);

This ensures the timer interrupt routine is available at the correct memory location when triggered.

Timer Interrupt Routine:
The timer interrupt routine is written to print the message "TIMER" and then return to the user program. The code for the interrupt routine is:

   - print "TIMER";
   - ireturn;

This simple handler serves as a starting point for further timer-related tasks.

Steps for Implementation:

1. Write the timer interrupt routine and save it in your UNIX machine at:
   
   $HOME/myexpos/spl/spl_progs/sample_timer.spl

2. Compile the routine using the SPL compiler.

3. Load the compiled XSM code into the XSM disk using the XFS Interface with the following commands:

   cd $HOME/myexpos/xfs-interface
   ./xfs-interface
   # load --int=timer $HOME/spl/spl_progs/sample_timer.xsm
   # exit

4. Recompile and reload the OS Startup code to include the new timer interrupt routine.

5. Run the XSM machine with the timer enabled:

   cd $HOME/myexpos/xsm
   ./xsm --timer 2

Outcome:
Once these steps are completed, the XSM machine will run with the timer enabled. Every 20 instructions executed in user mode will trigger the interrupt, causing the "TIMER" message to be printed.

Consequences:
This stage lays the foundation for handling hardware interrupts in the operating system, specifically for timer-based events. In future stages, more complex interrupt handling and scheduling routines can be implemented.


