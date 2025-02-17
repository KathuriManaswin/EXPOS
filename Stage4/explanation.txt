______________________________
Stage 4 - Writing OS Startup Code in SPL
______________________________

- In this stage, we introduce **SPL (Systems Programming Language)**, which allows writing high-level programs for the XSM machine instead of using only assembly language.
- SPL supports high-level constructs like `if-then-else`, `while-do`, and arithmetic operations, making kernel development easier.
- SPL programs must be **compiled to XSM assembly code** using the SPL compiler before execution on the XSM simulator.

______________________________
Steps Followed:
______________________________

- **Created an SPL program** to print odd numbers from 1 to 20:
  
  ```
  alias counter R0;
  counter = 0;
  while(counter <= 20) do
    if(counter % 2 != 0) then
      print counter;
    endif;
    counter = counter + 1;
  endwhile;
  ```
  
  - The register `R0` is **aliased** as `counter` to store the loop variable.
  - The loop iterates from `0 to 20`, checking if a number is **odd** before printing it.

- **Saved the program** as:
  ```
  $HOME/myexpos/spl/spl_progs/oddnos.spl
  ```

- **Compiled the SPL program** using:
  ```
  cd $HOME/myexpos/spl
  ./spl $HOME/myexpos/spl/spl_progs/oddnos.spl
  ```
  - This generates the file `oddnos.xsm`, containing the equivalent XSM assembly code.

- **Loaded the compiled code as OS Startup Code** to disk using the XFS interface:
  ```
  cd $HOME/myexpos/xfs-interface
  ./xfs-interface
  # load --os $HOME/myexpos/spl/spl_progs/oddnos.xsm
  # exit
  ```

- **Ran the machine using**:
  ```
  cd $HOME/myexpos/xsm
  ./xsm
  ```
  - The output displays **odd numbers from 1 to 19**, then halts.

______________________________
Expected Output:
______________________________

```
1
3
5
7
9
11
13
15
17
19
Machine is halting.
```

______________________________
Observations:
______________________________

- SPL programs **eliminate the need for writing all code in assembly**, making it easier to develop the eXpOS kernel.
- Compilation generates XSM assembly, which can be analyzed before execution.
- The OS Startup Code can now be **written in SPL** and compiled before loading into disk.


