---
draft: !!perl/scalar:JSON::PP::Boolean 0
status: private
title: Great programmers steal
---

The system teaches us to work hard and obey, since we were sent to school as kids. My reader, who wanders into programming blogs, it might be that throughout your education you've done well through attending classes, completing assignments, and studying for tests. I did, I loved that stuff, when I worked hard it made me feel like I was really getting somewhere. 

Dear reader, I want you to be efficient and focused on your goals, and today I am going to teach you how to use UNIX to shirk your responsibilities as a programmer. Write minimal amounts of code, get all the work done to a vaguely acceptable standard, go home at four, and laugh at your TDD-applying colleagues.

---
## The case for reusing
Your program ain't gonna write itself, but if it's not you who wrote most of it, I believe it's almost as good. Please give me a chance to convince you on this - I am going to explain how to copy from [right](https://en.wikipedia.org/wiki/Copyright) and [left](https://en.wikipedia.org/wiki/Copyleft), but first I need to endow you with an appropriate moral attitude.

### Other people's code: the good parts
Intellectual property is an unusual kind of owning: knowing what other people have would be as good as having it yourself, if it wasn't for the system opressing you. You should obey and anyway a much better reason to not reuse stuff that wasn't created to be reusable is the fact you might reuse something wrong.

So if you need to add functionality, and use bits of people's code, it can be wrong, and when everything fails you will have all that stuff to read. If you actually write your own code, it's even worse, because then you also have to type and think whether what you have typed is right.

I'd say you're better off pinching entire programs. They can also be wrong, because even the classic ones like `grep` are [full of bugs](https://debbugs.gnu.org/cgi/pkgreport.cgi?package=grep), so once everything seems to work you should probably stop changing stuff and call it a day.

### Advanced state of human technology
As you have seen if you went to that link `grep` is full of bugs, but on the other hand it has also worked well lots of times, thanks to people from the Internet who fixed stuff in it and made it how they wanted it to be. Your program is not gonna get anywhere this much attention, not the least because you're now reading a blog post advocating going home at four.

### How to reuse
This is a [hexayurt](https://en.wikipedia.org/wiki/Hexayurt), a perfectly adequate housing option made from tarp and cardboard glued together with duct tape:

<a title="By Vinay Gupta?; original uploader was Ray Van De Walker at en.wikipedia [Public domain], via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File:All_hexayurts_web_dimensions.png"><img width="512" alt="All hexayurts web dimensions" src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dc/All_hexayurts_web_dimensions.png/512px-All_hexayurts_web_dimensions.png"></a>

To reuse is to make a hexayurt. Write a bigger program only after you have shown by demonstration that it's not gonna be enough to put tape between pieces of tarp and cardboard. I hope I sound inspiring here, so on that note, you can make a hexayurt more durable by using nuts and bolts for joins, and you can put a nice carpet in there. I will talk about this later - under [Extra functionality](./#extra-functionality), but first I need to explain how to make [components that fit together](./#reusable-components) and what the metaphorical [duct tape](./#data-interchange) can be.

##<a name="reusable-components"> </a> Reusable components

There's a lot of components out there, some of them [with fancy names and written in Java](https://projects.apache.org/), just like there's a lot of building materials to choose from when you roll into your local Wickes / B&Q / Home Depot / Leroy-Merlin. It might be good to consider in the abstract what you might need.

If you need to keep track of changes to information, you have all that database stuff people came up with, and then your program will be a way to transition between states of that database. 

Maybe the run of your program needs to be orchestrated, and reliably available, or be part of an always buzzing swarm, ready to act as a single unit.

At the very least, if your program needs to be told things from outside, it needs to be hooked up to a webserver, or, if it doesn't need to reply, a queue.

It's interesting to meditate about good uses of databases that aren't about "keeping track of changes to information", programs that hang in there compared to ones that don't, etc. Maybe one day I'll toast up a really abstract essay about these. Today I want to talk analytical but concrete, so here's what programs do, based on how they are run. It's a simplified list, the full one is [here](https://en.wikipedia.org/wiki/System_call#Categories_of_system_calls).

### State
Programs have temporary memory, and store information for good by editing files on disk. That's what databases do, although sometimes they also:
- index files for random access
- cleverly partition data
- sync up with other databases

### Hanging in there
Running programs can start and kill other programs. There are ways of orchestrating this across machines, or to reflect some real world situation, but ultimately it's about making programs run:
- at certain times
- as part of bigger operation
- at machine startup
- when failing a status check
- when there is work to do

### Interacting
Communicating with other processes happens by reading and writing to [temporary files](https://en.wikipedia.org/wiki/Named_pipe), or by [asking the kernel to pass messages on](https://en.wikipedia.org/wiki/Signal_(IPC). Programs can by read and write to [sockets](https://en.wikipedia.org/wiki/Unix_domain_socket), and then they can communicate with *outside*.
Humans tell programs what to do through [physical devices](https://en.wikipedia.org/wiki/Device_file), but your program will likely see that through another program passing the message on.

This is all programs do. You can debug an arbitrary program by looking at system calls it does, and [understand it on some level](https://sysdig.com/blog/fascinating-world-linux-system-calls/), but I still haven't explained the yurt. So. Each building needs to preserve a microclimate, and stand firmly, and the hexayurt design achieves this with separate materials that each make the construction satisfy one of the requirements.

Your operating system ships with lots of tools that each do a particular aspect of the above really well. Therefore, if you pick the tools which together are able to do everything your program needs to do, then 

. Your operating system has lots of default tools for all of the above, and people made even more. Maybe a yurt was a bad example, because they all need a component for If you need your program to show people a webpage, maybe you only need [netcat](https://en.wikipedia.org/wiki/Netcat#Making_any_process_a_server) and a tiny script that decides what the webpage says. 
For all of these and more, I recommend that you first try the default solutions preinstalled on every UNIX-based operating system. 
##<a name="data-interchange"> </a> Data interchange

Everything else is changing format of data and plugging these together.
The basic mechanism offered to you for interchange is to connect up the programs emitting and accepting chunks of bytes. Sometimes you want the bytes: Signal example.
Text is nice. Streaming and being per-line is nice.
 
Pick common formats (YAML for data interchange, or text)

---
xxx

## Memory and organisation aids
Knowing what internet is good for, designing for interoperability with great tools, and robustly connecting components go a long way but won't replace commitment and solid work ethic. Remembering what you did recently, what you are about to do, correct typing, and patiently letting a machine do its job are all necessary for basic competence in many desk jobs, most notably, accounting. This is not "Great accountants steal", so I don't suggest you try any harder at any of these, but fortunately there are alternatives.

### Copypasting-enabled wiki
Maintain documentation pages in which every operation you do is a command, such that you have to only copy and paste it from a terminal. If you don't always execute exactly the same thing, you can use variables, and your command can check it got given the variables. Then you don't even need to read what you're pasting!

### Scheduled execution
Type in five long commands in five different terminals, stagger execution with `sleep 1000`, `sleep 2000`, etc., send them off, and go home. If you log in from home to check whether you made no syntax errors, you lose a work-life balance point.

### Topic or work item groups
Give names to things you're working on, and switch between through a terminal multiplexer like `screen` or `tmux`. Then you don't have to take notes, you just go back to the terminal you used, unless the server that ran `screen` got rebooted in which case you're out of luck.
