The first project of mine will be examining  FACTORIAL IN ASSEMBLY ARM code, i do not have an arm processor, so i will emulate an ARM processor using WSL ubuntu, we will examine this code and look at it in a deeper level.

First, I am instructured to create a file and oaste the asm arm code inside, using a an emulated ubuntu, i have used nano alongside the filename fac.s, and pasted the code in and saved it in the text editor as fac.s in the root of your home folder. 
<img width="1480" height="767" alt="image" src="https://github.com/user-attachments/assets/3236a293-921c-4cc3-8e4d-a3b5bf8d124a" />

As we can see,  _start label marks the point at which the program begins execution. The .global directive makes the _start label visible to the linker so that it can be set as the entry point for the program: <img width="212" height="30" alt="image" src="https://github.com/user-attachments/assets/08e22885-0cf1-446d-bcd7-d5da11bfa8ba" />

The .text directive tells the assembler that the lines following it are instructions: <img width="78" height="23" alt="image" src="https://github.com/user-attachments/assets/8df21e10-6571-4947-a51d-ff79cc7d217e" />
And at the end of the code, the .data directive tells the assembler that the lines following it are 
data: <img width="242" height="80" alt="image" src="https://github.com/user-attachments/assets/31b0c828-6abe-4d0e-b524-10a9a1086b1a" />
In the data section, the program is storing two 32-bit values, each indicated by the .word directive, and the two values stored;
`n = 5` & `result = 0`
PS:  In this context, “word” means 4 bytes, or 32 bits, additionally in the `.data` section, all these values are stored in memory before the program runs.


The first two instructions in _start load the value of n from a location in memory . The ldr 
instruction loads a register with a value. We reference the address of n with =n. On the next line, 
[r1] is in brackets because the program is accessing the value stored at the address in r1.
The two instructions following end save the result to a location in memory:
<img width="521" height="117" alt="image" src="https://github.com/user-attachments/assets/cbb45542-e031-40f1-a820-5664a3477ae5" />
In essence, we need to store the memory address, and take the value located in the memory address and assign it to register 0, where r1 stores the memory address for n.


The two instructions following end save the result to a location in memory . The first instruc
tion moves the address of the memory location named result into the r1 register. After that, the 
code stores the value in the r0 register (which happens to be the calculated factorial) to the result 
memory address, referenced by r1: 

<img width="157" height="72" alt="image" src="https://github.com/user-attachments/assets/6df3e2e2-748f-42ec-a164-8eb372daa8c6" />


The last three instructions in the .text section are used to cleanly exit the program . This 
requires the help of the operating system
<img width="663" height="97" alt="image" src="https://github.com/user-attachments/assets/78526b95-54d8-4690-8537-c9f8ac30c0ee" />

START
`ldr  r1, =n` - We firts load the memory address of `n` into register 1.
from there, `ldr  r0, [r1]` we take the memory from the memory address of register one and set `r0` to the value of `n`
`subs r3, r0, #1` - is where we get into the factorial logic, from here we use register 3 and store `r0 - 1` using `subs` into register 3.
`ble  end` - & if r3 <= 0 , we will jump to the end and skip the loop otherwise continue. 
FUN FACT: When we use ble, we have certain flags which are set for the cpu, so we dont actually store those previous values, certain flags are set for the CPU to understand whether or not r3 <= 0!

LOOP
As 5 > 0, `mul  r0, r3, r0` we set r0 to be = `r3 x r0` (assuming the value of r3 is 4 and r0 is 5 which means r0 is now 20).
`subs r3, r3, #1 ` - we not decrement the r3 counter by one (`r3--`), 
`bne  loop` - if `r3` is > 0, we jump straight back inton the loop, assuming that: `r0 = 20` & `r3 = 3`, and repeat the procedure above until `r3` (our counter) is = 0.

END
`ldr  r1, =result` - We set r1 register to be equal to the memory address of `result` which we touched in `.data`.
`str  r0, [r1]` - In which we access the value of the memory address (`r1` stores the memory address of result!), store the value of `r0` (which would be 120 if you done the maths) & store it into the memory address value. (`MEMORY[r1] = r0`)

---
NEXT UP:

We need to assemble this script so the CPU can understand it which will turn it into an object file, from there we will use a linker to make the file executable.

<img width="662" height="67" alt="image" src="https://github.com/user-attachments/assets/9dc68e64-40f8-4678-9c58-6eebc6eac073" />
Here, we have converted our assembly code into machine code, however we need to use a linker to make this code executable.

To make this object file executable, I will use a linker.
The command below demonstrates this: 

<img width="376" height="51" alt="image" src="https://github.com/user-attachments/assets/91f69520-ad04-46a1-977f-edd626cf006d" />


However, running this script as demonstrated below, doess not ACTUALLY output anything.
<img width="668" height="403" alt="image" src="https://github.com/user-attachments/assets/568d34fc-efe8-432a-b96e-30327a141591" />
Reason being, this code does not output anything, rather just saves the result in memory, then exits. 
To interact with the user, the program would need to request some help from the operating system, as we are keeping this program as minimal as possible - we will NOT go through this at this moment in time.

Rather than adding and adjusting the code to add input or output to the sscreen, **How can you tell what it’s doing?**
Answer: We can use a debugger, a program that can examine a process as it runs.

1) We first use `gbd` to load the fac file, but no instructions execute yet.
 <img width="518" height="157" alt="image" src="https://github.com/user-attachments/assets/102bbab4-d007-4762-a6c8-bf53bc2de7f3" />

2) From there, in the prompt we type `info files` to to view the start address of the program.
 <img width="561" height="191" alt="image" src="https://github.com/user-attachments/assets/77070459-15b3-4deb-a40d-df72ffbdf553" />
 This entry point address corresponds to the _start label, since that’s where the program begins.

3) Now lets disassemble this machine code using `gdb`, starting at `Entry point: 0x10074`.
  <img width="683" height="132" alt="image" src="https://github.com/user-attachments/assets/23b5ad69-68b4-4995-94ec-06553c2dceec" />
As you can see, the first 4 instructions have been disassembled and we can see them clearly.
Additionally, if we want to view all of the instructions, we nee to know the ending address, which can easily be calculated by counting the amount of instructions you have multiplied by 4 (1 instruction = 4 bytes).
From there, we just add the amount of bytes (48) in our case to the entry point.

4) Now lets use `gdb` to do this without the worry of human error.
 <img width="637" height="442" alt="image" src="https://github.com/user-attachments/assets/cc6ec1ba-4859-486e-bcd7-2566ce5971c9" />
May look quite intimidating, but it really is not.
`print/x` simply prints the output in the hexadeximal format.
And the output (specically `$1`) is just a convenience variable stored in `gdb`.
The value after the equals sign is the printed value, the result of the calculation, `0x100a4` in this case.

5) Now that we know the ending address (`0x100a4`), now we can ask `gdb` to disassemble our WHOLE program, not just the 4 instructions.
   <img width="686" height="327" alt="image" src="https://github.com/user-attachments/assets/7c2691ce-5932-4f89-a3c2-89252702d5e1" />
As you can see, every instruction has been assigned an address, unlike in our assembled code in which we used labels.
Furthermore, the references to n and result have been replaced with memory offsets relative to the program counter register.
