# Stage 9: Kernel Stack Switching

## **Objective**
In this stage, we implement **kernel stack switching** during interrupts to prevent security vulnerabilities arising from user-mode stack manipulation. Unlike in Stage 8, where the OS entered kernel mode but continued using the same stack, Stage 9 ensures that upon an interrupt, the OS switches to a **dedicated kernel stack** allocated within the **User Area Page**.

## **Concepts Introduced**
- **User Stack and Kernel Stack**: The OS now maintains separate stacks for user and kernel modes.
- **User Area Page**: A dedicated memory page allocated for each process to store its kernel stack and other metadata.
- **Process Table Enhancements**: Stores both user and kernel stack pointers.
- **System Status Table Updates**: Tracks the currently executing process.
- **SP Switching**: On an interrupt, the stack pointer (SP) is switched to the kernel stack before executing kernel code.

## **Process Table and System Status Table Modifications**
The **Process Table** stores the following additional information:
1. **User Stack Pointer (SP)** – Stored at word 13.
2. **User Area Page Number** – Stored at word 11.
3. **Process ID (PID)** – Stored at word 1.

The **System Status Table (SST)** helps in tracking the currently executing process.

### **Memory Layout**
- **Process Table Base Address**: 28672 (Page 56)
- **System Status Table Base Address**: 29560
- **User Area Page Allocation**: Physical page **80**

## **Modifications to OS Startup Code**
1. **Allocate User Area Page**: Assign page **80** to the user process.
   ```assembly
   [PROCESS_TABLE + 11] = 80;
   ```
2. **Store PID (0) in Process Table**: Since there is only one process, assign PID **0**.
   ```assembly
   [PROCESS_TABLE + 1] = 0;
   ```
3. **Set Current PID in System Status Table**: Track the active process.
   ```assembly
   [SYSTEM_STATUS_TABLE + 1] = 0;
   ```

## **Timer Interrupt Handling**
### **Steps Performed by Timer Interrupt Handler**
1. **Save the Current User Stack Pointer**
   ```assembly
   [PROCESS_TABLE + ( [SYSTEM_STATUS_TABLE + 1] * 16) + 13] = SP;
   ```
2. **Switch to the Kernel Stack**
   - Compute the new SP from the **User Area Page Number**.
   ```assembly
   SP = [PROCESS_TABLE + ([SYSTEM_STATUS_TABLE + 1] * 16) + 11] * 512 - 1;
   ```
3. **Backup User Context**
   ```assembly
   backup;
   ```
4. **Print Debug Message**
   ```assembly
   print "TIMER";
   ```
5. **Restore User Context and Switch Back to User Mode**
   - Restore registers from the kernel stack.
   ```assembly
   restore;
   ```
   - Reset SP to the saved user stack pointer.
   ```assembly
   SP = [PROCESS_TABLE + ( [SYSTEM_STATUS_TABLE + 1] * 16) + 13];
   ```
   - Use `ireturn` to return to user mode.
   ```assembly
   ireturn;
   ```

## **Summary**
In Stage 9, the OS now switches to a **dedicated kernel stack** during interrupts, preventing user processes from interfering with kernel operations. The **Process Table** and **System Status Table** store additional information required for stack switching. The **Timer Interrupt Handler** correctly saves and restores user context while operating in the kernel stack. These modifications improve **process isolation and system security**, making the OS more robust against stack-based attacks.


