---

# Stack vs Heap Memory in C (Using gdb)

In this project, we will look at whether variables are allocated in stack or heap memory in a running program.

We first begin by debugging the `vars` executable program which I compiled using `gcc` in my last project.

---

## Part 1: Inspecting Stack Memory

### Step 1: Debug the Executable

Debug the `vars` executable file using `gdb`.

<img width="180" height="26" alt="image" src="https://github.com/user-attachments/assets/e3d01336-ef8e-411b-8e51-56855bd07bbe" />

We use the command `gdb vars` in which we can enter prompts now to debug the executable.

---

### Step 2: Run the Program

From the gdb prompt, type the following to run the program, which continues until the `SIGINT` statement is executed.

<img width="660" height="171" alt="image" src="https://github.com/user-attachments/assets/255ff3af-302e-46e9-b959-324f2b2717b5" />

Look at the memory addresses of the `points` and `year` variables.
In my case: `0xffffe030` & `0xffffe034`

---

### Step 3: View Memory Mappings

Now we use the following command to see all the mapped memory locations for your running program:

<img width="661" height="647" alt="image" src="https://github.com/user-attachments/assets/1d60b027-0295-4155-91c9-85a98eef28cd" />

The output lists the start and end address of the various memory ranges in use by this program.

---

### Step 4: Identify Stack Memory

Now we can look for the one that includes the addresses of our variables.

<img width="577" height="11" alt="image" src="https://github.com/user-attachments/assets/dbe72053-ea6c-4ca8-88d5-9012ff88cb31" />

As you can see, gdb indicates that this memory range is allocated for the stack, which is exactly where we would expect local variables to be.

---

## Part 2: Inspecting Heap Memory

Let’s now look at memory allocated on the heap.

However, we need to modify `vars.c` and rebuild it so that the program allocates some heap memory.

---

### Step 5: Modify the Code

We update our code as below, by adding: `#include <stdlib.h>` at the top & some code above our program is forced to stop.

<img width="643" height="291" alt="image" src="https://github.com/user-attachments/assets/c2df1581-7dff-4791-871b-bd55a7e60b8a" />

We call the memory allocation function malloc to allocate 512 bytes of memory from the heap.
The malloc function returns the address of the newly allocated memory.
That address is stored in a new local variable called data.

The program then prints two memory addresses:
The address of the new heap allocation and the address of the data variable itself, which should be on the stack.

---

### Step 6: Recompile and Run

Lets recompile our code again as below (using `GNU C Compiler (gcc)`).

<img width="347" height="13" alt="image" src="https://github.com/user-attachments/assets/a6b979b6-d7af-4318-90ce-5b7fbd18f57e" />

Then run it again as below using `gdb vars` then `run`.

<img width="655" height="509" alt="image" src="https://github.com/user-attachments/assets/f333e577-48cd-4ea2-ba5d-0cf501ee4d08" />

---

### Step 7: Compare Addresses

We expect that the first address, the address that came back from malloc, to be on the heap.
The second value, the address of the data local variable, should be on the stack.

<img width="375" height="15" alt="image" src="https://github.com/user-attachments/assets/71c0bed3-f6d4-47ef-bcd1-ec074a4e7a0b" />

---

### Step 8: Verify Using Memory Mappings

Let us additionally try see this program’s memory ranges and see where these two addresses fall.

<img width="657" height="628" alt="image" src="https://github.com/user-attachments/assets/2ad06dc1-5413-45a3-934f-7a757477513c" />

---

### Step 9: Identify Heap and Stack Regions

We can see our heap below:

<img width="615" height="31" alt="image" src="https://github.com/user-attachments/assets/aa5f51a1-2359-4f9b-b17d-f20b1bc931ee" />

We can see our stack below:

<img width="580" height="12" alt="image" src="https://github.com/user-attachments/assets/45304ffe-30b2-4e7b-b609-7344f9a785bb" />

---

## Summary
* Local variables (`points`, `year`, `data`) are stored on the stack
* Dynamically allocated memory (via `malloc`) is stored on the heap
* `gdb` allows us to inspect memory addresses and confirm where data resides
* The `info proc mappings` command helps map addresses to memory regions

---
