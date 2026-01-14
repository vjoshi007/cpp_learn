1. Start With an Explicit Target Architecture (NEW, STRONGLY EMPHASIZED)

New emphasis from Vuopio + Koskinen:
Do not rely on Catapult to “find” a good architecture.

Before writing C++:

Decide:

Throughput target (samples/cycle)

Latency target (cycles)

Parallelism (how many MACs, multipliers)

Memory structure (streaming vs buffered)

Sketch a block diagram (even rough)

Vuopio shows that small code changes radically alter RTL and that designers who had a clear target microarchitecture achieved RTL comparable or better than hand-written RTL 

nbnfioulu-202109098961

.

Practical rule

Write C++ as if you were writing RTL, but with loops and types.

2. Bit-Accurate Types Are Non-Negotiable (REINFORCED)

Already stated earlier, but both theses confirm:

Use ac_int and ac_fixed everywhere

Never use int, long, float, or double in synthesizable code

Size everything deliberately:

Counters

Accumulators

Coefficients

Indices

Koskinen’s beamforming multiplier shows that precision choice dominates area and power, often more than scheduling choices 

master_Koskinen_Otto_2018

.

Wireless-specific guidance

Use full precision internally, then truncate once at outputs

Avoid repeated truncation inside loops (causes extra logic and power)

Prefer wrapping over saturation unless required (saturation adds logic)

3. Throughput First, Then Area: Pipeline Everything That Streams

Strong consensus across all sources:
Wireless DSP = streaming workload.

Rules

Main processing loop → pipeline

Aim for II = 1

Accept extra registers if needed (power is usually lower than logic depth)

The Blue Book’s pipeline feedback chapter + Koskinen’s recursive datapaths show:

Feedback paths must be explicitly delay-balanced

Catapult will not “fix” mismatched feedback automatically 

https:cse.usf.edu:~haozheng:tea…

.

Pattern for DSP loops
for (int i = 0; i < N; i++) {
  #pragma hls_pipeline II=1
  acc = acc + x[i] * h[i];
}


If acc feeds back:

Insert shift-register delays manually until II=1 is achievable

4. Control Logic: Make FSMs Explicit and Small (REFINED)

Vuopio’s thesis highlights that control-heavy code is where HLS fails most often unless carefully written.

Best practice

One explicit state variable (ac_int or enum)

One switch(state) per cycle

No implicit control via nested loops + breaks

Avoid

Deeply nested if-else

Variable loop bounds tied to external signals

Software-style “early returns”

The Blue Book’s control-feedback warnings are validated directly by Vuopio’s case study 

nbnfioulu-202109098961

.

5. Loop Structure Determines Hardware More Than Pragmas (NEW INSIGHT)

A key takeaway from Vuopio and Koskinen:

Pragmas refine architecture — loops define it.

Loop rules

Fixed bounds whenever possible

Constant upper bounds + break if needed

Narrow loop counters (3–6 bits often enough)

Optimized loop control reduced logic depth and power in both theses, consistent with Blue Book examples 

https:cse.usf.edu:~haozheng:tea…

.

6. Unroll Only When Memory Can Feed It (REINFORCED)

Koskinen’s beamforming work shows a classic HLS pitfall:

Aggressive unrolling → resource explosion

Performance limited by memory ports, not compute

Rule

Never unroll a loop unless:

Data comes from registers or

Memory is partitioned / multi-ported

For ASIC:

Prefer register files + streaming FIFOs

Avoid large multi-port RAMs unless unavoidable

7. Streaming Interfaces Beat Arrays for Wireless DSP (NEW EMPHASIS)

Across all sources:

Arrays imply memories

Memories imply bottlenecks

Bottlenecks kill II

Prefer

ac_channel<T> for:

Sample streams

Coefficients streams

Control tokens

Vuopio shows that moving from arrays → channels:

Simplifies scheduling

Improves throughput

Reduces control logic 

nbnfioulu-202109098961

.

8. Templates Are for Scalability, Not Cleverness (REFINED)

Koskinen’s multiplier is highly templated, but he also shows:

Over-templating increases compile time and debugging complexity

Use templates for:

Bit widths

Vector/matrix sizes

Number of parallel lanes

Avoid templates for:

Control flow

Algorithmic branching

9. Verify Continuously Against a Golden Model (REINFORCED)

All sources agree:

HLS debugging is architectural debugging

Bugs often appear only after scheduling changes

Best practice

Keep a floating-point or high-precision C++ reference

Compare outputs at every iteration

Add assertions on:

Range

Overflow

State transitions

The Blue Book strongly recommends this, and both theses confirm it saves massive time 

https:cse.usf.edu:~haozheng:tea…

.

10. Power Is a Side-Effect of Architecture (NEW SYNTHESIS)

From beamforming + Catapult studies:

Power optimization comes from:

Shorter combinational paths

Fewer toggling signals

Regular pipelines

Practical tips

Prefer pipelines over deep logic

Avoid wide multiplexers (use one-hot where possible)

Gate control updates (only change state when needed)