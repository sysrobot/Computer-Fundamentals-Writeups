In this project, we willobserve the system calls made by the programs in `C` I have previously made.
We will use `strace`, which traces system calls and prints the output to the terminal.
The previous script as shown below, Is just a program that:

Uses three API functions;
`open`, `write`, and `close`, to create a new file, write some text to it, and finally close the file.

<img width="551" height="222" alt="image" src="https://github.com/user-attachments/assets/cd12ded9-ca45-4eee-9d4d-5cfc6b3380d6" />

I have also previously compiled this file using the `GNU C Compiler` (`GCC`), into an executable called `newfile`.

The `strace` tool launches a program (`newfile` in this case) and shows all the system calls that are made while that program runs. 

<img width="848" height="493" alt="image" src="https://github.com/user-attachments/assets/09ea69b5-87ac-4e1a-9898-64a2077139be" />

At the beginning of the output, you can see a number of system calls that represent the work required to load the executable file and required libraries.
This is work that happens before the code you wrote runs; 
you can skip past that text. Near the end of the output, you should see text similar to the following:

<img width="478" height="42" alt="image" src="https://github.com/user-attachments/assets/2428b4d8-4497-4e71-a9e7-057ad0d7993c" />

It’s almost the same three API functions that we used to create `file1.txt `and write text to it.
The `C` functions that I called from your program are just thin wrappers around the system calls of the same name, with the exception of open, which invokes the `openat` system call. 
The values after the equals signs are the return values from the three system calls. 
On my system, the `openat` function returned `3`, which is a number known as a file descriptor that refers to the `opened` file.

You can see the file descriptor value used as a parameter to the subsequent calls to `write` and `close`.
The `write` function returned `13`, the number of bytes written.
The close function returned `0`, an indicator of success.

I also previously made a python script that does exactly the same as the script above.
We should use `strace newfile.py` to examine the system calls used in this program:
Expect to see even more output here, since strace is actually monitoring the python interpreter, which in turn has to load `newfile.py` and run it.

If you look near the end of the output, you should see calls to;
`openat`, `write`, and `close`, just as you did in the `C` program.

<img width="612" height="97" alt="image" src="https://github.com/user-attachments/assets/02e3ed5e-17fa-4785-bf77-d1d7103cda67" />

This shows that: 
Despite the source code differences between `C` and `Python`, in the end, the same system calls are invoked to interact with files.

The `strace` tool can be used to quickly get an idea of how a program interacts with the operating system.
For example, the `ps` utility to get a list of processes. 
If you want to understand how `ps` works, you can run ps under strace, like so:

<img width="251" height="13" alt="image" src="https://github.com/user-attachments/assets/5a943073-87e4-4220-a4c1-6e0db2b037b1" />

I may examine the how the `ps` utility interacts with the operating system.
