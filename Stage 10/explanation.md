Here's the explanation for **Stage 10** in the same format as the previous stages:  

---

## **Stage 10: Implementing INT 7 (System Call Handling)**

### **Objective**  
In this stage, we implement a system call handler for **INT 7**, which processes an argument (a word) and prints it to the console. The system call must correctly handle valid and invalid arguments and return the appropriate values to the user program.

---

### **System Call Handling Logic**

1. **Check if Argument 1 (File Descriptor) is Valid**  
   - If the file descriptor is **not -2**, it is invalid.  
   - Store **-1** as the return value at `userSP - 1` (top of the user stack).  
   - The return value must be stored in memory using **address translation**:
     ```assembly
     alias physicalAddrRetVal R5;
     physicalAddrRetVal = ([PTBR + 2 * ((userSP - 1) / 512)] * 512) + ((userSP - 1) % 512);
     [physicalAddrRetVal] = -1;
     ```
   - Control exits after setting the return value.

2. **Handling a Valid Argument (Else Block)**  
   - **Step 1:** Extract the word (argument 2) from the user stack using **address translation**:
     ```assembly
     alias word R5;
     word = [[PTBR + 2 * ((userSP - 3) / 512)] * 512 + ((userSP - 3) % 512)];
     ```
   - **Step 2:** Print the extracted word to the terminal:
     ```assembly
     print word;
     ```
   - **Step 3:** Set the return value to **0** (indicating success):
     ```assembly
     alias physicalAddrRetVal R6;
     physicalAddrRetVal = ([PTBR + 2 * ((userSP - 1) / 512)] * 512) + ((userSP - 1) % 512);
     [physicalAddrRetVal] = 0;
     ```

3. **Final Steps Before Returning Control**
   - Restore **SP** to point back to the top of the user stack:
     ```assembly
     SP = userSP;
     ```
   - Reset the **MODE FLAG** field in the process table to **0**, switching the process back to user mode:
     ```assembly
     [PROCESS_TABLE + [SYSTEM_STATUS_TABLE + 1] * 16 + 9] = 0;
     ```
   - Use `ireturn` to pass control back to the user program.

---

### **Modifications to OS Startup Code**  

- Load **INT 7** handler from disk to memory during OS startup:  
  ```assembly
  loadi(16,29);
  loadi(17,30);
  ```

---

### **Making Things Work**  

1. **Write and Compile the INT 7 Handler**
   - Save the system call handling code as:
     ```shell
     $HOME/myexpos/spl/spl_progs/sample_int7.spl
     ```
   - Compile it using the **SPL compiler**.

2. **Load the Compiled Code into the XSM Disk**
   - Use the **XFS interface** to load the compiled **INT 7** handler into the system.

3. **Automating the Loading Process**
   - To simplify loading multiple files into the XFS interface, create a **batch file** containing all the necessary `load` commands.
   - Run this batch file using the **XFS-interface run command**.

4. **Run the Machine Without Timer Interrupts**
   - Disable the **timer** while testing to avoid interference.

---

### **Introduction to ExpL (Next Stage Preview)**  

- From the next stage onward, you will write **user programs** using **ExpL** (Experimental Language).  
- **ExpL Features:**
  - Allows writing **high-level programs** that can invoke system calls using the `exposcall()` function.
  - The **ExpL compiler** translates these calls into **XSM machine instructions** and invokes system calls automatically.
  - ExpL programs interact with the **eXpOS library**, which handles low-level system interactions.

---

## **Q&A**
### **Q1: Why calculate physical addresses for `userSP - 3` and `userSP - 1` separately?**  

- The page table translation mechanism depends on **which page a virtual address belongs to**.  
- If `userSP - 3` and `userSP - 1` belong to different pages, their **physical addresses could be in separate memory locations**.
- Directly adding/subtracting an offset might **cross a page boundary**, leading to incorrect memory accesses.
- To **ensure correctness**, each address is computed **independently** using **address translation**.

---

This completes **Stage 10**, setting up system call handling with **INT 7** and preparing for **ExpL programming** in the next stage. 🚀
