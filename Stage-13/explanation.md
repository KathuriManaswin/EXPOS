### Stage 13: Modules in eXpOS  

Modules in eXpOS are used to perform frequently required logical tasks such as scheduling processes and managing resources. These modules operate in **kernel mode** and can only be invoked from the kernel. User programs cannot directly invoke a module. Modules can be invoked by **interrupt routines, other modules, or the OS startup code**.  

Since modules execute in kernel mode, the **kernel stack** of the currently scheduled process acts as the caller-stack. eXpOS supports **eight modules (MOD_0 to MOD_7)**, which can be invoked using the `CALL MOD_n` or `CALL <MODULE_NAME>` instruction. The `CALL` instruction pushes the **next instruction's IP address** onto the kernel stack before executing the module. The module returns to the caller using the `RET` instruction, which restores the IP from the stack. Unlike `IRET`, `RET` does not switch to user mode but stays in kernel mode.  

Some modules have multiple functions, each identified by a **function number**. This function number is passed via register `R1`, while other arguments are passed through `R2`, `R3`, etc. The return value is stored in `R0`.  

### Boot Module (MOD_7)  

Due to memory constraints, the OS startup code is limited to **one memory page (page 1)**. To handle OS initialization beyond this limit, a **Boot Module (MOD_7)** is introduced. It is loaded by the OS startup code, which:  

1. **Creates the Idle Process** manually.  
2. **Loads the Boot Module** into memory pages **54 and 55** from disk blocks **67 and 68**.  
3. **Sets up the stack** for calling the Boot Module.  
4. **Calls the Boot Module** using `call BOOT_MODULE`.  

After returning from the Boot Module, the OS startup code:  

- Sets up the page table for the **Idle Process**.  
- Initializes process table entries for the Idle Process.  
- Marks the Idle Process as **RUNNING** (PID = 0).  
- Transfers the **entry point address** of the Idle Process to its stack.  
- Sets `SP` to the logical address of the user stack (`8 * 512`).  
- **Switches to user mode** using `IRET`.  

### Responsibilities of the Boot Module  

The Boot Module is responsible for initializing essential components before the system starts executing user processes:  

- **Loads all required system components** such as:  
  - Interrupt routines  
  - Exception handler  
  - eXpOS library  
  - INIT process  
- **Sets up the INIT process** (page table, process table entry).  
- Marks the **INIT process as CREATED** (not scheduled immediately).  
- Transfers the **entry point of INIT** to its stack.  
- Returns control to the OS startup code.  

### Execution  

After modifying the OS startup code and implementing the Boot Module, the system must be:  

1. **Compiled and loaded** onto disk using the XFS interface.  
2. **Run on XSM** with the timer enabled.
