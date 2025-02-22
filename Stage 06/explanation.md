Stage 6: Writing and Executing the First User Program in Unprivileged Mode
=========================================================================

In this stage, we transition from executing system programs in kernel mode to executing a user program in user mode. The key objective is to write and load the first user program, known as the INIT program, and execute it as a process in user mode.

Key Tasks:
----------

- **User Program (INIT Program):**
    - We write a simple assembly program that calculates the squares of the first 5 natural numbers. This program runs in user mode, which uses logical addressing. The INIT program is stored in blocks 7 and 8 on the XSM disk, and we load it into memory as part of the OS startup.
    - The INIT program logic:
        - R0 stores the value of `n` (the number to square).
        - R1 stores `n^2`.
        - The program loops from `n = 1` to `5`, calculating and storing the square of each number in R1.
        - After completing the loop, the program exits by invoking the `INT 10` system call.

- **OS Startup Code:**
    - The OS startup code loads the INIT program into memory from disk blocks 7 and 8, placing it in **logical pages 4 and 5** of memory (mapped to physical pages 65 and 66).
    - A **page table** is created to ensure the translation from logical addresses to physical addresses for the INIT program.
    - The stack for the user program is allocated in **logical page 8** (mapped to physical page 76). The stack pointer is set accordingly to ensure the correct start of the user process.
    - The OS startup code also includes the setup of essential OS data structures for process management (though only the page table is involved at this stage).

- **Page Table Setup:**
    - The page table maps logical pages to physical pages. The code pages (logical pages 4 and 5) are mapped to physical pages 65 and 66, while the stack page (logical page 8) is mapped to physical page 76.
    - The **auxiliary information** for the code pages is set to `0100` (read-only), and the stack page is set to `0110` (read/write).
    
    Page Table Entries: since each logical page is 2 parts (physical address and permissions), logical page 4= [PTBR+8], and so on
    - `[PTBR+8] = 65` (code page 4 → physical page 65)
    - `[PTBR+9] = "0100"` (read-only access for code)
    - `[PTBR+10] = 66` (code page 5 → physical page 66)
    - `[PTBR+11] = "0100"` (read-only access for code)
    - `[PTBR+16] = 76` (stack page 8 → physical page 76)
    - `[PTBR+17] = "0110"` (read/write access for stack)

- **Stack and Instruction Pointer (IP) Setup:**
    - The stack for the user program starts at physical page 76, with the corresponding physical address `76 * 512`. This address is loaded into the stack pointer (SP), and the IP is set to `0` to start execution of the INIT program from the beginning.
    - The **IRET instruction** is used to transfer control to the user program. The value at the top of the stack (which is `0`) is popped to set the IP to the correct address.
    
    Code for stack and IP setup:
    ```spl
    [76*512] = 0;    // Initialize the top of stack with IP value 0
    SP = 2*512;      // Set SP to the top of stack
    ireturn;         // Transfer control to the INIT program using IRET
    ```

- **User Program Execution:**
    - After loading the INIT program and setting up the stack and page table, the OS startup code transfers control to the INIT program using `IRET`. The INIT program runs in user mode, executing the square calculation logic and exiting via the `INT 10` system call.

- **Handling Interrupts and Exceptions:**
    - The exception handler routine is loaded to handle any unexpected events, like illegal instructions or invalid memory accesses. By default, the handler halts the machine on exceptions.
    - The `haltprog.spl` file is used to implement the basic exception handler and the `INT 10` interrupt handler.

Conclusion:
-----------

This stage introduces the concept of user programs and user mode execution. We write and execute the INIT program in user mode, handle basic interrupt mechanisms, and use the OS startup code to load and initialize the program. The page table setup and memory management are critical for ensuring that the logical addresses used by the user program are correctly mapped to physical memory.

