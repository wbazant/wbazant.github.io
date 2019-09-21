---
title: Upgrade from object-oriented to functional programming with this weird trick
tags:
  - perl
  - programming
date: 2018-07-10

---
Picasso was a great man, but fond of exaggeration, so I always take it with a pinch of salt when people say it took him a lifetime to learn to draw like a child. As you might have guessed already, this post will be about me, and how I write Perl, and tracing the influence of my early periods of being sold on Haskell at university, training myself to appear worthy at interviews, and subsequently becomming a maintenance Java programmer.

---

Actually I want to discuss [typical programming with objects](https://docs.oracle.com/javase/tutorial/java/concepts/class.html). The following style is available to you in Perl:

    my $bike = new Bicycle;
    $bike->speed_up;

[This Modern Perl chapter from 2009 criticises the style for not being modern](http://modernperlbooks.com/mt/2009/08/the-problems-with-indirect-object-notation.html), but you probably won't listen if you're trying to make your Perl look more like Java. I won't criticise, I'll stand by and cheer you on.

You can also pretend you're writing Lisp, and it doesn't even require that many parentheses, I merely mean writing programs [based on expressions](https://en.wikipedia.org/wiki/S-expression). Delete all the variable declarations you can, and you're mostly there.

I want to explain the alternative more concretely with [LWP::UserAgent](https://metacpan.org/pod/LWP::UserAgent) as an example. The tutorial does essentially this:

    my $ua = LWP::UserAgent->new;
    my $response = $ua->get('http://search.cpan.org/');
    if ($response->is_success) {
        print $response->decoded_content;
    }
   
It also has a few options that you may or may not need, like, handling the error case.

There's a time and a place for being responsible, but sometimes you just want a program that works. Then you can do this:

    print LWP::UserAgent->new->get('http://search.cpan.org/')->decoded_content;

Here we go. We have deconstructed getting webpages and printing the decoded content, and it is worth to ponder if anything got lost in the process. The UserAgent used to be there in some sense: the symbol table would hold an object representing your friendly bro that's fetching you pages, and you could tell him about timeouts or proxies if you wanted to - and the Responses he return would make you feel like you really got a response from that HTTP server. Then everything that wasn't the essential function of the program was stripped away, leaving behind a razor sharp expression of intent. 

I originally wanted to claim the first example is object-oriented and the second is functional programming, but LOL I don't even know what these words mean any more, and I think the fundamental difference is what the two styles of programming are later good for.

The first snippet is good for extending: telling the UserAgent about extra options or doing something appropriate when the response wasn't successful. It's good when you're going to fetch webpages in baroque and special ways.

The second snippet isn't at all good for extending- unless [the module authors made all the subroutines return the same thing](https://en.wikipedia.org/wiki/Fluent_interface) - but it's good for integrating somewhere else. Printing the webpage with everything uppercase is as simple as
   
    print uc LWP::UserAgent->new->get('http://search.cpan.org/')->decoded_content;

It's good if you don't care about extra options and you just need the job done. You can go so fast there will be [flies on the windscreen](https://www.youtube.com/watch?v=KaJLuPMeokI).

I like the second style more. It lets me spend less time on programming, so that I can do more blogging. But this is already the end. Till the next time!
