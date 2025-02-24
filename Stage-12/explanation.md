---

### **Introduction to Multi-programming**
Stage 12 introduces multi-programming, allowing the operating system to execute two processes concurrently. This is achieved by modifying the **timer interrupt handler** to switch between two processes during execution.

### **Processes Involved**
1. **INIT Process (PID = 1)**  
   - This is the same INIT process used in Stage 11.  
   - It executes a program that prints all numbers below 50.
  
2. **IDLE Process (PID = 0)**  
   - A new process introduced in this stage.  
   - The idle process runs an infinite loop, consuming CPU cycles when no other process is ready.  
   - It must be preloaded into memory during system startup.

---

### **Loading the Idle Process**
The **Idle Process** must be loaded into memory before OS bootstrap. The following steps are required:

1. **Store the idle process binary in disk blocks 11 and 12.**  
   - The ExpL program for the idle process is a simple infinite loop:
     ```c
     int main()
     {
        decl
           int a;
        enddecl
        begin
           while(1 == 1) do
              a = 1;
           endwhile;
           return 0;
        end
     }
     ```
   - This program should be compiled and loaded into disk blocks using:
     ```
     load --idle <path_to_idle_program>
     ```

2. **Load the program into memory pages 69 and 70** at system startup:
   ```assembly
   loadi(69,11);
   loadi(70,12);
   ```
  
3. **Set up the page table for the idle process.**  
   - Unlike other processes, the idle process **does not require** heap or library pages.
   - It only needs **code and stack pages**:
     ```
     PTBR = PAGE_TABLE_BASE;  // Since PID of idle process is 0
     ```
   - Page table setup:
     ```assembly
     // Library
     [PTBR+0] = -1;
     [PTBR+1] = "0000";
     [PTBR+2] = -1;
     [PTBR+3] = "0000";

     // Heap
     [PTBR+4] = -1;
     [PTBR+5] = "0000";
     [PTBR+6] = -1;
     [PTBR+7] = "0000";

     // Code
     [PTBR+8]  = 69;
     [PTBR+9]  = "0100";
     [PTBR+10] = 70;
     [PTBR+11] = "0100";
     [PTBR+12] = -1;
     [PTBR+13] = "0000";
     [PTBR+14] = -1;
     [PTBR+15] = "0000";

     // Stack
     [PTBR+16] = 81;  // Allocate page 81 for stack
     [PTBR+17] = "0110";
     [PTBR+18] = -1;
     [PTBR+19] = "0000";
     ```

---

### **Setting Up the Process Table**
Each process in EXPOS has a **Process Table entry**. The OS maintains process metadata such as PID, state, stack pointers, etc.

1. **Process Table for Idle (PID = 0)**
   - **Stored at `PROCESS_TABLE`**  
   - State is set to **CREATED** (since it has never been scheduled before).  
   - User Area Page allocated: **Page 82**  
   - User stack pointer (UPTR): `8*512`
   - Kernel stack pointer (KPTR): `0`

2. **Process Table for INIT (PID = 1)**
   - **Stored at `PROCESS_TABLE + 16`**
   - State is set to **RUNNING** (since INIT is scheduled first).  
   - Set page table and memory regions as per previous stages.

---

### **OS Startup Modifications**
1. **Initialize registers for INIT process.**
   - Load page table entries for INIT.
   - Set machine registers (PTBR, PTLR) for INIT.
   - Set **current PID in System Status Table** to `1` (INIT).
   - **Transfer control to INIT using `ireturn`.**

---

### **Modifications to the Timer Interrupt Handler**
The **timer interrupt** is responsible for process switching.

#### **Steps in Timer Interrupt Handling**
1. **Save Context of Running Process**
   - Switch from user stack to **kernel stack**.
   - Save CPU registers and stack pointer.

2. **Fetch the PID of the currently running process**
   ```assembly
   alias currentPID R0;
   currentPID = [SYSTEM_STATUS_TABLE+1];
   ```
   
3. **Save process metadata into its Process Table Entry**
   ```assembly
   alias process_table_entry R1;
   process_table_entry = PROCESS_TABLE + currentPID * 16;

   [process_table_entry + 12] = SP % 512;
   [process_table_entry + 14] = PTBR;
   [process_table_entry + 15] = PTLR;
   [process_table_entry + 4]  = READY;
   ```

4. **Select Next Process**
   - We only have two processes (`INIT` and `IDLE`), so we **toggle between them**.
   ```assembly
   alias newPID R2;
   if(currentPID == 0) then
       newPID = 1;
   else
       newPID = 0;
   endif;
   ```

5. **Restore Next Process Context**
   ```assembly
   alias new_process_table R3;
   new_process_table = PROCESS_TABLE + newPID * 16;

   SP = [new_process_table + 11] * 512 + [new_process_table + 12];
   PTBR = [new_process_table + 14];
   PTLR = [new_process_table + 15];

   [SYSTEM_STATUS_TABLE + 1] = newPID;
   ```

6. **Handle Newly Created Process**
   - If the process was **never executed before (CREATED state)**,  
     set stack pointer and **return to user mode**.
   ```assembly
   if([new_process_table + 4] == CREATED) then
       [new_process_table + 4] = RUNNING;
       SP = [new_process_table + 13];
       ireturn;
   endif;
   ```

7. **Resume Execution**
   - If the process was previously running, restore the register context and return to user mode.

---

### **Key Takeaways**
1. **Multi-programming:**  
   - Introduces **process switching** using the **timer interrupt**.  
   - Supports two processes (**INIT** and **IDLE**) concurrently.

2. **Idle Process Implementation:**  
   - Runs an **infinite loop** when no process is ready.  
   - **Preloaded** during OS startup.

3. **Process Table Management:**  
   - Stores **metadata** (PID, state, memory regions, stack pointers).  
   - INIT starts as **RUNNING**, and IDLE as **CREATED**.

4. **Timer Interrupt-Based Scheduling:**  
   - **Preemptive scheduling** using a basic toggle mechanism.
   - Saves **registers, stack pointer, PTBR, PTLR** of the current process before switching.

5. **Handling New and Resumed Processes:**  
   - **CREATED state** → Initialize stack and start fresh execution.  
   - **READY state** → Restore previous execution context.

---
