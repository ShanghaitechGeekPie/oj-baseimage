# OJ Sample judging image

This image serves as a base for grading student submissions. 
There are many hardcoded restrictions so please do read this document! 
If in doubt, you can consult the reference implementation.

## Hardcoded stuff

### `/submission` 

Student submission will be placed here. 
Please note that student may name their program entry point (or anything) arbitrarily! 
It's up to you to force them to use one you can understand! 
Of course this means nothing for those ecosystems that enforce a naming convention.

### `ENTRYPOINT` and `CMD` instructions in Dockerfile

`ENTRYPOINT` has to point to a executable that takes exactly one parameter.
This parameter will be one of `setup`, `run` and `grade`, indicating it's in that stage.

`CMD` will NOT work, as it will be overwritten by the OJ. Just don't use it.

## How does this image works

Most mechanisms can be replaced by your custom mechanisms, but the overall workflow must remain the same. A full judging cycle is made up of 4 stages.

### Stage Genesis

During this stage, the Dockerfile is ran on the judging servers you have designated. 
However, those scripts/programs specified in ENTRYPOINT instruction will not run now.

Typically, you'd wish to install everything needed by the grading scripts and some library or building tools students may need. 

If you wish, you could also dynamically generate a reference output based on a reference program and a reference input. Using randomly generated reference input is not advised as it could break the reference program unless you put unnecessarily large amount of person-hours into improving it.

Remember, if you want something in this folder to be available in later stages to grading scripts, you have to COPY them.

Unlike all other stages, this stage will be run AT MOST ONCE PER JUDGING SERVER.

### Stage Setup

From this stage on, you will not touch Dockerfile anymore. 

During this stage, the student submission is taken from his git repo and placed into `/submission` folder.
The grading script should setup student-defined dependencies and compile his program (if needs to). 
If setup/compile/whatsoever failed and the grading script exited with a nonzero return code, student gets 0 score and a big red Compile Error. 
As a consequence, this stage should not involve actual running the main program of student or he may get a confusing error.

If your assignment also requires some other setup, e.g. a service, you should do it now.

### Stage Run
During this stage, you run the student homework, but please don't grade it yet. 

**Don't do anything else.**

Exiting with nonzero code at this stage will have various meanings:

    RESULT_WRONG_ANSWER = -1
    RESULT_CPU_TIME_LIMIT_EXCEEDED = 1
    RESULT_REAL_TIME_LIMIT_EXCEEDED = 2
    RESULT_MEMORY_LIMIT_EXCEEDED = 3
    RESULT_RUNTIME_ERROR = 4
    RESULT_SYSTEM_ERROR = 5

All others will be treated as Unknown Runtime Error.

### Stage grade
During this stage, you grade the student output from previous stage. 
## Writing back info

As a convention, containers log data by printing it to console. 
#   o j - b a s e i m a g e  
 