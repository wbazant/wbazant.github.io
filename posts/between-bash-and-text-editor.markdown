---
title: Between bash and text editor
tags:
  - bash
  - programming
date: 2018-05-26

---

When you have a long command you need to type correctly, and you only have left, right and backspace to navigate, it can soon feel tiresome. I wouldn't want you to tell all your friends you gave command line a go but it was like some caveman alternative to Microsoft Office products. When typing a command you can move it to a text editor with "hold-ctrl-press-x-then-e", and that's what the post is about.

---

## Setup
Upon "hold-ctrl-press-x-then-e", Bash will try to open in turn `$VISUAL`, `$EDITOR`, and `emacs`, so you could give values to these environment variables in your `~/.bashrc`. This happens to be my setup:

    EDITOR=vi
    VISUAL=$EDITOR

## There and back again
OK so now you can do it. Type something in the command line, "hold-ctrl-press-x-then-e", and you have the same content in a text editor in a temporary file. Save and quit, and the file will get executed.

## From the other side
You can also be in an editor, and get it to execute a command. In `vi`, this is with *:!*. Say:

    :! perl %

will get perl to execute the current file. You don't have to type it in every time, after *:* you can navigate the history with up and down arrows.

## Why it's important
Okay so some program for nerds has this random feature, why should I care, you might say. I use it, but I'm not weird, I don't want to learn these shortcuts, I didn't need them and I got this far. They're not impressive, I can't put them on my resume like my friends do with Microsoft Excel and VBA macros. I am uncertain whether Nietzsche would classify you as [a bird of prey or a lamb](http://www.sparknotes.com/philosophy/genealogyofmorals/section4/), but ponder this quote of his:

#### *“He who has a why can bear almost any how.”*

When you've typed lots of stuff as a command and furiously hold the arrow left key because you need to correct a mistake in the middle of it, you're not thinking about doing it better, because your goal not explicitly the efficiency. I've only learned "hold-ctrl-press-x-then-e" last week, and before that to change something in the middle I would copy half a command, correct it, then copy the other half.

Having ever more reliable and efficient ways of performing tasks will make you, uh, perform those tasks ever more reliably and efficiently. It also lets you do new stuff with your [Bicycle For The Mind](https://www.youtube.com/watch?v=ob_GX50Za6c) because you're not bogged down with tedious stuff any more. So I recommend improving technique of minor things like editing text or executing commands, and so does Nietzsche, in his "Will To Power":

####*“My humanity is a constant self-overcoming.”* 

Perhaps reading programming blogs is not actually the most valuable way to do this. Much better to occasionally recognise a gap in your skill or knowledge and do a quick experiment or search to fill it. Internet can be consumed passively or in explorer mode, the second one is much more fun, and this applies also to procrastination. You'll have enough time to do your real grown up work in due course, and experimenting with little things catching your attention can lead you to good ideas. Both me and Nietzsche recommend doing that. Thus spoke Zarathustra, always one for pissing around with UNIX:

####*“We should consider every day lost on which we have not danced at least once. And we should call every truth false which was not accompanied by at least one laugh.”*

<p><a href="https://commons.wikimedia.org/wiki/File:Nietzsche187a.jpg#/media/File:Nietzsche187a.jpg"><img src="https://upload.wikimedia.org/wikipedia/commons/1/1b/Nietzsche187a.jpg" alt="Nietzsche187a.jpg" width="354" height="480"></a><br>Photography by Friedrich Hartmann (1822-1902) in Basel</p> 

#### Postscriptum and disclaimers
All the Nietzsche quotes here are out of context and literally none of them here fits what he was trying to say. I don't endorse his beliefs, and I particularly disagree with how the Nazis bent them for their propaganda, and I strongly denounce the Nazi movement and its ideology in general.

Spreadsheet software is actually pretty good.

I don't spend all that much of my time at work pissing around with UNIX. I run operations, random acts of software development, ensure correctness of data and I'm responsible for stuff.
