### **Dynamic Memory Management in ExpL**  
ExpL provides built-in support for **dynamic memory management** through three functions:  
1. `Alloc()` – Allocates memory dynamically.  
2. `Free()` – Deallocates previously allocated memory.  
3. `Initialize()` – Initializes dynamic memory management.  

These functions are implemented as **library routines** in the ExpL library (`library.lib`) and provide a higher-level interface for managing memory in eXpOS.  

---

### **1. `Initialize()` – Setting Up Dynamic Memory Management**  
- Before using `Alloc()` and `Free()`, the memory management system must be initialized using `Initialize()`.  
- The `Initialize()` function **sets up internal data structures** required for dynamic memory allocation.  
- This function should be **called once** at the beginning of the program.  

#### **Usage in ExpL**  
```c
temp = Initialize();
```
- `temp` stores the return value, but `Initialize()` typically returns **0**.  
- If memory management is not initialized, calls to `Alloc()` or `Free()` **may not work correctly**.  

---

### **2. `Alloc()` – Allocating Memory Dynamically**  
- `Alloc()` allocates **one unit (16 words) of memory** from the heap.  
- Returns the **base address** of the allocated memory block.  
- If memory is **unavailable**, it returns **0**.  

#### **Usage in ExpL**  
```c
ptr = Alloc();
```
- Here, `ptr` will contain the **base address** of the newly allocated memory block.  
- If `ptr == 0`, it means that **no memory is available**.  

---

### **3. `Free()` – Deallocating Memory**  
- `Free(ptr)` deallocates a previously allocated memory block at address `ptr`.  
- The deallocated memory is returned to the **free memory pool** for future allocations.  
- If an invalid address is passed (i.e., not allocated via `Alloc()`), the behavior is **undefined**.  

#### **Usage in ExpL**  
```c
temp = Free(ptr);
```
- `temp` will store the return value:  
  - `1` if the deallocation is **successful**.  
  - `0` if the pointer is **invalid** or **not allocated**.  

---

### **4. Example: Using Dynamic Memory in ExpL**  
```c
int main()
{
decl
    int ptr1, ptr2;
enddecl
begin
    Initialize();  // Set up memory management

    ptr1 = Alloc();  // Allocate first memory block
    ptr2 = Alloc();  // Allocate second memory block

    if (ptr1 != 0) then
        temp = Free(ptr1);  // Free the first block
    endif;

    return 0;
end
}
```
- `Initialize()` is called first to **set up memory management**.  
- `Alloc()` requests memory dynamically.  
- `Free()` deallocates memory when it is no longer needed.  

---

### **5. Memory Management in eXpOS**  
- These functions internally use **system calls** to manage memory at the OS level.  
- The OS tracks **free and allocated memory blocks** using **a linked list in the heap**.  
- Proper memory allocation and deallocation **prevent memory leaks and fragmentation**.  

---

### **Key Takeaways**  
1. **`Initialize()` must be called first** before using dynamic memory functions.  
2. **`Alloc()` returns a memory block’s base address** or `0` if memory is unavailable.  
3. **`Free(ptr)` deallocates memory** and prevents fragmentation.  
4. **Always check return values** to handle memory allocation failures.  
5. Dynamic memory is managed internally by eXpOS, following **a linked list-based approach**.  

For more details, visit the **[official documentation](https://exposnitc.github.io/os_spec-files/dynamicmemoryroutines.html)**.
---
