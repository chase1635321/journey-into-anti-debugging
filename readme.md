# Journey into anti-debugging
## Resources
 - Use the "Ultimate" anti-debugging reference: https://anti-reversing.com/Downloads/Anti-Reversing/The_Ultimate_Anti-Reversing_Reference.pdf
 - OpenRCE Anti reverse engineering database: http://www.openrce.org/reference_library/anti_reversing
 - Art of unpacking whitepaper: https://www.blackhat.com/presentations/bh-usa-07/Yason/Whitepaper/bh-usa-07-yason-WP.pdf

## Techniques
### Basic
 - Windows/Linux API: IsDebuggerPresent()
 - CheckRemoteDebuggerPresent()
 - CoseHandle()/NtClose(). Idk how this works.
 - FindWindow() can find debugger by providing window class
 - Check for debugger installation
 - NtGlobalFlag. Idk how this works.

### Advanced
 - Timing defense. Check timestamp after each function run.
 - Count number of 0xcc (breakpoint) bytes (usng repne scasb instruction)
 - Checksum the opcodes
 - Hardware breakpoint detection using GetThreadContext()/SetThreadContext()

### Interrupts
 - Two byte INT3 can be used as false breakpoint
 - INT 0x2C rases a debug assertion exception
 - ICEBP Generates a single step exception
 - Trap flag: Generates a single-step exception after executing an instruction
 - Stack segment: Tracing over SS (e.g. mov ss, pop ss) the debugger will not break on those, effectivley stopping on the folowing instruction. In other words, and unset of the trap flag won't be possibel after that.

### TLS callbacks
 - Thread Local Storage (TLS) callbacks happen before entry point, so analyst won't know what's happening.

### Anti-VM techniques
 - CPUID: this instruction is executed with eax=0x1 as input, and return value describes the processor features. The 31st bit of ECX will be 0 on a physical machine, on a guest VM, it will equal 1.
 - Another method executes it with EAX=0x40000000 which is called "hypervisor brand". The return result will be the virtualization vendor (VMwareVMWare, Microsoft HV, etc). Result is in ECX and EDX.
 - Check for known VMWare/Virtualbox mac addresses. 
 - Red Pill is an anti-VM technique that executes the SIDT instruction to get the value of the IDTR register. The VM monitor most relocate the guest's IDTR register to avoid conflict with host, so the VM IDTR is returned.
 - No Pill is another technique that uses the fact that the LDT structure is assigned to a processor. I's location on host machine will be zero and on VM it will be non-zero.

