Implementing High Precision Timers
==================================

If the S-Bit is 0 the PARISC architecture provides userspace access to
cr16. The value of cr16 is constantly incrementing in relation to the
insn counter. The changing value of cr16 is technically allowed to be
0.5x to 2.0x the insn clock rate. In reality all PA systems have this
set at 1.0x.

A precise, but long term inaccurate, time measurement can be made by
reading cr16 at the start of a profile and then after. The difference in
cr16 multiplied by the processor clock frequency can yield the running
time of the profile in seconds.

Various problems are faced by such an implementation, such as:

- Scheduling and changing CPUs.
- cr16 wrapping.

Should the process read cr16 on CPUX and then later get scheduled to
CPUY the difference between cr16 measurements is not correct. CPUX and
CPUY are not guaranteed to have cr16 in sync. Anecdotal evidence
suggests HPUX locks all the processors in a startup loop that
synchronizes cr16, this is not done in linux.

Solving cr16 wrapping is impossible. A second reference time source must
be used to evaluate how many times cr16 has wrapped. At this point it is
not a useful light-weight high precision timing system. Instead it is
suggested that if the difference between two cr16 measurements is
greater than 1ms that the value be discarded as inaccurate and exceeding
the useful range of the measurement.

Scheduling to a cpu with a different cr16 value can be solved by having
the kernel maintain a table, indexed by cpuid, that contains the cr16
offset from cpuid zero. At cpu startup each cpu can write their own cr16
into the table. At some later point cpu zero normalizes those values,
subtracting it's own startup cr16. A process can then make a
light-weight-syscall that stores the not the value of cr16, but the
value of cr16 + the current cpuid's normalized offset. This means that
userspace sees a normalized cr16 wrt to cpuid zero. If you ignore the
scheduling constraint, then a simple "get_cycles" implementation is
presented next. The more complex implementation has "get_cycles" call an
LWS to get a normalized system-wide cr16.

Implementing get_cycles
-----------------------

The "get_cycles" function reads cr16 and returns it to the process.

If the difference between two calls to "get_cycles" returns a value
larger than 1ms then the result should be interpreted as being
inaccurate.

Implementing get_cpu_mhz
------------------------

The PDC returns the value of the processor clock and is written into
/proc/cpuinfo under an entry like this "cpu MHz : 650.000000". The
"get_cpu_mhz" function should return a floating point value that
represents the instruction clock speed of the processors.

No PA system to date seems to support different clock rates on the same
SMP system. In linux we do not support changing the clock rate of the
processor for power saving either, so it should be fine to read
/proc/cpuinfo. While this is a very ugly thing to do, it is a good
solution for high precision code.

Implementing high-precision clock_gettime
-----------------------------------------

A version of clock_gettime for glibc could be implemented where the
nanosecond field of the clock timer is filled by the values returned
from get_cycles but converted to nanoseconds through the use of
get_cpu_mhz.

Some might argue that the nanoseconds field may overflow, but as stated
before, we can merely set nsec to zero if this is the case and be no
worse off in terms of resolution than we were before.

One would have to examine if time continues to monotinically increment
when the nsec field is set to zero. It may require some small positive
offset for the sake of comparison. Eventually though one of the other
values would increment indicating a change in time.
