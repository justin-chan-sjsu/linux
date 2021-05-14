# Assignment 4

1. For each member in your team, provide 1 paragraph detailing what parts of the lab that member implemented / researched. 
   
   I worked alone on this project.
   
2. Include a sample of your print of exit count output from dmesg from “with ept” and “without ept”. 

   This sample is not from dmesg. Instead, it comes from output from a test C program.
   
   Here are the exit count results with ept enabled:
   
   ![alt text](https://github.com/justin-chan-sjsu/linux/blob/Assignment4/NumberOfEachExit1.PNG?raw=true)
   ![alt text](https://github.com/justin-chan-sjsu/linux/blob/Assignment4/NumberOfEachExit2.PNG?raw=true)
   
   Here are the exit count results with ept disabled:
   
   ![alt text](https://github.com/justin-chan-sjsu/linux/blob/Assignment4/NumberOfEachExitEPT0-1.PNG?raw=true)   
   ![alt text](https://github.com/justin-chan-sjsu/linux/blob/Assignment4/NumberOfEachExitEPT0-2.PNG?raw=true)
   
3. What did you learn from the count of exits? Was the count what you expected? If not, why not? 

   From the count of the exits, I learned that with shadow paging enabled and EPT disabled, the VM will undergo many more exits. This is especially
   evident with exit number 0, or Exceptions, and exit number 48, which are EPT violations. I expected the number of exception exits caused to be higher
   since shadow paging should result in a greater number of page faults than EPT. I did not expect the EPT violations exit counts to be higher since EPT
   is not even used with shadow paging.
   
4. What changed between the two runs (ept vs no-ept)?

   In the KVM hypervisor, from ept to no-ept, the main change was the switch from nested paging to shadow paging. In terms of the counts of exits, shadow
   paging (no-ept) overall had more exits than nested paging. A few examples of the exit types that stood out as having more exits are exit number 0, which
   is Exceptions, which went from 22793 exits to 32068 exits, exit number 1, which is External interrupts, which went from 325379 exits to 844239 exits, and 
   exit reason 48, which went from 481725 exits to 1522620 exits.
