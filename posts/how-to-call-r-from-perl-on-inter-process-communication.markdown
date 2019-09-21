---
status: published
title: 'How to call R from Perl: on inter-process communication'
tags:
  - perl
  - programming
date: 2018-12-09

---
I'm glad the title caught your attention. Why call R from Perl - or have code in language X call code for language Y, for any X and Y, for that matter? Sometimes it's just hard to choose among languages you're fluent in, especially if you're a bit of a polyglot and don't have a clear favourite. Perhaps you value diversity, just like modern companies value diversity in their workforce: different programming languages, just like employees with different backgrounds, offer distinct advantages and strengths that can be drawn from when creating a product.

I happen to have a language I strongly favour, and I consider my programs are already diverse enough, even though they all got written by a single white male. I also managed to have a job in bioinformatics without installing R for quite a while. I am not riding that streak any more because a specific thing I needed to do only exists as an R package, so I learned how to use R from Perl. This merits celebration with a blog post, so I'm gonna tell you how to call R from Perl and also more generally about IPC.

---

## The answer to the original question
Just use a library, [Statistics::R](https://metacpan.org/pod/Statistics::R). The code that prompted the post is [here](https://github.com/WormBase/wbps-expression/blob/master/lib/Production/Analysis/DESeq2.pm), but the interesting part is how Statistics::R works. It starts R - getting the executable from `path` like it should - in a subprocess to which it talks through STDIN and STDOUT. It's easy to get and set scalars and arrays between the languages, though not e.g. R's data frames.

## Ways of IPC
Different ways, and their different complexity, is best explained by means of a meme:

<img src="/static/expanding-brain-ipc.png">

As the brain expands, the ways get more elaborate and stupid. Yes, more stupid - the whole idea of calling languageX from language Y is backwards and outdated, and if you want to keep your skillset modern I advise you avoid IPC and stick to microservices. Microservices are slick and simple, you just need to:

- create separate isolated environments for X and Y: I've heard Docker is good
- have a program that automatically rents machines on the cloud for you: I've heard Terraform is good
- have a program that installs stuff on machines, and prepares your environments there: I've heard Ansible is good
- have a program that launches and runs processes in these environments: I've heard Kubernetes is good
- let your processes talk to each other via HTTP: this requires that they're connected by a network - if you're concerned about security, I've heard that subnets are good

If you do not require a fleet of rented VMs, just pretend you do. Run Kubernetes locally via something called [Minikube](https://kubernetes.io/docs/setup/minikube/).


## Temporary files
An old method of sending messages is through temporary files: a writing party stores them in a pre-agreed place that the recipient periodically checks. This is worth a try in a work setting, when you need to share intermediate results with another group: instead of promising you'll email someone when you're done, tell them you'll leave them a file in a certain place and suggest they set up a cron job.

You can use a similar scheme when co-ordinating two processes. By convention they should use STDIN/STDOUT, but you can also refer to a file system path in both the writer and the reader. This works nicely when the two processes run the same program, like with lock files.

## Run a process, let it finish, capture STDOUT
"Shelling out" is a bit like using temporary files. Some communication happens in it, I can't really decide whether it's more or less advanced than using temporary files. It is accomplished via backticks in Perl:

    # LOL terrible code don't do this
    print for `ls`;

The process with your main program is a <strike>master</strike> leader, asks the OS for a temporary file handle and to start a process with an appropriate follower whose STDOUT will go to that file handle. The follower <strike>does its job</strike> performs their role and goes to rest. 

## Pipes
Shelling out will wait for the worker to finish, but when connected via pipe the worker will be put on hold as you read at your own pace. It's good, I do recommend.

If you start a worker controlling both its STDIN and STDOUT you can talk with it back and forth: this is how Statistics::R works. Twigging this was really helpful in understanding how I should pass data from R - it explained why I could only get stuff in at most one-dimensional data structures, i.e. vectors.

## Shared memory

Ah, yes, the brain is expanding. You don't need pipes - the forty year old mechanism to orchestrate IPC by stopping your processes, batching the input and more - because you can do better. Here's your playground - give your processes some [shared memory](https://en.wikipedia.org/wiki/Mmap) and make them read and write to the same area. If you wanna learn how to do that, you need to read some other blog because I have no idea. 

What I do know is that reading arbitrary memory contents is well-supported by Perl, through [packing](http://perldoc.perl.org/functions/pack.html) and [unpacking](http://perldoc.perl.org/functions/unpack.html) bytes, which could be an array of C structs laid out one after another. There's a little domain specific language for packing - if you ever need to format something to be 80 characters wide, do find out about it.

## Recommendations
Just do what you need to do. If you end up with Python calling Bash calling R calling Java, and the whole thing still works, it is a sign of high DevOps skill, and will be helpful when you try get a promotion to a Senior DevOps. Sketch some diagrams, call your program a pipeline, and then put it in a Docker container. I've heard Kubernetes is good for managing these.
