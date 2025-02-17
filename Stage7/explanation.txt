_______________________________
Stage 7: Compliance with eXpOS ABI
_______________________________

- In this stage, we modify the user program (INIT) and the OS startup code to comply with the eXpOS ABI.
- The modifications involve adjusting the program format, loading a shared library, and setting up the page table entries accordingly.

_______________________________
Modifying INIT
_______________________________

- The INIT program must be converted to comply with the XEXE executable format.
- The first 8 words of the file must be a header, followed by the program instructions.
- The OS loads the INIT file starting from logical page 4.
- The first instruction starts at memory address 2056, with subsequent instructions placed accordingly.
- Jump addresses in INIT must be adjusted based on this alignment.

_______________________________
Modifications to OS Startup Code
_______________________________

- **Load Library Code from Disk to Memory:**
  - The library code is stored in disk blocks 13 and 14.
  - During OS startup, it is loaded into memory pages 63 and 64.
  - This library is attached to logical pages 0 and 1 of each application.
  - The library facilitates system call invocation and memory management functions.
  - Applications refer to library routines without including them directly in their executable files.
  
- **Steps to Ensure Library Code is Linked Correctly:**
  - Preload the library code into disk blocks 13 and 14 using:
    ```
    load --library ../expl/library.lib
    ```
  - During OS startup, load the library into memory pages 63 and 64.
  - When an application is loaded, map logical pages 0 and 1 to physical pages 63 and 64.
  - Allocate two physical pages for the heap and map them to logical pages 2 and 3.

_______________________________
Modifying Page Table Entries
_______________________________

- **Page Table Base:**
  - The SPL constant `PAGE_TABLE_BASE = 29696` stores the start of the page tables.
  - Each user process gets one page table with 20 entries.
  - The first user process (INIT) uses entries in this region for its page table.

- **Memory Mappings According to ABI:**
  - **Library (Logical pages 0, 1):** Read-only pages mapped to memory pages 63 and 64.
  - **Heap (Logical pages 2, 3):** Read-write pages mapped to memory pages 78 and 79.
  - **Code (Logical pages 4, 5):** Read-only pages mapped to memory pages 65 and 66.
  - **Stack (Logical pages 8, 9):** Read-write pages mapped to memory pages 76 and 77.

- **Setting Page Table Entries:**
  ```
  // Library
  [PTBR+0] = 63;
  [PTBR+1] = "0100";
  [PTBR+2] = 64;
  [PTBR+3] = "0100";
  
  // Heap
  [PTBR+4] = 78;
  [PTBR+5] = "0110";
  [PTBR+6] = 79;
  [PTBR+7] = "0110";
  
  // Code
  [PTBR+8] = 65;
  [PTBR+9] = "0100";
  [PTBR+10] = 66;
  [PTBR+11] = "0100";
  [PTBR+12] = -1;
  [PTBR+13] = "0000";
  [PTBR+14] = -1;
  [PTBR+15] = "0000";
  
  // Stack
  [PTBR+16] = 76;
  [PTBR+17] = "0110";
  [PTBR+18] = 77;
  [PTBR+19] = "0110";
  ```

- **Set Process Page Table Limit:**
  ```
  PTLR = 10;
  ```

- **Initialize Instruction Pointer (IP) from Header:**
  ```
  SP = 8 * 512;
  [76 * 512] = [65 * 512 + 1];
  ```
  - The IP is set to the second word in the executable header.
  - The stack pointer (SP) is initialized at the top of the stack.
  - When executing `IRET`, the machine retrieves this value and sets IP accordingly.

_______________________________
Making Things Work
_______________________________

- **Steps to Verify Modifications:**
  1. Compile and load the modified OS startup code.
  2. Load the modified INIT program.
  3. Run the machine in debug mode and verify correct execution.


