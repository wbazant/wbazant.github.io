

Some pipelines are easier to work with than others, and there is a number of qualities that affect it, e.g. how well you can tell what is going on at any time, how long it takes until mistakes manifest themselves, and how predictable the correct behaviour is. I want to talk about one such characteristic, and a fairly elusive one: rerunnability. You know the pipeline has this quality when you can hack on it: kick off a sample run, see what the logs complain about, adjust the inputs slightly, and so on until successful, productively and with no fear of messing up.

There's a lot of theoretical models of "running stuff lots of times and all over again", and I want to start with a very old one.

### Reentrancy
In concurrency theory, we have a concept of a program or routine being [reentrant](link to wikipedia):

*definition*

*example with swap, and how it can go wrong*
Interestingly knowing that the above can go wrong is somewhat irrelevant to modern computer programs, because they don't use global memory addresses like aknowing that the above. Variables in any programming language get tied up with memory addresses residing in some kind of this-run-of-the-program-only space, subroutine calls get variables in memory addresses assigned only to them, and isn't there a lot of cleverness involved in how it all works.

So, that reentrancy definition is from the time where the above was being worked out. It's still relevant for pipelines, because they do operate on global state of databases and filesystems. This is, however, stronger than what we want: it's okay if two concurrent runs of the same thing have an undefined result, because we don't do such a crazy thing. The runs are all separate, and we're happy to wait until the pipeline finishes (sometimes prematurely and abruptly after we give it the right signal).

### Idempotence
The mathematical concept of "Running twice is the same as running once" is idempotence. More formally:

    Let f: X -> X. f is **idempotent** iff, for each x in X, 
    f(f(x)) = f(x)

There is a very rich theory of idempotent functions. For example, in linear algebra, any two idempotent linear transformations happen to commute: i.e. `f then g` is the same as `g then f`. I will not spoil the surprise of why that is by providing a proof ;) and anyway, I want to talk about pipelines so instead I'd like to give a couple of examples of idempotence:

- `rm -rf `, `make clean`: wipe something out
- `touch`: the newest result overwites the previous one
- `INSERT IGNORE`: do nothing if there is a result in place
- compute then cache result

A really good case study for both how to achieve idempotence, and the gains from it, is `GNU make`, the old school conditional compilation tool for C projects. `make` can look at timestamps of your source files, and timestamps of their dependencies, and recompile whenever something needs to be refreshed, i.e. whenever the dependency is newer than the dependent. A big project will have lots of source files, recompiling everything will take a while, and the programmer wouldn't want to do that with every change: `make` comes to the rescue by not doing the unnecessary work.

Runs of `make` are indeed idempotent: two runs on the same source files are the same as one, and the second run takes no time at all.

Projects organised by make have another property: as you make small changes to the source files, and occasionally `make compile`, only the newest result matters, the old computation results are reused when they can be but otherwise forgotten. 

### Rerunnability 
I would like to formalise this by tweaking the definition of idempotence. Let X be what it was, and C some config space of inputs passed in. Then

    Let f: X x C -> X. f is **Wojtek-made-up-rerunnable** iff, for each x in X, and c1, c2 in C,
    f(f(x, c1), c2) = f(x, c2)

When you tweak the config, the newest result wins. The old runs do not mess up the newest one. The result depends on the run was configured, and mess-ups do not matter provided you make it good in the end.

To search for anti-examples, consider cases of previous results stopping operation or getting improperly reused. The pipeline leaves temp files behind, and also refuses to overwrite temp files. The `INSERT IGNORE` sees previous data and silently does nothing. Some runs wipe out data needed for other runs.

We can abstract away some properties of `make` runs into necessary conditions for rerunnability:
- what's wiped out can be recreated
- results depend on their inputs only
- it is clear when partial results can be reused
- stale partial results get wiped out

### No bad inputs
An input is bad if a run with the input messes everything up. You don't want that, instead you want them rejected:

    f(x, c1) = x if c1 is a bad input, for any x

### Caching done right
Reusing partial results is good for reasons of not having to wait for stuff. Say you have no patience to download a file over and over:

    [ -f file-from-internet.txt ] && curl www.example.com/my-input > file-from-internet.txt

This is good when the runs are a few minutes apart as you're debugging, but perhaps not between data releases. So if you tie paths to data releases, you can do the above while keeping rerunnability, and impunity. 
### Trade-offs
A program is rerunnable if it doesn't reuse partial results, but this loses the nice property of not redoing unnecessary work. You can achieve better trade-offs than this by taking care that the results get reused. 








