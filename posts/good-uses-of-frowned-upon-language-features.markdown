---
title: Good uses of frowned-upon language features
tags:
  - perl
  - programming
date: 2019-05-19

---

Not every language feature is respected these days. I speculate why it might be, and demonstrate a few mind-expanding programming techniques.

---

The job of a software engineer is hard, and it is frequently meaningless and devoid of any real control over software created. Instead, members of the profession receive tiny fiefdoms and clash with each other, most commonly during mandated code reviews. The confrontations are kept polite, and sides arm themselves into best practices, style guides, and complicated frameworks of rules. Some try to rescue some meaning in their jobs by actually trying to apply these rules: this is known as the ethos of [craftsmanship](http://manifesto.softwarecraftsmanship.org/) and I don't think of it very highly.


<img src="https://uploads8.wikiart.org/images/jean-baptiste-simeon-chardin/boy-with-a-top.jpg!Portrait.jpg">

Dear reader, if you consider yourself a bit of a craftsman, please don't be offended - I must say that once I was one, too. After university, I was working as a Technology Associate for a corporation, and I passed my days rewriting classes, adding tests, and applying my evolving judgement. I've learned a lot, invented some really good module names, and if you are at a similar stage, I recommend craftsmanship to you too. You'll gain skills that will come in handy when you set out to make some software that actually matters.

If you are just past that - well, you need a new approach, and you might also need some new things to read. There's a wealth of literature based around telling people what to do, what the best practices are, and especially what not do to because you will presumably regret later, but relatively little contrarian advice about worst practices. I aspire to fill in that gap, and help you write good quality code using language features that would are commonly known to be the worst.

---
## `goto`

The Perl `goto` statement corresponds to something like the `JMP` x86 instruction, and it's the archetypical feature a craftsman would avoid. Indeed the original diss of GOTO by E. Dijkstra, "[Go To statement considered harmful](https://en.wikipedia.org/wiki/Goto#Criticism)" has inspired considering many other things harmful, and provided a template for how to criticise language features.

### Temporary `goto if $ENV{...}` as alternative to commenting out code sections

I endeavour to keep my scripts useful the whole time, mostly to avoid consequences of forgetting to fix them afterwards. but frequently I want to do a partial run or leave something out. This is easily accomplished by a quick `goto`:

	...
	goto MERGE if $ENV{DO_MERGE_ONLY};
	... # this will be skipped
	MERGE:
	...

If you forget about it, nothing bad happens, unless you have also accidentally exported `DO_MERGE_ONLY` and reused that shell session.

### Implementing flowcharts

<img src="https://imgs.xkcd.com/comics/flow_charts.png">
([source](https://xkcd.com/518/))

Structuring programs using conditionals and function calls is typically superior, but sometimes an algorithm really is described best as a labelled list of steps containing `goto` statements. I've just remembered I wrote such code a few months ago ([link here](https://github.com/WormBase/wbps-expression/blob/03389b717a090721d9a610976c7bb28c775e707b/lib/WbpsExpression/Analysis/DESeq2.pm#L75)). It's no longer as clear as it used to be, but it still makes a lot of sense.

### Making a cool JAPH
Reading Perl is a lot about feeling the intent. If you see code formatted into a square block and without any words in it, it can only ever print "Just another Perl hacker", like this beauty based around `goto` on computed labels:

	$l="l10";sub l{print;goto$l++}l;l35:exit;l34:;$_="\n";l;l15:;l29:$_="a";l;l14:;
	l22:;l27:$_=" ";l;l30:;$_="c";l;l20:;l24:;l32:;$_="e";l;l10:;$_="J";l;l19:;l28:
	;$_="h";l;l31:;$_="k";l;l26:;$_="l";l;l16:;$_="n";l;l17:;$_="o";l;l23:;$_="P";l
	;l21:;l25:;l33:;$_="r";l;l12:;$_="s";l;l13:;l18:$_= "t";l;l11:;$_="u";l;die;die

Credit to Abigail ([source](https://perl-blog.abigail.be/post/9347752138/a-new-japh)).

## Unreadable code
This is not quite a language feature, but more of a general thing.

### Protecting the innocent
Sometimes it's good to make parts of your code unreadable if you plan to put it online. It is especially advised if you feel responsibility towards its future readers, some which could be terrified to find out how your code works. Fortunately they can be weeded out by raising the readability bar just high enough that they won't bother to put in the effort.

### Hiding profanity
`Regexp::Common::profanity` can be used to text match profane words, which is particularly useful for user input. It consists of, essentially, a regexp that matches the bad words, cleansed with a letter-rotating regex so that nobody has an unpleasant experience of reading it.

	my $profanity = ...;
	my $contextual = ...;
	tr/A-Za-z/N-ZA-Mn-za-m/ foreach $profanity, $contextual;

Credit also to Abigail - taken from module code: [link](https://metacpan.org/release/Regexp-Common/source/lib/Regexp/Common/profanity.pm).

## String `eval`

### A cool calculator
String `eval` essentially runs the interpreter for you. You can use it to create a mini-language that is executable through an interpreter. For example, this has once helped me calculate relative sizes of numbers:

	relative_change(){ perl -E '
	  my ($from, $to) = @ARGV;
	  say sprintf("%.3f", eval "(($to) -($from))/($from)" ) 
	' "$@"  ; }

### Extra regex feature
Once I wanted to refer to the replacement part of a regular expression elsewhere in the program. 
Calling `s/$match/$replacement/` would have evaluated the replacement and I didn't want that, but `eval "\$word =~ s/$match/$replacement/"` was just the ticket.

## Discover more
<img src="https://uploads4.wikiart.org/images/karl-edvard-diriks/from-christiania-harbour-1912.jpg!Portrait.jpg">

[This stackoverflow topic](https://stackoverflow.com/questions/161872/hidden-features-of-perl#162094) is pretty good, and contains many wonderful features. There's a culture of coming up with them, and at a slightly higher level a culture of creating modules that do something unique. I would treat it for what it is - art. A spaceship operator or an Eskimo greeting is not meant as a piece of working software, even when it is presented as such - it can be, but that's not what it's for. Better to think of these as manifestations of people's creative expression and what they've brought back from travelling to the edge of the possible.

For creating software that matters, flocking towards random bits of cleverness is just as unhelpful as following strict rules. You'll know what you need when you need it, and if you've been fortunate enough to choose a good programming language there will almost surely be a way for you to achieve it.
