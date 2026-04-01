In this project we will write a program that creates a thread.
We’ll then observe the thread running.

1) We create a new file called `threader.c` in the root of oue home folder
   From there, we enter our code as below (just use `nano` as your text editor)
<img width="577" height="537" alt="image" src="https://github.com/user-attachments/assets/6ce52152-a339-4679-afbb-6c7d7f03306e" />
   We can now use GNU C Compiler (`gcc`) to compile our code into an executable file as shown below:
<img width="553" height="56" alt="image" src="https://github.com/user-attachments/assets/beefa9a1-85fc-40f9-a33f-200b483b629b" />

  
2) Before we run it, let me explain the code briefly:
   The program starts in the main function , which creates a thread  that runs the function `mythread`.
   This means there are two threads, the `main` thread and `mythread`.
   Both threads run in an infinite loop, where every so often they print the `PID` and `TID` of the current thread. 
   For variety, `mythread` prints about every 5 seconds, while `main` prints approximately every 10 seconds.
   The key idea is that threads share a process (`PID`) but have their own thread IDs (`TID`).

3) Now we can execute the executable file by using `./threader`, with the result shown below:
<img width="347" height="332" alt="image" src="https://github.com/user-attachments/assets/c0a171c6-9f3c-4e8a-853a-e06f1e6ac5ff" />
  As the program runs, expect the two threads to continue printing their `PID` and `TID` information.
  The `TID` and `PID` numbers won’t change for this instance of the program, since it’s the same process and threads running the entire time.
  You should see `mythread` print twice as often as `main` every 5 seconds versus every 10 seconds.

4) Let us leave that program running and look at our list of running processes and threads.
   To do this, we shall open another terminal tab and write the following command below and inspect the result:
<img width="503" height="98" alt="image" src="https://github.com/user-attachments/assets/0afe5466-b335-489b-bc6d-fee1bce15730" />
   Adding the `T` option to the `ps` command shows threads as well as processes.
   The `grep` utility filters our output to only see the threader process information.

5) Let us inspect this output:
   The first column is the `PID` and the second column is the `TID`.
   So you can see that the output from ps matches the output from your program.
   The two threads share a `PID` but have different `TID`s.
   Also, note that the `main` thread’s `TID` matches its `PID`.
   This is expected for the first thread in a process.

5) Let us halt the execution of this program by either using `CTRL+C` or kill the process via its `PID` using: `kill (PID of the process`).
**PS: MAKE SURE TO RUN THE `kill (PID)` COMMAND IN THE OTHER TAB!**
