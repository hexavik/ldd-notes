Every Linux driver follows this lifecycle:

```text
Boot
  ↓
Kernel init
  ↓
Bus detects device
  ↓
Probe()
  ↓
Driver registers file_operations
  ↓
User opens device
  ↓
Interrupt occurs
  ↓
Bottom half processes
  ↓
User reads/writes
  ↓
Remove()
```

