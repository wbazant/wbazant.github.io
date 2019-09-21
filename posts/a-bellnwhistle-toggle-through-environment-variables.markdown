---
date: 2018-04-25
status: published
tags:
  - bash
  - programming
title: A bell'n'whistle toggle through environment variables
---

The usual way to control a script's behaviour is to provide a config file or command line arguments at startup. Actually, it's also common to modify the script's code, typically after executing it and noticing it's not doing the right thing. Neither of these are very convenient when you are including the script somewhere else. This post is about the third way, its curious advantages, and how it's usually epically bad but sometimes it isn't.

---
Say you have a script like this:

    do_op
    do_infuriatingly_slow_checks
    do_final_op

Suppose you've decided to go fast and loose, and you wanna skip the checks. A lesser programmer would comment them out, run the program, and then comment them back in, but this situation just asks for an environment variable:

    do_op
    [ "$SKIP_INFURIATINGLY_SLOW_CHECKS" ] || do_infuriatingly_slow_checks
    do_final_op

Now you've given your program an additional hidden switch. A couple of notes about this:
- I suggest the name of the variable to be long and idiosyncratic, e.g. `$DEBUG` might affect something you don't expect, especially if you get into the habit of using this pattern a lot
- you want the variable to be transient: if you try to set it in scripts, or programs rely on it, it's probably better to go back and think about your software engineering practices
- you can set it as you call the script, as follows: `$ SKIP_INFURIATINGLY_SLOW_CHECKS=1 ./run.sh`, and then you don't have to remember whether it got exported or not

It is common for environmental variables to store secrets and config, and [the 12 factor app](https://12factor.net/) suggests they should come from there. It is also common to use something similar to enable testing, although it could be a sign that dependency injection was not done right. Maybe on `$TEST_ONLY` the script rollbacks, rather than commits, a database transaction.  Using env vars for controlling program behaviour is not something I have really seen until I started programming in Perl.

I saw the technique in [JSON-Validator](http://github.com/jhthorsen/json-validator): a variable `$JSON_VALIDATOR_DEBUG` turns on logging about what is being parsed. It was consistently acted upon in the validator, and was a frequently useful and easily controllable effect. It has helped me a lot when I couldn't locate a validation error, and the validator was used a by my program within a program etc. it wouldn't have been similarly easy if the author chose to let users control this behaviour by a `--verbose` flag.

What if you have such a situation, but you'd rather not add an extra-environmental-variable-bell-n-whistle-switch? I think the right alternative for whenever you want to `[ "$SKIP_INFURIATINGLY_SLOW_CHECKS" ]` in your script is not the `--go-fast-and-loose` flag but simply commenting the checks out. Or deleting the checks once and for all, but you want to skip the infuriatingly slow checks now, and do them another time, it is foreseeable that another time you will want to skip them again.

Since I almost suggested it as a solution, I want to emphatically stress that commenting out code has big drawbacks. From an esthetic point of view, you're making a mess, and if you don't put the comment above the comment, you won't know what you left there and why. Are these infuriatingly slow checks good to put back in? They finished quite fast, are they still good?

Anyway, what started as a demonstration of a simple pattern for work-in-progress scripts has invited consideration of future changes, functionality, and usage. What a neat case study in software engineering!
