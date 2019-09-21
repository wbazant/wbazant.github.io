---
status: published
tags:
  - programming
title: Customise your Internet
date: 2018-08-08

---

It's quite hard to have your own piece of internet these days. Blogging isn't easy, even if you don't have many readers. Sites ran by companies don't let you have your own piece, and they're made less usable by attempts to track the movement of your eyeballs and the like. My reader, I know you are yearning for something different. This post will teach you how to make bits of the modern Internet into what you may want them to be.

---

### Change content in the browser
It is an intuitive fact but worth reflecting on: after a server sends you a webpage, it's yours, so if you don't like what it says you can just make it say something else.

[This](https://twitter.com/realdonaldtrump/status/818419002548568064) is a post from a social media account of an American politician. It's something he wanted to say to people, entirely within what he and the site thinks are acceptable speech. You can reply to the post if you feel your opinion will add to the discourse about whatever this was a discourse about, but I advise against it. If you'd rather the site said something else, you can just change the content using "Inspect Element", and "Edit as HTML" options of a modern browser:
<img src="/static/trump-before.png"/>

Great! Now the site says something else:
<img src="/static/trump-after.png"/> 

Everyone else's copies of this site will not be changed by this operation. You could take a screenshot of your version, and show it to people, but please don't make them confuse it for an original, there have been enough lies like this elready.

Unfortunately when you refresh the page, your customisation will also be gone. We will attempt to address this now.
 
### Serve your own version of the site
#### Download-and-edit
The social media site we have modified in the previous example is served from somebody else's computer. Your computer can serve sites too. Very empowering!

You can download somebody's site, change it, and serve it from somewhere else. I've used this technique at work e.g. [here](https://wbazant.github.io/expression-atlas-mockups/transcripts/E-MTAB-4484.htm). I saved a site I wanted to change, added a feature I wanted the site to have, and added comments to it, by hand-editing the HTML code.

#### Use with an iframe 
This way of customising Internet is mostly for other people that visit your version of the Internet by accident. Dear reader, I trust you have good intentions, so I'll tell you how to do this. 

Put up a site on static hosting you control that is merely a shell for a full-length iframe, together with some Javascript code for making the page as you want it to be. [Some sites ask not to be iframed](https://stackoverflow.com/questions/6663244/cant-show-some-websites-in-iframe-tag) because a malicious actor with intentions worse than yours could put a usually trustworthy site inside a snoopy or password-stealing site.

#### Proxy
If you want a more dynamic, "continuous" solution that doesn't involve saving a site to a folder, I recommend a proxy server, perhaps [nginx](https://www.nginx.com/). 

I did this recently to try improve a subcomponent in [the site with worm genomes I look after data for](http://parasite.wormbase.org). I hosted the alternative version from my computer, and delegated everything else, with [an nginx config](https://github.com/GMOD/jbrowse/issues/1150#issuecomment-409621964). It was a good day at work: [I improved something](https://github.com/GMOD/jbrowse/pull/1148) and the development process I have used has made me very enthusiastic about customising my Internet.

With proxying, the page appears the same no matter how many times you refresh - what an advantage over modifying bits of the page in the browser! Unfortunately you have to use a different address for the customised page: with `http://localhost:8080` instead of `http://parasite.wormbase.org` at the beginning.
 
#### Proxy but with URLs staying the same
`parasite.wormbase.org` is a DNS record, referring to a computer somewhere out there. `dig parasite.wormbase.org` will consult some good people and tell you the IP address of this computer is `193.62.193.83`. Your own address is `127.0.0.1`, so if you somehow get one to be the other inside your computer...

Say you make the DNS record `parasite.wormbase.org` resolve to `127.0.0.1`, and then run a proxy which listens to the requests on port 80, changes small pieces, and forwards them to `193.62.193.83:80`. Then you can do all kinds of things!

#### Proxy that Twitter page
So could we do the same with [that Twitter page](https://twitter.com/realdonaldtrump/status/818419002548568064)? Twitter serves the site over HTTPS, so they try not let you. Feel free to conduct a search on "https man in the middle attack" and see if you can do something about Twitter's security. Good luck! 

