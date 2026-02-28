[[Raspberry Pi 5]]
[[Components]]

[[Learning Path]]


---

Ch 1 [[Introduction to Drivers]]



---


Interview Preparation (12 Hour)

- [[Kernel Basics]]
	- Monolithic vs microkernel
	- Process vs interrupt context
	- User Space vs kernel space
	- System call flow
	- What happens when a module is loaded
- Device Driver Fundamentals
	- Types of Drivers
		- Character driver
		- Block driver
		- Network driver
		- Platform vs PCI vs USB drivers
	- file_operations Structure
	- copy_to_user vs copy_from_user
	- Major and Minor Numbers
- Memory and Concurrency
	- kmalloc vs vmalloc
	- GFP_KERNEL vs GFP_ATOMIC
	- Spinlock vs mutex
	- Why you cannot sleep in interrupt context
	- What is bottom half (tasklet/workqueue)
- Interrupts & Hardware Interaction
	- Interrupt flow
	- request_irq()
	- free_irq()
	- Shared interrupts
	- Top half vs bottom half
- Debugging
	- How to debug kernel panic?
	- What is printk?
	- What is dmesg?
	- How to enable dynamic debug?
	- How to analyze oops trace?