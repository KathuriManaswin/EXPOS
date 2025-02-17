____________________________
STAGE 5 - DEBUGGING WITH BREAKPOINTS
____________________________

- In this stage, we write an SPL program that includes a **breakpoint statement**.
- The **breakpoint statement** translates to the `BRKP` machine instruction and is useful for debugging.
- When the **XSM machine** runs in **debug mode**, encountering the `BRKP` instruction suspends execution, allowing us to inspect the state of the machine.
- Execution resumes only after we instruct the simulator to proceed.

____________________________
CREATING THE PROGRAM
____________________________

- Write an SPL program to generate **odd numbers from 1 to 10** with a debug instruction in between.

  **SPL Code:**
  ```
  alias counter R0;
  counter = 0;
  while(counter <= 10) do
    if(counter%2 != 0) then
      breakpoint;
    endif;
    counter = counter + 1;
  endwhile;
  ```

- Save this file as:
  ```
  $HOME/myexpos/spl/spl_progs/debug_oddnos.spl
  ```

____________________________
COMPILING AND LOADING THE PROGRAM
____________________________

1. **Compile the SPL program using the SPL compiler**:
   ```sh
   cd $HOME/myexpos/spl
   ./spl $HOME/myexpos/spl/spl_progs/debug_oddnos.spl
   ```
   - The compiled XSM code will be generated in `debug_oddnos.xsm`.

2. **Load the compiled XSM code as OS startup code into the XSM disk using XFS-Interface**:
   ```sh
   cd $HOME/myexpos/xfs-interface
   ./xfs-interface
   # load --os $HOME/myexpos/spl/spl_progs/debug_oddnos.xsm
   # exit
   ```

____________________________
RUNNING THE MACHINE IN DEBUG MODE
____________________________

1. **Start the XSM machine in debug mode**:
   ```sh
   cd $HOME/myexpos/xsm
   ./xsm --debug
   ```
   - The machine will pause after encountering the first `BRKP` instruction.

2. **View the contents of registers**:
   ```
   reg
   ```
   - This command displays the current values of all registers.

3. **Inspect the memory contents**:
   ```
   mem 1
   ```
   - This writes the contents of **memory page 1** to a file named `mem` inside the `xsm` folder.
   - Open and view the `mem` file to examine memory contents.

4. **Step to the next instruction**:
   ```
   s
   ```
   - This moves execution to the next instruction.

5. **Continue execution till the next `BRKP` instruction**:
   ```
   c
   ```
   - You can observe the changing value of register `R0` during each iteration.

____________________________
OBSERVATIONS
____________________________

- Each time the program reaches the `BRKP` instruction, execution halts, allowing inspection.
- The value of **register R0** changes in each iteration, reflecting the updated counter value.
- Using debugging commands, we can track how the program behaves at different execution points.

____________________________
CONCLUSION
____________________________

- **Breakpoints** help analyze program execution step-by-step.
- Using the debug mode, we can inspect **register values, memory contents, and program flow**.
- The **step (`s`) and continue (`c`) commands** allow controlled execution for better debugging.


