We will be examining an executable file, without the original assembly language source file.
In essence, we will be using different set of tools for examining machine code.


Below, we have our `fac` named executable file in which we used previously, when we assembled it and then made it executable using a linker.
<img width="350" height="52" alt="image" src="https://github.com/user-attachments/assets/f04c1021-7ed5-4ed3-8632-70892ecb150d" />

1)  We look at the bytes contained in the file as hexadecimal values by using the hexdump tool.
   <img width="552" height="180" alt="image" src="https://github.com/user-attachments/assets/438bc512-6704-45ce-a853-074f4361f2be" />
- This is displaying the bytes in our executable file.
- What you see is simply a sequential listing of the bytes in the file, each displayed as a two- character hexadecimal value. If the output of this command is too large to fit in your terminal window, scroll up to see the beginning bytes. The eight- character hex numbers along the left- hand column represent the offset into the file of the first byte in the corresponding row.
- There are 16 bytes on each row, meaning the offset number of each row (along the left) increases by 0x10.
- On the right hand side of the output are the same bytes interpreted as ASCII. Bytes that do not correspond 
to a printable ASCII character code are indicated with a period.
2) At offset `00000000`, , you should see a 7F, followed by 45 4c 46, or in ASCII, ELF.
  <img width="571" height="12" alt="image" src="https://github.com/user-attachments/assets/6b092e23-57eb-45fa-aeff-e5e64d55bbef" />
- This is an indicator that this is a file that is in executable and linkable format (ELF).
- ELF files are the standard Linux format for executable programs.
3) Moving past the headers to the `text` section, with the offset being `00000074` is the beginning of the text section, and it starts with bytes 28 10 9f e5.
  <img width="237" height="16" alt="image" src="https://github.com/user-attachments/assets/dde54195-1ef9-4023-8943-2c04393d6722" />
-  If you rearrange these bytes last to first, you get `e59f1028`, which is the machine code instruction for `ldr` `r1`, `[pc, #40]`.
-  Each set of 4 bytes in this section is a machine instruction.
4) As a side note, the order in which computers store bytes of data for larger numerical values is known as endianness.
   When a computer stores the least significant byte first (at the lowest address) this is called little- endian.
   Storing the most significant byte first is called big- endian.
---
If you want to view parts of this hexadecimal data, but grouped into sections, you can use the `objdump` tool:
<img width="433" height="202" alt="image" src="https://github.com/user-attachments/assets/b355ec40-171a-4957-82cc-af18317bfa1a" />
- Note how the numbers along the left-hand side have changed.
- Instead of starting at `0074`, the `.text` section (that is, code) starts at `10074`.
- Instead of starting at `00ac`, the `.data` section containing the values of n and result starts at `200ac`.
- The hexdump tool simply shows the byte offset within the file, whereas objdump output refers to the address where the bytes are loaded in memory when the program runs.
- Another way to view the addresses of the various sections in an ELF executable file is with `readelf -e fac`. This displays the headers in the file

Another feature of `objdump` is the ability to disassembled machine code, meaning you can see the assembly language instructions alongside the machine code byte values.
<img width="928" height="735" alt="image" src="https://github.com/user-attachments/assets/abe12f5c-fdb6-4b5b-b1cf-6f6ae26b41a5" />
*PS: (My ARM processor emulation strategy is not working well, and I do now own an ARM device, as most desktops use x86 CPUs, so bare with me!)*

Using the techniqueswe have described in the preceding pages, you can get a view of the contents of an ELF executable file!
s stated before, you can examine any ELF executable on a Linux system, not just code you wrote.
For example, say you want to see the machine code for `ls`: the tool you used earlier to list the contents of a 
directory. 
First, you need to find the filesystem location of the ls ELF file, like so:
1) First, we need to locate where the `ls` file's `ELF` file is located like below.
   <img width="321" height="31" alt="image" src="https://github.com/user-attachments/assets/8d99cf6b-1a71-4154-adf2-190f71c55b5b" />
2) Now we can use `objdump` to examine the machine code of this executable file, or any of the other methods aboved
   The output of this command is VERY long, so I redirected to a file named `ls.txt`.
   <img width="397" height="12" alt="image" src="https://github.com/user-attachments/assets/94d401be-cfab-4ee1-91e6-75ba52a2a56a" />
   If we try viewing the contents of the redirected file `ls.txt` using the `cat` command, it is way too long but we can see it all.

There is no need do to do this, as Linux is open-source.
Just use an online documentation to view it.
