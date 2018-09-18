## RAM Output
```html
check-my-memory.py - version: SJ 2013-02-22
OK, you're root
ANALYSIS:
Total of physical memory modules found 28672 MB in 4 memory module(s)
cat: /var/log/dmesg: No such file or directory
BIOS offers 0 MB as usable
cat: /var/log/dmesg: No such file or directory
Memory seen by OS 24086 MB
BIOS version 11/08/2017
CPU is PAE enabled
CPU is x86_64 64-bit enabled
OS is x86_64 64-bit

SUMMARY:
Memory difference between DIMM hardware and BIOS offering 28672 MB
Memory difference between BIOS offering and memory seen by OS -24086 MB
Memory difference between DIMM hardware and memory seen by OS 4586 MB

ADVICE:
Your BIOS is not offering all of your physical memory. Try to update your BIOS, and/or enable 'memory hole remapping / hoisting' in your BIOS to get more usable memory

Finally: show more detailed memory info from lshw. This can take up to 30 seconds ...
       description: System Memory
       size: 28GiB
          description: DIMM 1333 MHz (0.8 ns)
          size: 8GiB
          description: DIMM 1333 MHz (0.8 ns)
          size: 8GiB
          description: DIMM 1333 MHz (0.8 ns)
          size: 8GiB
          description: DIMM 1333 MHz (0.8 ns)
          size: 4GiB

Finished
```
