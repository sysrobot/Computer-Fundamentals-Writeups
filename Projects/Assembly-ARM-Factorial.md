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
![Uploading image.png…]()

---

Explaining the code logic:
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
