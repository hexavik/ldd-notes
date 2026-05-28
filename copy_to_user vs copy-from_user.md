Why can't we just do:

```c
memcpy(user_buf, kernel_buf, size);
```

Because:
- User space memory is not directly accessible
- Might cause page fault
- Might access invalid memory

So we use:

```c
copy_to_user()
copy_from_user()
```

They:
- Validate address
- Handle page faults
- Return number of bytes NOT copied

