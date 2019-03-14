Things to remember to talk about
========================================
1. Stack overflow -- when does that happen & the role of the OS in it
2. The heap - revisited
3. Statically allocated storage
4. System calls
   -> syscall interface design? Maybe an article of its own



Notes
========================================
-> There can only be one JOS environment active at a time, & so the kernel just needs one stack.
    1. even if we have multiple CPUs, you can't have processes running in parallel!
    2. if multiple envs could run at the same time (on different cpus), then the kernel needs to serve syscalls whenever the env
    does one. For that, you need a stack. If both cpus use the same stack, they'll just overwrite their memory! So you need one stack
    per cpu at the very least.
        -> Wrong. Each cpu has its stack (see boot_aps()).
