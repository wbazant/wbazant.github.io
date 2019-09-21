---
title: How to parse XML with regular expressions
tags:
  - bioinformatics
  - perl
  - programming
date: 2018-04-26

---

There is a belief, especially popular under university graduates with heads full of Noam Chomsky, [in the inherent hierarchy of grammars](https://en.wikipedia.org/wiki/Chomsky_hierarchy), and there are people out there who think it's something that has to be followed. [Obey the hierarchy, they tell you](https://stackoverflow.com/a/1732454). I suggest you hear them out, learn what is there to be learned, and then reply, I didn't know there were any rules about this. Dear reader, you are my fellow human and I want you to be strong and empowered. I'm gonna show you how to parse XML with regular expressions, and then you can make your own choices.

---

Here's an example XML, and a program I wrote for work. I wanted to have a list of all assemblies published for taxon 6231 (flatworms). I went to NCBI's website where they live, opened the Network tab, and saw this is loaded through AJAX:

    https://www.ncbi.nlm.nih.gov/assembly/organism/browse-organism/6231/all/?p$l=Ajax&page=1&pageSize=1000&sortCol=4&sortDir=-1

It's clearly populated dynamically by a program, as an unofficial API - it wasn't meant for me, it looks like it's meant to fill a HTML page, and it's not actually valid XML. It's a [text soup](http://ergoemacs.org/emacs/elisp_text-soup_automation.html), but it does have structure:

    crap
    <td>{name I want}...</td>
    ...
      <a href="{oh yes I want that link}"...>{this looks useful too}</a>
    more crap
    crap everywhere
    same crappy stuff all over again

I want to filter the file to get just the bits I want. `grep -B2` produces:  

	<td>Caenorhabditis remanei (nematodes)</td>
	  <td>
		<a href="https://www.ncbi.nlm.nih.gov/assembly/GCA_001643735.2/">ASM164373v2</a>
	--
	  <td>Setaria digitata (nematodes)</td>
	  <td>
		<a href="https://www.ncbi.nlm.nih.gov/assembly/GCA_900083525.1/">Sdigitata</a>
	--

Better: a regularly repeating unit of stuff, although with crap around it. Distinct, repeated craplets. The `-A` and `-B` flags to `grep` mean lines after and lines before, and get the tool to produce entries like that, with `--` separating the units.

Now it's time to get on your programming horse. I tried many breeds, but none were as high as I would have liked them, so I have settled on the camel &#128043;. I will post the program first:

	perl -e '$/="--"; while(<>){ 
		/<td>(.*?) \(/ ;
		print "$1\t"; 
		/<a href="(.*?)">(.*?)</; 
		print "$1\t$2\n"; 
	}'



It's delightful, because it uses so many different features despite being relatively short: `-e` executes a program, and `while(<>)` reads input line by line. Before the loop, I'm first redefining `$/` to `"--"`, which tells perl that when it's reading files, the lines are meant to be separated by two dashes.  We want that, because this is how we got our records from `grep -B`. 

Then you'll notice the loop is just `while(<>)`. In Perl, when we don't mention the name of the variable, that name is `$_`. We don't refer to this variable in the program, but the next few bits know to use it: `/<td>(.*?) \(/ ;` is a regex match, over - also - `$_` the default variable. The brackets mean [capturing groups](https://en.wiktionary.org/wiki/capturing_group), which is by the way how you can swap two columns with the programmer's regular expressions, a task proven impossible with the academic's regular expressions.

The capturing groups, when they match, get stored for us in global variables `$1`, `$2`, etc.

The program recognises `$1` with 'the bit after `<td>` and before space', then prints it with a tab. Then it matches again as `$1` and `$2` with another regex 'the bit in quotes before `href`' and 'the bit between `>` and `<`'. It prints these two, and a newline character.

The end program, ready to go into your pipe, is

    curl -s 'https://www.ncbi.nlm.nih.gov/assembly/organism/browse-organism/6231/all/?p$l=Ajax&page=1&pageSize=1000&sortCol=4&sortDir=-1' | grep -B 2 href | perl -e '$/="--"; while(<>){ /<td>(.*?) \(/ ; print "$1\t"; /<a href="(.*?)">(.*?)</; print "$1\t$2\n"; }' | sort

My last comment on this is that you can solve the problem of making a list of assemblies in any other programming language. For example Python has BeautifulSoup, a module for scraping webpages. You can also try your luck and search for "retrieve Platyhelminthes assembly list tsv", which, one day, might yield this very blog post.

That's all. I am sorry if you didn't like it. Maybe you even thought it was literally the worst? I will be curious to hear your comments! 

