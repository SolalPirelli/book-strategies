# Artifacts

This page will help you publish usable artifacts as a result of your research, so you can have:

* **Reproducible research**. If no one can reproduce your results, how can you trust them?
* **Higher impact**. If no one can understand your work, your impact is limited to theory.
* **Higher productivity**. All the time you invest will more than pay for itself.

The last point may sound doubtful; after all, any time you spend writing a build script or setting up continuous integration is time you’re not spending doing cool research! But you will eventually have to change machines, and remember all the dependencies of your project. You will eventually have to edit your code, and re-learn how your code works. You will eventually have to run your benchmarks again, and re-discover how you did it the first time. You will eventually have to convince someone else of the validity of your results, and re-learn why your code works.

There is a very high chance that at least one part of your workflow is non-standard: patch some dependency to fix a bug you uncovered, run multiple tools in a brittle order to work around known limitations, use specific versions of tools for compatibility… you cannot hope to remember all of this, let alone have someone else discover it.

Any result you include in a paper that cannot be automatically reproduced from a known version of a known repository could require you to retract the paper if you cannot reproduce it later. Imagine having to retract a paper because you executed the benchmarks wrong, didn’t realize it since you don’t even remember how to run them, and your system actually performs worse than your baseline! No amount of time gained by cutting engineering corners can outweigh this. 

## Code properly from the beginning

Research code is synonymous with hacked-together prototypes that only do the exact task they were designed for, and cannot work on any other input. This is normal, research is a naturally fluid activity without a clear specification, otherwise you’re doing engineering.

But this does not mean your code has to be ugly! It means your code will be filled with TODOs for what to expand on later, FIXMEs for code that only handles a specific kind of input or environment, as well as exceptions and error codes returned any time the task at hand is not exactly the one you are solving.

Perhaps the user has to input data in a weird-but-easy-to-parse format, or install a specific version of an esoteric library they have never heard of. This is fine. What is not fine is having to understand the code just to know which command-line arguments to give, which dependencies to install, what inputs will silently result in bad data…

You will write examples of how to use your code when manually testing it; you might as well reuse those examples as documentation, or even automated tests.

## Design the paper and the artifact together

In your paper, you will refer to your code many times; what it does, how it does it, why it does it that way, how fast it is, how many lines of code it has… Make those numbers obvious from the artifact itself. If it takes you, the author of the code, significant effort to describe the code, there is no way someone else can understand the code, which means a single mistake from you can sink the paper since nobody can double-check.

This will also force you to make your methodology and results explicit. For instance, if you include in your paper the number of lines of code per component, measuring this should be trivial from the code. If the components aren’t clearly separated, and it takes effort to know which file to include in which category, are they really components at all?

Design your artifact around the questions your evaluation will answer; each question should have an associated script that runs the system with the right input and environment, and spits out the output in some easy-to-read format. The output needs not be directly embeddable into the paper, but it must require no further computation. Otherwise, you risk messing up the numbers, even if it’s a simple matter of adding together some numbers. Are you really sure you the right numbers were used in right operations, for every single cell of every single table in the paper? The answer must be an unconditional "yes”, not "uh... probably".

Don’t forget that you will re-run your evaluation benchmarks many times, especially as the deadline draws closer and you fix bottlenecks. If running the benchmark requires careful attention for a significant amount of time, not only will you lose time but you may waste lots of time fixing non-bottlenecks that a flawed benchmark run led you to suspect.

## Artifact users have no context

You have been working on your cool system and paper for months if not years, and you know it inside-out; the names of the various components have changed with time to be more descriptive, the structure has been refactored several times, dependencies have changed... You know all of this. Others don’t. All they can do is read the paper, and read whatever documentation your code has. They will only read the code if they are interested by the rest. Thus, here are guidelines for your artifact, with some examples.

### Use consistent terminology between paper and artifact.

If the paper talks about “models”, don’t refer to “stubs” in code. If the paper mentions your system has a library and a front-end, this separation should be obvious in code.

### Explain how to obtain all paper figures and tables.

This is a short but essential addition to your documentation: what should one do to replicate every single number in your paper? You may need to document your exact environment, such as hardware, kernel parameters and other such specifics if your system depends on it.

### Map the clean concepts in your paper to the messy prototypes in your code.

Your paper describes a clean system that automatically performs a complex task; your code requires the user to do some work that could be automated by obvious engineering work. Explain the differences between the two.

### Explain how to use the extensibility points.

How do I write a new plugin for your system? In which format should I write a new input to be analyzed? This must be easy to do, not a long list of files to copy and modify.

### Double-check every file in your repository.

Are all files there? Are all the files that shouldn’t be there absent? Are there any files you copy/pasted from some public source and forgot to attribute properly? 

### Test the artifact thoroughly on a clean machine.

This means using the public repository you will refer to in the paper, on a machine with a fresh OS install, either on bare metal or in a virtual machine or container. No shortcuts. It’s the only way to truly gain confidence in the artifact. Incrementality is good when developing, but testing must be done end-to-end.

### Get someone unfamiliar with the codebase to test the artifact.

It’s hard to follow the documentation you actually wrote and not the one you have in your mind. A third party will tell you if your documentation is good enough or not.

