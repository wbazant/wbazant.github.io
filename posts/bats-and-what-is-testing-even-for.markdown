---
title: Testing Bash scripts and what is testing even for
tags:
  - programming
date: 2017-03-30

---

An externally facing application is tested before delivery to assure it meets expectations placed in it. [Uncle Bob is sternly clear that you're really not doing a good job if you don't have an automated means of doing that](http://blog.cleancoder.com/uncle-bob/2017/10/04/CodeIsNotTheAnswer.html). This of course assumes you care about the expectations people place in your software, but (even outside the commercial setting where people try to get others to pay for their work) disputing that assumption is only allowed in what my friend calls a "philosophical free-for-all" - perhaps you are the [Rothko](http://www.tate.org.uk/art/artworks/rothko-red-on-maroon-t01168) of coders demonstrating that software having to do something is just another assumption to be challenged, or [another V the cloaked renegade](https://www.youtube.com/watch?v=YaGxhpe1NZ4) directing an orchestra of humming fans and creaking CPUs.

When you're employed on a large software development project, you gotta write tests, maybe even before you write the code, and then you gotta submit pull requests, and there will be tools checking that you wrote enough tests, and that they pass. In a less hip but equally large environment, like a bank, you still you gotta run tests, and e.g. attach their results in a spreadsheet. 

At Expression Atlas, I did some work on the experiment import pipeline. I could choose how many tests to write, and it wasn't typical to write tests, but I believe it's quite neat that I did. 

---
When you're making a product for other users, it comes with a set of expectations about it. If you benefit from the users interacting with your product - perhaps it has a functionality they need and are paying you for - your venture relies on the users being able to do what they want to do. When you're making a product for someone that won't be the user, but will be the one benefitting from the users interacting with the product, it's not quite the same because you're trying to make someone else happy. Nevertheless in this situation the expectations from the product will be even more clear and perhaps exactly described via contract. This was something else entirely: the developers of the pipeline were also its users, so there was no delivery. The goal of my work was to make future operations more successful, in the hands of whoever will do them: giving them better tools for their job.

The second round of our free-for-all is about whether you even want good tools- maybe you don't mind taking a long time doing relatively simple tasks, not unlike a zen monk drawing lines in their stone garden, or maybe accomplishing things is just a social construct that you happen to reject. Similarly up for discussion is the notion that one day, the functionality you are today trying to provide will cease to be required, and if you successfully dispute one of these, you've pretty much contradicted what I hoped will be a noncontroversial statement, which is that there are lower and upper bounds on quality to aim for when developing. [The Law Of Equal And Opposite Advice](http://slatestarcodex.com/2014/03/24/should-you-reverse-any-advice-you-hear/) suggests to tell yourself both "don't worry if the program you wrote came out a bit unfortunate", and "you'll be glad you have put in this additional effort", see which one sounds more familiar, and then do the other one.

---
Actually, starting to write tests for bash scripts can be an opportunity to go in either direction, and thus help you meet the expectations discussed before. When you'd like to save on effort because currently it takes you lots of work to keep the quality of your pipeline high enough, add some tests and then skip most of current checks. If you're observing not to be putting enough effort into development - you can tell that by e.g. the tip of the branch being frequently broken, and you coming back to fix things - e.g. a test that runs the whole script and checks it didn't fail is probably a good return on your investment.

I have been using [BATS](https://github.com/sstephenson/bats) for this. The best effort saver I know is a test that runs your script and checks if `$?` is zero. It's almost free to set up, because you will surely run this thing you are writing at least once, so you already have the command - and then you can rely on that instead of actually running your script and examining what happened. Everything above checking the exit status is in the direction of "spend more time, raise quality" - maybe a check on the output of your program? Or putting bounds on the time it took?

Installing a linter plays out similarly - let me give a concrete example of what it's good for. A typical Bash caveat that e.g. [Shellcheck](https://www.shellcheck.net/) will tell you about is when you need to pass arguments between functions: `"$@"` is correct. Let me demonstrate:

    f(){ echo f received $# args: $@ ; }
    g1(){ f $@ ; }
    g2(){ f "$@" ; }
    g2 is the correct function
    g1 appears to be indistinguishable from it
    
    #f counts 9 args
    g1 "the difference" "is when you have spaces in args"
    
    #f counts 2 args
    g2 "the difference" "is when you have spaces in args"

Shellcheck will emit [SC2068](https://github.com/koalaman/shellcheck/wiki/SC2068) twice: for f and for g1. For f it's a false negative since echo will do the same thing either way.


For saving effort, assume that if the code lints it won't trip up on something you haven't thought about, and spend less time trying to think about everything. If you passed on the arguments between functions as `"$@"` in your script, and it lints fine, (1) congratulations you got it, and you didn't confuse it with `$@`, `$*`, or `"$*"`, so you don't have to read this part of my blog post again. If you didn't and got a random error, you fixed it quickly without worrying too much, and then see (1). Your `echo "$@"` is slightly less readable and otherwise equivalent to `echo $@`, so you wrote worse code, but you didn't spend time thinking about it so you saved time.

If you're in the other position, where you need to push quality up, fix the error and warnings, I guarantee that there will be some. For that investment of effort, the quality will go up: and you will have fewer cases where your pipeline worked fine until some header had a space in it.

### Coda (15 April 2018) 
I would like to add a nice ending, with a historical perspective. I've written most of this when I was working on the Atlas pipeline. I moved stuff around, installed bats, wrote some tests, I also installed shellcheck and [integrated it with Test Anything Protocol](https://github.com/wbazant/shellcheck-tap-integration). Writing the tests pushed the project in a good direction- it created units of code that were okay - but at the time nobody needed to change that code so what's the point. Shellcheck told our bioinformatician about all the potential problems his code could have but actually didn't- because he ran it, and it was fine, so what was Shellcheck on about. Before I left I changed the pipeline to only alert on linter errors, and made it send everyone an email. 

I feel I did a more significant job when fixing bugs and polishing individual scripts, like, that R script which didn't work for TSV files with one column because R read it in as a vector instead of a matrix, or that Perl script which called [Statistics::Descriptive](http://search.cpan.org/~shlomif/Statistics-Descriptive-3.0612/lib/Statistics/Descriptive.pm) to calculate percentiles on an small array for each 50.000 x 100 matrix entry, and using libraries to achieve common tasks is desirable except this one helpfully precalculated a bunch of other statistics users might also want- I found out it takes long under a profiler, went to read the package code, and saw it's completely not right for how it was used: carving it up and including just the bits needed gave our script a nice speedup. 

I've since learned about another use case of BATS- in devops, as part of `ansible test`. In other words, your deployment script has just done a bunch of stuff, write some assertions about what it did, and if they're about files being present and programs succeeding Bash is the right language to do it in, and BATS provides convenient utilities for this.
