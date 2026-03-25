## Overview
The first project of mine will be examining **FACTORIAL IN ARM ASSEMBLY** code.
I do not have an ARM processor, so I will emulate an ARM processor using **WSL Ubuntu**.  
We will examine this code and break it down at a deeper level.

---
## Setup
First, I was instructed to create a file and paste the ARM assembly code inside.
Using an emulated Ubuntu environment, I used `nano` with the filename:
```bash
nano fac.s
````
I then pasted the code and saved it as `fac.s` in the home directory.
![Setup](https://github.com/user-attachments/assets/3236a293-921c-4cc3-8e4d-a3b5bf8d124a)

---
## Program Structure

### Entry Point
As we can see, `_start` marks the point at which the program begins execution.
The `.global` directive makes `_start` visible to the linker so it can be used as the entry point:
![Global](https://github.com/user-attachments/assets/08e22885-0cf1-446d-bcd7-d5da11bfa8ba)

---
### Code Section
The `.text` directive tells the assembler that the lines following it are instructions:
![Text Section](https://github.com/user-attachments/assets/8df21e10-6571-4947-a51d-ff79cc7d217e)

---
### Data Section
At the end of the code, the `.data` directive tells the assembler that the following lines are data:
![Data Section](https://github.com/user-attachments/assets/31b0c828-6abe-4d0e-b524-10a9a1086b1a)
In the data section, the program stores two 32-bit values using `.word`:
```text
n = 5
result = 0
```
> In this context, a "word" = 4 bytes (32 bits)
These values are stored in memory before the program runs.

---
## Memory & Register Interaction
The first two instructions in `_start` load the value of `n` from memory:
* `ldr r1, =n` → loads the **address** of `n` into `r1`
* `ldr r0, [r1]` → loads the **value at that address** into `r0`

Brackets (`[r1]`) mean:
```text
"access the value stored at the address in r1"
```
---
## Storing the Result
The two instructions following `end` save the result into memory:
![Store Result](https://github.com/user-attachments/assets/cbb45542-e031-40f1-a820-5664a3477ae5)

* `ldr r1, =result` → load address of `result`
* `str r0, [r1]` → store value of `r0` into that address
```text
MEMORY[result] = r0
```

---
## Program Exit
The final instructions cleanly exit the program using the operating system:
![Exit](https://github.com/user-attachments/assets/78526b95-54d8-4690-8537-c9f8ac30c0ee)

---
## Execution Breakdown

### START
```asm
ldr  r1, =n
ldr  r0, [r1]
```
* Load memory address of `n` → `r1`
* Load value of `n` → `r0`

---

```asm
subs r3, r0, #1
ble  end
```

* `r3 = r0 - 1`
* If `r3 <= 0`, jump to `end`
> When using `ble`, CPU flags are used internally to determine the condition.

---
### LOOP
```asm
loop:
mul  r0, r3, r0
subs r3, r3, #1
bne  loop
```
* `r0 = r3 × r0`
* Decrement `r3`
* Repeat until `r3 == 0`

Example:
```text
r0 = 5, r3 = 4
→ 20, 3
→ 60, 2
→ 120, 1
→ 120, 0
```

---
### END
```asm
ldr  r1, =result
str  r0, [r1]
```
* Store final result (120) into memory

---
## Assembling & Linking

### Assemble
![Assemble](https://github.com/user-attachments/assets/9dc68e64-40f8-4678-9c58-6eebc6eac073)
Converts assembly into machine code (object file).

---
### Link
![Link](https://github.com/user-attachments/assets/91f69520-ad04-46a1-977f-edd626cf006d)
Creates an executable from the object file.

---
## Running the Program
![Run](https://github.com/user-attachments/assets/568d34fc-efe8-432a-b96e-30327a141591)
The program produces no visible output.
Reason:
```text
It stores the result in memory but does not print anything
```

---
## Debugging with GDB
To understand what the program is doing, we use a debugger.

### 1. Load the Program
![GDB Load](https://github.com/user-attachments/assets/102bbab4-d007-4762-a6c8-bf53bc2de7f3)

### 2. Find Entry Point
```bash
info files
```
![Entry Point](https://github.com/user-attachments/assets/77070459-15b3-4deb-a40d-df72ffbdf553)
This corresponds to `_start`.

### 3. Disassemble Instructions
```bash
disas 0x10074
```
![Disassemble](https://github.com/user-attachments/assets/23b5ad69-68b4-4995-94ec-06553c2dceec)

### 4. Calculate End Address
Each instruction = 4 bytes
```bash
print/x 0x10074 + 48
```
![Calc](https://github.com/user-attachments/assets/cc6ec1ba-4859-486e-bcd7-2566ce5971c9)

### 5. Full Disassembly
```bash
disas 0x10074 0x100a4
```
![Full Disassembly](https://github.com/user-attachments/assets/7c2691ce-5932-4f89-a3c2-89252702d5e1)

---
## Hacking our Program
What if we wanted to change the value of `n`? We could edit the hardcoded value in the code, or add user input. However, assuming we do NOT have access to the source code, we can still change how the program behaves by editing memory through a debugger.

### Objective
Change the in-memory value of `n` from `5` to `7`, so the program computes `7! = 5040`.

### Steps
1) **Restart the program**  
Type `run` and confirm with `y` to start from the first breakpoint.

2) **Get the memory address of `n`**  
Use the print command to find where `n` lives in memory.
```gdb
p &n
```

3) **Edit the value**  
Assign a new number to the address found in step 2.
```gdb
set {int}0xADDRESS_OF_N = 7
```

4) **Verify the change**  
Print `n` to ensure it updated correctly.
```gdb
p (int)n
```

5) **Disable middle breakpoints**  
Skip to the end of the loop by disabling intermediate breakpoints.
```gdb
disable 2
disable 3
```

6) **Continue execution**  
Run the program to completion.
```gdb
continue
```

7) **Check result**  
Print the final value stored in `result`.
```gdb
p (int)result
```

Expected output:
```text
5040
```
This allows us to alter program behavior dynamically without recompiling.

---
### Integer Overflow Limitation
The program uses `.word` for `n` and `result`, meaning they are stored as **4 bytes (32-bit integers)**. Factorials grow exponentially, so there is a limit to how large `n` can be before the result exceeds what fits in 32 bits.
- **12! = 479,001,600** → Fits in 32-bit
- **13! = 6,227,020,800** → Exceeds 32-bit max → Overflow

Setting `n` greater than `12` will result in incorrect values due to integer overflow.

---
## Conclusion
This project demonstrates:
- How assembly interacts with memory and registers
- How control flow works at a low level
- How high-level logic (factorial) is implemented in machine instructions
- The importance of debugging to truly understand execution
- The ability to modify program state at runtime using a debugger
- The ability to change memory values to manipulate programs without having direct access to source code.

---
# Side Note
Although I forgot to screenshot myself using a debugger to halt the execution of the program and using breakpoints.
However, I had completed the project for it.

---

# Side Note
Although I forgot to screenshot myself using a debugger to halt the execution of the program and using breakpoints, I had completed the project for it.
