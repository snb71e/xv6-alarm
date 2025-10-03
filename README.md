# xv6-lab: Traps (sigalarm & sigreturn)

This repository is based on the **Traps Lab** from the xv6 **Operating System** assignments.  
It extends xv6 by implementing two new system calls: `sigalarm` and `sigreturn`.



## Implementation Details

- **New System Calls**
  - `int sigalarm(int ticks, void (*handler)());`
  - `int sigreturn(void);`

- **Extensions to `struct proc`**
  - `alarm_interval`: alarm period (in ticks)
  - `alarm_ticks_left`: ticks remaining until the next alarm
  - `alarm_handler`: user-level handler function pointer
  - `in_alarm`: flag to prevent re-entrant alarms
  - `alarm_tf_backup`: saved trapframe before invoking the handler

- **Workflow**
  1. User calls `sigalarm(ticks, handler)` to register the alarm interval and handler.
  2. On each timer interrupt, `alarm_ticks_left` is decremented.
  3. When it reaches zero, the kernel backs up the current trapframe and sets `epc` to the handler address.
  4. The handler executes in user space.
  5. When the handler calls `sigreturn()`, the trapframe is restored, and the alarm is re-armed.

---
## Modified Files
- `user/user.h` : added function prototypes for sigalarm and sigreturn
- `user/usys.pl` : added system call entries
- `kernel/syscall.h` : defined SYS_sigalarm and SYS_sigreturn
- `kernel/syscall.c` : added syscall mapping
- `kernel/proc.h` : added alarm-related fields
- `kernel/proc.c` : initialized alarm fields
- `kernel/sysproc.c` : implemented sys_sigalarm and sys_sigreturn
- `kernel/trap.c` : added alarm handler logic in the timer interrupt path

## Run test in xv6
At the xv6 prompt:
- `alarmtest`: All tests (test0–test3) passed successfully.  
- `usertests -q`: All user tests passed (ALL TESTS PASSED).

---

## References
- xv6-labs-2020: Traps Lab