1. For each member in your team, provide 1 paragraph detailing what parts of the lab that member implemented /
   researched. (You may skip this question if you are doing the lab by yourself).
   
   For this lab I had worked alone.

2. Describe in detail the steps you used to complete the assignment.

   - I used the environment I had set up during Assignment 2. The environment used is an Ubuntu 20.04 VM built using VMWare Workstation 16 Player. 
     The Linux kernel source code was cloned from the torvalds repository and built on the VM.
     
   - I started making my code changes in the vmx.c file located arch/x86/kvm/vmx/ directory since this is the file where vmx exits are handled. 
     In this code, I globally declare a new variable _Atomic u32 number_of_each_exit[70]. This variable is used to keep track of how much each exit type
     was triggered in the KVM code.
     
   - Still within the vmx.c code, I added additional code to the __vmx_handle_exit function. The code I added was an increment of the 
     number_of_each_exit[exit_reason.basic] variable. The exit_reason.basic index is already used within the KVM code to indicate the 
     type of exit being handled.
     
   - I then added changes to the cpuid.c file located in the arch/x86/kvm/ directory. This code file deals with calls to the CPUID instruction.
     I globally declare the variable extern number_of_each_exit[] which references the variable with the same name in the vmx.c file earlier.
     
   - I then added changes to the kvm_emulate_cpuid function in the cpuid.c code. I added an if-statement check to determine if the
     eax variable matches the leaf value specified in the assignment (0x4FFFFFFE). If eax matches that value, I check the exit reason, which should be
     stored in the ecx variable. I do two checks; the first check I do is if the exit reason supplied is not one provided in the SDM, I set eax, ebc, and ecx
     to 0 and edx to 0xFFFFFFFF. I then check if the exit reason is supported by the KVM. If it is not, I set eax, ebx, ecx, and edx to 0. Otherwise, if the
     exit reason is supported by both the SDM and the KVM, then is set eax to be equal to number_of_each_exit[ecx], which should be the number of exits
     for that exit type.
     
   - Some miscellaneous notes about the cpuid.c code changes above; it is possible that an exit reason is defined by the SDM but is not supported by
     the KVM. However, exit reasons not supported by the SDM are also not supported by the KVM. In this case, the edx is set to 0xFFFFFFFF (indicating that
     the exit reason is not defined by the SDM) instead of 0 since the SDM condition is checked before the KVM one. Also, the basic exit reasons are found
     in Appendix C of Volume 3 of the SDM. The exits supported by the KVM are found in the Linux kernel source code; they are found in the 
     linux/arch/x86/include/uapi/asm/vmx.h file. 
     
   - All code changes made for this assignment are only in the cpuid.c and vmx.c files mentioned above.
   
   - After the code changes were made, the code has to be built. However, with the addition of the global variables above, the modules by themselves cannot
     be build by just using the make command. In order to rectify this, use make menuconfig and go the the Virtualization section to make all options 
     built into kernel as opposed to being built outside of it.
     
   - After this is done, just use the make command again and let the kernel build. This may end up taking much longer since because of the change to menuconfig,
     the entire kernel may need to be built again. After the kernel is build, use sudo make install to install the kernel and sudo make modules_install to install
     the built kernel modules. Once this is done, reboot the VM so that the new kernel can be loaded.
     
   - Now that the new kernel has been build and loaded, KVM can be used as a hypervisor for a nested VM. The VM was created using the virt-manager Linux application.
     The VM built was another Ubuntu 20.04 VM.
     
   - After the nested VM was created, a test C program was created to test out our added CPUID functionality. In this program, I test the exit numbers 0 and 1, since
     both these exit numbers are supported by SDM and KVM. I also test 70 and 71, exit numbers not defined in the SDM, and exit numbers 5 and 6, which are defined
     in the SDM but are not supported by the KVM. Below are images of the test results.
     
     ![alt text](https://github.com/justin-chan-sjsu/linux/blob/Assignment3/testing.PNG?raw=true)
     
   - Once this test was completed, I ran another test program using CPUID, that listed all the exit numbers supported by the KVM and their exit counts. 
   
3. Comment on the frequency of exits – does the number of exits increase at a stable rate? Or are there more exits performed during certain VM operations? 
  Approximately how many exits does a full VM boot entail? 
  
   The exits are pretty frequent. However, there were more exits for certain exit types than there were for other exit types. Most exit types had exit counts of 0.
   This makes sense in many of these cases; for example, exit reason 20, which had 0 exits, indicates that a VM attempted to execute VMLAUNCH, which a machine 
   should not really be doing during boot up. In total, it seems the VM exited about 3.45 million times.
  
4. Of the exit types defined in the SDM, which are the most frequent? Least? 

   The four most frequent exits were exits 32 (Guest attemtped to execute WRMSR) with 1155701 exits, exit 48 (EPT violation) with 481725 exits, exit 12 (HLT) with 
   326274 exits, and exit 1 (External Interrupt) with 325379 exits. Many exits are tied for least frequent with 0 total exits. A few examples of these exits include
   exits 2 (Triple Fault), 3 (INIT signal), and 4 (Start-up IPI).
