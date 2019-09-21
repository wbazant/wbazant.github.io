---
date: 2018-09-26
tags:
  - perl
  - java
  - programming
title: How to use fewer variables
---

Using variables may benefit program readability, but many programmers regard it as a bit of a weak move ([source: Yegor Bugayenko- programmer, blogger, and entrepreneur](https://www.yegor256.com/2015/09/01/redundant-variables-are-evil.html)). Giving stuff names is emphasised in beginner tutorials, and I expect many of my programming readers use variables a little bit too much, perhaps not realising it is merely a programming technique. I will explain the drawbacks of this technique, and give some alternatives, so that by eschewing it you might improve your craft further.

---

## The drawbacks
Yegor explains the drawbacks well enough in the link above. I have just one thing to add, and I would like to achieve it by means of example. Consider this snippet from [the main class in a FizzBuzz project](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition/blob/master/src/main/java/com/seriouscompany/business/java/fizzbuzz/packagenamingpackage/impl/Main.java), meant as a keystone for enterprise-grade coding practices:

	final ApplicationContext context = new ClassPathXmlApplicationContext(Constants.SPRING_XML);
	final FizzBuzz myFizzBuzz = (FizzBuzz) context.getBean(Constants.STANDARD_FIZZ_BUZZ);
	final FizzBuzzUpperLimitParameter fizzBuzzUpperLimit = new DefaultFizzBuzzUpperLimitParameter();
	myFizzBuzz.fizzBuzz(fizzBuzzUpperLimit.obtainUpperLimitValue());

I consider such code weak and spineless. This work was clearly written by programmers who could not keep track of what the different objects are, and had to use variables as mental crutches. I am not against crutches, because people with disabilities genuinely need them, but this kind of code puts undue strain on the Java compiler. Advances have been made on this front [since Java 10](https://developer.oracle.com/java/jdk-10-local-variable-type-inference) which gave the language the `var` keyword. Nevertheless even the Java 10 compiler is frequently unable to reliably figure out types for variables such as `context`, `myFizzBuzz`, and `fizzBuzzUpperLimit`, which is why type annotations still need to be provided in Java. 

## How to remedy
### Pretend you're writing Lisp
Fortunately it is not too difficult to remove the variables and improve the snippet above:

        ((FizzBuzz) new ClassPathXmlApplicationContext(Constants.SPRING_XML)
           .getBean(Constants.STANDARD_FIZZ_BUZZ)
        ).fizzBuzz(
           new DefaultFizzBuzzUpperLimitParameter().obtainUpperLimitValue()
        );

You might notice the extra brackets this style requires.

### Use a map/dictionary/hash
Variables are stored in [symbol tables](https://en.wikipedia.org/wiki/Symbol_table), and in dynamic languages like Perl this is [just a hash holding data at runtime](https://perldoc.perl.org/perlmod.html#Symbol-Tables). You can store variables in a `Map<String, Object>` in Java instead of that symbol table, but the language makes it difficult for you, so let's not talk about applying the technique in Java.  I like Perl much more, and I feel it is time to switch to dynamic languages, where the technique is more useful anyway.  

So I am here to tell you today, make your own hash. I frequently do this for parsing command line arguments in scripts, with great success. Consider this tutorial example from the classic [GetOpt](https://perldoc.perl.org/Getopt/Long.html) package:

      use Getopt::Long;
      my $data   = "file.dat";
      my $length = 24;
      my $verbose;
      GetOptions ("length=i" => \$length,    # numeric
                  "file=s"   => \$data,      # string
                  "verbose"  => \$verbose)   # flag
      or die("Error in command line arguments\n");

The suggested rewrite is:

      use Getopt::Long;
      my %args;
      GetOptions ("length=i" => \$args{length},    # numeric
                  "file=s"   => \$args{file},      # string
                  "verbose"  => \$args{verbose})   # flag
      or die("Error in command line arguments\n");
      $args{length} //= 24;
      $args{file} //= "file.dat";

Now you can pass the whole hash around, and it is clear later in the program that `$args{file}` is coming from a command line argument.

### Replace with function calls
When you need to give an intermediate result a name, consider extracting a function/subroutine and naming that.

### Use functional programming
I used to be fond of higher order functions like `map` and `filter` as alternatives to `for` loops as early as 2012 during a year three of my uni degree, when I took an introductory programming course taught in Haskell, and decided who the adult me is gonna be. It was before the Javascript community made "functional" mean "`map` and `filter` instead of for loops", and later to just mean "good". I am not so keen on this any more: recently I replaced a suspect `map` with a for loop in my Perl code, it seems to have fixed a bug I had, and I'm not sure what was wrong in the first place.

### Use default variable names
This is `$_` in Perl.

## Conclusion
Programming is more of an art than a science, as useful as it is to STEM disciplines - and art is a dialogue, where historic context and conversational narrative gives meaning and depth to otherwise [simple works](https://en.wikipedia.org/wiki/Black_Square_(painting)). When you take over an ongoing project, it is your turn to make your mark on it. Rethinking how the team uses variables is relatively simple to undertake, and can be very beneficial. If your colleagues protest or aren't quite sure how to program with fewer variables, please do refer them to this blog post. 
