---
title: Extra functionality through pipes
tags:
  - bash
  - perl
  - programming
date: 2018-05-19

---

Sometimes the programs you are running aren't as fully featured as you need them to be. If it's your program, extend it to do work like you want it, the ad-hoc extensions will make it soon feel like a warm worn-in shoe. If it's not your program, and the missing feature is correctness, just fix your copy, and submit the fix to the author. Otherwise add the extra functionality outside, with an appropriate contraption. Here's a couple from me, based around pipes. 

---

### Early termination
You could make programs that don't encounter errors, or ones that know when to continue against adversity and when to stop, but you don't always have to. When you type in a command, and it's doing a wrong thing, you send it a signal to kill it, right? Well, here's a program that recognises that the previous program is doing a wrong thing, and sends it a SIGPIPE.

    for i in pass_1 pass_2 fail pass_3; do 
      echo $i
      echo "Process 1 said:" $i >&2
      sleep 1
    done | perl -e 'while(<>) { 
      print "Process 2 saw: " , $_; /fail/ and exit; 
    }'
    Process 1 said: pass_1
    Process 2 saw: pass_1
    Process 2 saw: pass_2
    Process 1 said: pass_2
    Process 2 saw: fail
    Process 1 said: fail
    # no more output

### Nicely formatted log message
`while read -r input_line; do ... echo ... ` is good for decorating output, so is a `perl -ne` program decorating the lines.  

    while true; do sleep 1; echo "Still chugging along"; done \
      | while read -r l ; do echo `date` $l ; done
    Tue 8 May 2018 23:28:30 BST Still chugging along
    Tue 8 May 2018 23:28:31 BST Still chugging along

### Read it again later
Sometimes I check up on my job, see the ending credits have already rolled in, and I scroll up but it doesn't take me all the way to the top, and I've lost the logs. Then I wish I had used `tee`, UNIX's bookmark feature, that splits a stream in two.
As a random example of a chatty process, let's take this number generator.

    get_numbers() {
       LC_ALL=C 
       < /dev/urandom tr -dc '_[:digit:]' \\
         | tr _ "\n" | grep -v "^$"
    }

We can then pipe the whole output to a file somewhere else, and pass through only the really important output. Be careful with the next example! It can make your machine hum ominously, then heat up, and finally either sacrifice its CPU, disk, or print [The Number of the Beast](https://www.youtube.com/watch?v=WxnN05vOuSM) and exit. 

    get_numbers | tee ~/get_numbers.out | grep "^666$" | head -n1

For a more practical example, you could limit the output of an otherwise insufferable job:

    java -jar Jar.jar | tee ~/binks.out | grep "Info\|Warn\|Error"

### Progress bar
[monitor the progress of data through a pipe](https://linux.die.net/man/1/pv)
`pv` is the best at telling you how fast the data is flowing through. You quickly find out performance of your write-only storage: 

    yes | pv > /dev/null
    269MiB 0:00:08 [33.1MiB/s] [                 <=>                                                                                         ]

Can your Hadoop cluster ingest over 30 MB of data per second? My laptop can!

Much more usefully, if you put `pv` at the *beginning* of a chain of pipes, and subsequent processing steps take a while, and they're all stream processing operations (nothing reads in all of data at once), you can monitor an entire pipeline. I have an [example](https://github.com/wbazant/wormbase-pipeline/commit/c5b6c9bd943b28dc294210fbf4ec69fdeb3e21aa#diff-06cf752add61092dd50356d96228220bR37) from work - I was getting impatient at my code running too long, I added `pv` at the beginning, and I could see it's going to take e.g. 25 more minutes to complete, kill the process, reorder some stuff / add an extra `grep` / change `grep` to `grep -F`, literal match / and see if the expected running time goes down. 
#### Progress bar addendum: a puzzle
The above might seem like magic if you don't know how processes connected by pipes are orchestrated. Say we connect `yes`, which produces enthusiastic output as fast as it can, with something that can't process it quite as fast:

    yes | while read -r l ; do sleep 1; echo `date` $l ; done

So if the first program produces megabytes of yes'es per second, and the second one can process only one per second, where do all the extra yes'es get stored?
