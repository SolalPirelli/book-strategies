# Benchmarking

This page contains questions to ask and points to remember when benchmarking a system.  
Follow it, and you’ll avoid common mistakes that make you lose time and doubt your results!  
Many of these seem obvious, but it only takes one mistake for a benchmark to be wrong. 

## Design 

Before doing anything, answer these questions to avoid losing time later on. 

**Why are you benchmarking?** The answer to this question will heavily influence your benchmark design. For instance, if you want to report on your system’s performance, your hardware needs to be fast enough to not be the bottleneck; but if you just want to show you’re as good as some other system, it’s OK for the hardware to be the bottleneck on your system as long as the comparison point performs worse.

**What is your baseline?** Absolute numbers are rarely useful. Numbers relative to baselines that people do not care about are not particularly useful either. Naming plays a role in how trustworthy a baseline is: a sample application built into some existing framework has more credibility than your own custom baseline using that same framework, even if they are functionally identical.

**How should the system and the benchmark be configured?** Even poorly-performing systems can perform well in contrived edge cases. Get an expert opinion on whether your benchmark results would be useful.

**Which measurements are most important?** You may discover unexpected bottlenecks in your system when benchmarking. The earliest you do so, the fewer measurements you’ll throw away after fixing a bottleneck. Thus, do the most important benchmarks first.

**How will you sanity-check your measurements?** Just like other code, benchmarks can be buggy; if you have no way to tell whether your measurements are even in the right ballpark, you can’t trust them.

**How will you interact with other researchers?** Double-check with everyone else to make sure they know you’ll be running benchmarks on some machines and they shouldn’t use them during that time. If you need to time-share those machines with others, establish a protocol to know who uses what when.

**Do you have multiple instances of your benchmark environment?** Benchmark time can be the bottleneck near deadlines. If you have multiple instances of the environment, you can run benchmarks in parallel. This may require you to re-think the benchmark based on the available machines; if you have 4 machines, do you really need to use 3 at a time, or could you use 2 and thus have two environments available?

## Implementation

This section is a checklist to follow when writing a benchmark harness, with examples.

**Understand and control your environment**. Modern hardware and software balance throughput, latency, and power in complex ways. Make sure you understand what those are, and how to force the environment to behave as predictably as possible. The exact configuration is a part of the overall benchmark configuration, and subject to the same validation issues.

**Automate the installation of benchmark tools**. This is particularly important if you’re configuring anything in a non-standard way. If somebody joins your project to help out with the benchmarking, they must be able to replicate your exact setup as quickly as possible, or their efforts will be wasted.

**Sanity check the benchmark**. Are you sending a certain amount of requests in a certain format? Make sure that the requests are actually in the amount and format you expect. A good way to start is to ask the benchmark to do something impossible, such as sending more network packets than physically possible, or measuring latency with more precision than the hardware allows. If your tools don’t complain when asked for the impossible, be careful about whether they actually do the possible you’re asking for.

**Triple-check your baselines**. Make sure they are doing the same task as your system. Make sure compiler flags, optimizations, and other configuration are the same as for your system. Ideally make them use the same exact toolchain and build configuration.

**Triple-check your statistics**. Use existing tools as much as possible to compute statistics such as percentiles, medians, and standard deviations. Manually check that the results are correct. An off-by-one error when counting input lines can have large consequences on the overall median in some cases, such as systems with bimodal behavior.

**Fail fast**. You don’t want to wait an hour for a benchmark to finish, only to realize that there’s a bug in your system and the benchmark requests did not get any responses.

**Think of heatup effects**. Many systems take some time to reach peak performance, for instance because they need to fill some caches or JIT compile their code. You most likely want to only measure peak performance, but don’t forget to report how much you let the system heat up.

**Minimize benchmarking time**. Do you really need to measure throughput for a minute? Would 10 seconds do instead? What about 5 seconds? On a single run it won’t make much of a difference, but when you’re collecting lots of data it adds up. This answer may vary based on whether you’re doing simple profiling to optimize, or whether you’re collecting data for a paper.

**Automate as much as possible**. You will most likely want to run a benchmark with different benchmark and system configurations. If you have to manually change the configuration and run the benchmark, not only do you risk human error, but you’ll be unable to do much work while the benchmark is running since you’ll have to interrupt your work often to re-configure and re-run the benchmark.

## Instrumentation

Sometimes external measurements are not enough. For instance, if you want to know how much fraction of the overall latency a specific step takes, you’ll need to instrument your code. This is inherently problematic, because you will by definition not be benchmarking the “real” system but an instrumented version of it. Here are some tips to mitigate this.

**Don’t measure overall performance on an instrumented system**. Just don’t. Run the benchmark with the instrumentation to measure internal details, and without to measure the overall performance. You cannot simply ignore instrumentation overhead.

**Minimize overhead within the instrumented part of the code**. It’s OK to have high overhead outside of the instrumented parts; for instance, you can get the time before an operation, the time after an operation, then print both of them, because the print will not be counted in the operation. But if you had printed the time before, then printed the time after, the first print would count towards the time difference.

**Make sure your instrumentation is valid in context**. For instance, in multithreaded code, you probably want to measure only the time spent in a given thread and not the overall system time.

**Understand and control compiler and hardware optimizations**. Compilers and CPUs can and will reorder or change instructions for performance. The code you think you are instrumenting and the code that is actually instrumented may be different. Use compiler/runtime barriers to avoid reordering, make sure the targets of pointers or jumps are not statically decidable, etc.

**Amortize the effects of hardware parallelism**. CPUs parallelize both instruction handling and memory accesses. If you instrument a single iteration of a loop, you are most likely measuring the absolute worst case with no parallelism. Instead, measure many loop iterations at once.

**Be careful when printing out averages**. It’s easy to measure some loop iterations, print out the average, then get the median of those averages. But is this really what you want? You may be hiding internal deviations.

## Execution

When running benchmarks, think about the following.

**Start with a clean slate**. Did you change any of your system or its dependencies in any way, even temporarily? Re-install them; this is a quick operation that can save you from lots of headaches later when you have to throw away results because you forgot about “temporary” changes.

**Replicate known or trivial results**. Did someone else run benchmarks already? Make sure you can replicate their results. Benchmark the system with a configuration where you already know what the result will be, such as completely overloading the system, and check if the measurements reflect your intuition.

**Investigate before collecting tons of data**. Collecting data takes time. If you see unexpected behavior, collect a few data points to help understand it, but don’t spend days collecting all kinds of data just in case.

**Pinpoint problems before theorizing about them**. There is no point in coming up with complex theories about why performance is not as it should if you don’t know where the problem is. If you didn’t expect the benchmark results, you likely can’t predict why those results happen either. Pinpoint the problem as quickly as possible, such as by instrumenting code to find the bottleneck, then come up with a good explanation once you know where the problem is.

**Commit to version control every time you benchmark**. Whenever you change anything in the system or benchmark, after you’ve gotten results, commit with a clear description and push. This will save you from wasting time replicating your own past results when investigating further. Write down the commit ID used for benchmarks in any paper. Either give the commit ID in the text when referring to a public online repository, or put it in comments if your code is not public. Do this even for drafts. Replication is excessively hard without this.

**Delete useless benchmarks**. That’s what version control is for. Don’t confuse colleagues with benchmarks that do not mean anything any more.

## Improving performance

**Early bottlenecks are rarely complex**. If you haven’t thought much about performance when implementing your system, there’s a decent chance the bottleneck is something simple, like a custom hash function being poorly suited for the occasion. Don’t spend much time optimizing if you don’t have benchmarks showing what the bottleneck is, and don’t start by looking at edge cases.

**Beware of log statements**. Even if they do not actually perform logging, they may still format the string to be logged. Double-check what the code actually does.

