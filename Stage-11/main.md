---
### **Introduction to ExpL and High-Level Application Development**

In this stage, you transition from writing application programs in assembly language to writing them in **ExpL (Experimental Language)**, a high-level language designed specifically for eXpOS. 

#### **1. ExpL and Its Compiler**
- ExpL allows you to write high-level application programs.
- The **ExpL compiler** translates these high-level programs into **assembly code compatible with eXpOS**.
- ExpL supports system calls through a special function called `exposcall()`, which acts as an interface between the application program and the OS.
- Dynamic memory management functions (`Alloc`, `Free`, and `Initialize`) are built into the ExpL language and are implemented as **library routines**.

#### **2. The ExpL Library and Its Preloading in XSM**
- The ExpL standard library (`library.lib`) provides the implementation of system calls and memory management functions.
- This library is written in **assembly language** and **occupies two pages of memory**.
- Before bootstrapping the OS, this library must be preloaded into **XSM disk blocks 13 and 14** using the **XFS interface**.
- When the OS starts up, it loads this library code into **memory pages 63 and 64**.

#### **3. Library Linkage During Program Execution**
- ExpL programs **do not include the library code** in their compiled output. Instead, they contain calls to library functions.
- The **ExpL compiler** assumes that the library is loaded at **logical address 0**.
- When loading an ExpL program for execution, the OS must **map logical pages 0 and 1 to physical pages 63 and 64**, ensuring correct linkage.

#### **4. Writing and Compiling an ExpL Program**
- Now, instead of writing assembly programs, you will write programs in ExpL and compile them into assembly.
- Example ExpL program to print numbers from 1 to 50:
  ```c
  int main()
  {
  decl
      int temp,num;
  enddecl
  begin
      num=1;
      while ( num <= 50 ) do
           temp = exposcall ( "Write" , -2, num );
           num = num + 1;
      endwhile;
      return 0;
  end
  }
  ```
- Save this as `numbers.expl` in `$HOME/myexpos/eXpl/samples`.
- Compile it using:
  ```sh
  cd $HOME/myexpos/eXpl
  ./expl samples/numbers.expl
  ```
- The compiler generates `numbers.xsm` and also writes a copy to `assemblycode.xsm`.

#### **5. Running the Compiled ExpL Program**
- The compiled `.xsm` file should be loaded as the **init program** into the **XSM disk** using the **XFS interface**.
- Run the **XSM machine** to execute the program.

#### **6. Understanding Limitations: Why Console Read Will Fail**
- If an ExpL program contains a `read()` function, it **will not work yet**.
- The ExpL compiler translates `read()` into a **library function call** that ultimately triggers an **INT 6 (console input interrupt)**.
- Since **no handler for INT 6 is implemented**, the OS will **crash** when this interrupt is invoked.
- Debugging this behavior can be an insightful exercise to understand the **flow of execution** from the ExpL program to the OS.

---
### **Key Takeaways**
1. **ExpL is a high-level language** designed for eXpOS applications.
2. The **ExpL compiler generates assembly code**, assuming the presence of a **preloaded ExpL library**.
3. The OS must **correctly link the ExpL library** during program execution by mapping **logical pages 0 and 1** to **physical pages 63 and 64**.
4. The **ExpL compiler translates system calls into exposcall()**, ensuring that all OS interactions happen via the predefined library interface.
5. **Programs using console input will crash** because the OS **does not yet handle INT 6 (console read)**.
---
