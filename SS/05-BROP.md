## Fingerprint of strcmp

| arg1     | arg2     | result  |
| -------- | -------- | ------- |
| 0x0      | 0x0      | crash   |
| readable | 0x0      | crash   |
| 0x0      | readable | crash   |
| readable | readable | nocrash |

## strategy



* Step I: find the offset to overwrite the return address
    * What if we have stack canary?
* Step II: find the stop gadget
* Step III: find the BROP gadget
* Step IV: find the puts plt address
* Step V: dump the binary