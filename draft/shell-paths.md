---
title: Robustly use paths in shell scripts
date: 2014-01-01
status: draft
tags:
 - bash
 - programming
---

If you can use command line it's not a big leap to then also be able to write a shell script. It's a source of great joy to have a script that does exactly what you need done, and it's perfectly acceptable for one-off scripts to be a collection of commands that would otherwise be executed in turn. There are a few qualitative differences between such a script and a fairly complex pipeline that needs to be maintained over years, and one of them is how they use paths and what they assume about the state of the file system. Great now I have a segue into what I wanted to talk about.

---
### Shell programming is programming
The knowledge you might have gained working in a more socially acceptable programming language still applies and will help you out. Conversely what you have learned is a bad idea is still frequently a bad idea - examples will follow. :)

### Minimise assumptions about `pwd`
Expression Atlas pipelines did this kind of thing a lot:
```
pushd $target_dir
  for f in $(ls); do
    tweak_my_file $f
  done
popd
```
With a `for` loop it's half as bad because you can't copy and paste the middle bit of that for loop, you don't have individual access to `$f` anyway. But if you were to move the whole block, it would no longer work, right? It gets worse and worse the longer the pushd/popd block becomes, and it's another level of worse if the script just relies on being in the right directory. Relying on `pwd` can be subtle, e.g. when a script is referring to a file by its name and not by path : `tweak_my_experiment_file --file $experiment_id.xml` . You'll call this script from somewhere else, and it'll not work.

### Use absolute and relative paths appropriately
Absolute paths are good for stuff that is not part of the same software project. This could be a location of your FTP directory, or resources you don't control- say, another group's source code- and are likely to stay where you say they are. When you're referring to another script, use a relative path from your script. This is a prerequisite for your script being "deployed" - you write it somewhere, and send over to a place that can differ - but also I find it neater. 

### Use variables for roots of places
Across your project, it's nice when you define places you care about through environment variables that you export or `module load`. But also within a script it's easier to make sense of the hierarchy when you give roots meaningful names. The programming principle I'd like to quote is Don't Repeat Yourself, as an example of a point I have made here earlier.

### Paths may or may not refer to files
Each time your script refers from a path, it makes assumptions about the file system, for example writing to a file in a nonexistent directory isn't gonna work. Handle it judiciously - by checking status codes or a strategic `mkdir -p`, up to taste - but if you're trying to make the script bulletproof you're using a wrong tool.n a language with a type system, whether it's "read from here", "list this dir", "write over there", and the file system might not reflectn particular writing to a file in a nonexistent directory isn't gonna work. 

---


### APPENDIX A: Figure out links
The manual says this:
```
NAME
     link, ln -- make links

SYNOPSIS
     ln [-Ffhinsv] source_file [target_file]
```
I find this too fine for my liking and the words source and target always confuse me. In the notation of `ln -v` (verbose), the above shows as
`target_file -> source_file`: the arrow is going away from the manual calls the target, and if you too object to this terminology, please help yourself to an alternative:

```
ln -s <referent> <pointer> 
ln -s <the thing present> <where to put a link>
ln -s <big one> <small one>
```  


They're nice when they start from an environment variable, in case it does change, and also so you don't have to remember the paths. 

3. Design for semi-manual workflows
Your script might be break in the middle and you'll want to rerun just a part of it. 



3. Use a robust subset of shell functionality
`set -euo pipefail` from what this guy calls [Strict mode](http://redsymbol.net/articles/unofficial-bash-strict-mode/) at least when developing














Other
2. Use tools
[This static analysis tool](http://www.shellcheck.net/) will tell you that all kinds of things might be wrong with your script and it will be a false alarm quite often but if you've confused `$*` with `$@` you will be told.
Use an editor with syntax highlighting, it helps your brain think with patterns instead of text.


```
pushd $experiment_directory

popd
```
This might be ins
