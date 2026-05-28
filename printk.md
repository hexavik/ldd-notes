`printk` is controlled by `/proc/sys/kernel/printk`.

## Where exactly `printk` output goes?

It doesn't write to a standard terminal or a simple text file like a user-space application would. Instead, we are sending messages to the **Kernel Log Buffer**.

Because the kernel doesn't have a "screen" of its own, it stores these messages in a ring buffer in memory from there, several tools pick them up so you can actually read them.

1. The Kernel Ring Buffer (`dmesg`)
   The primary place `printk` output lives in a fixed-size memory buffer. The most common way to view this is the `dmesg` command.

2. System Log Files
   On most modern Linux distributions, background services (daemons) collect these kernel messages and write them to persistent files on your disk.
   - For Ubuntu/Debian/Arch: Look at `/var/log/syslog` or `/var/log/kern.log`
   - For RHEL/CentOS/Fedora: Look at `/var/log/messages`

2. The Systemd Journal
   If your distribution uses `systemd` (which most do these days), the kernel messages are also swallowed by the `journald` service. Run `journalctl -k`. The `-k` flag tells it to show only kernel messages.

| **Source**      | **Command to view**         | **Best for...**                     |
| --------------- | --------------------------- | ----------------------------------- |
| **Ring Buffer** | `dmesg`                     | Quick debugging of recent events    |
| **Journal**     | `journalctl -k`             | Searching logs from previous boots  |
| **Log Files**   | `tail -f /var/log/kern.log` | Monitoring background system health |

> [!NOTE] Important Note on Log Levels:
> if you used a "simple" `printk("Hello world\n");` without a log level (like `KERN_INFO` or `KERN_ERR`), the kernel assigns a default level. If that level is too "quiet" (high numerical value), it might not show up in a certain logs depending on your system's configuration.

