---
title: Genome assembly and annotation
tags:
  - bioinformatics
  - programming
date: 2018-01-11

---

This is going to be an introduction for programmers who don't know much bioinformatics, pretty much me before I started working on [WormBase ParaSite](http://parasite.wormbase.org). For something less ignorant, simplistic, and without swearwords, you could read [Genome annotation: from sequence to biology (2001)](https://www.ncbi.nlm.nih.gov/pubmed/11433356). 

---
### Motivation
**Imagine you have somebody's code, it's messy and you don't understand it, so you decide to add comments.**

It's actually what happens except it's something's code, not somebody's. The human genome is, by now, somewhat understood, and it's not my job to work on it. This is for the better, in WormBase ParaSite we usually hate the entity whose code we're reading and we want them destroyed.

### Sequencing and assembly

#### Get the code
You get the code through [sequencing](https://en.wikipedia.org/wiki/Sequencing): get DNA from the cells, chop it up into pieces, multiply them, then read off enough of the little pieces that maybe you'll be able to put everything together. I apologise that it's not very convenient.

#### Try piece the code together
This is technically before you looked at the code and realised it's messy and you don't understand it. It's called [sequence assembly](https://en.wikipedia.org/wiki/Sequence_assembly), and it's a classical computer science problem, it's hard but we've worked out how to do it - put the short pieces together to make larger pieces.The best possible effect is that we've assembled as many larger pieces as there are chromosomes, and humans have done that for e.g. their own genome, but it's hard. Many genomes are worked out only to the level of scaffolds: pieces of DNA that are quite long, but not the whole chromosomes. Some genomes are intrinsically complicated, for example, the genomes of cereals are 2-3 times as long as the human genome. Some other genomes are relatively simple but we don't have them fully worked out because we've only started.

---
### Gene prediction

#### Find pieces that look like a start of something that does something
A piece of DNA that looks like a start of something that does something is called an [open reading frame](https://en.wikipedia.org/wiki/Open_reading_frames). We can work them out thanks to a following fact: [the machinery transcribing DNA into RNA](https://en.wikipedia.org/wiki/Eukaryotic_transcription#RNA_polymerase) will attach to the DNA at [the start codon](https://en.wikipedia.org/wiki/Start_codon), which is three letters, usually AUG, and detach at a stop codon, another three letters. It is a simple computation on the text of DNA what segments between starts and stops are, and that gives us the ORFs.

#### Run some heuristics
Open reading frames that could be genes are, for example, probably going to be long enough to encode a protein. We also have some general knowledge on how proteins are structured, and in a sequence we may recognise sub-pieces from primary structure - elements tens of amino acids large TODO they are called protein domains I think. Paper (1) has a list of algorithms for gene prediction, they all have these opaque acronyms and each recognise a different recognisable thing, and there's not much harm in running them all and giving identifiers to all the results because LOL they're cheap.

---
### Functional annotation

#### Find the main method
Some bits of the genome have to be about self-replication - instructions for the machine to make itself. Hence there are genes that code for all the cute bits running transcription and translation.

#### Identify standard library version
God has created organisms of all the species at the same time, at day five of creation, and tests our faith hard, for example with that fossil business and letting us observe [new species on the way](https://en.wikipedia.org/wiki/Speciation). He made it happen in such a way that all life on Earth shares DNA with each other. Breathing, eating, and making babies appears to vary a lot between species on the level of organisms but the molecular machinery for these is pretty uniform. Hence it is helpful to look at the [Conserved sequence](https://en.wikipedia.org/wiki/Conserved_sequence) - the portions of two species' DNA that overlap each other - and [Orthologs](https://en.wikipedia.org/wiki/Sequence_homology) - genes from two species that make the same proteins.

#### Find out which other bits you even wanna bother to try know about
Protein coding genes are good candidates for "doing anything at all", but they're not the only important ones - there are also genes that don't code for proteins, but regulate whether some other protein shows up or not: [transcription factors](https://en.wikipedia.org/wiki/Transcription_factor).

---
### Marvel at it all
If this seems needlessly complicated and it is not how you'd design a cell with stuff going on inside it, you have every right to be angry and upset. Nature is the worst programmer out there, it only gets stuff working after lots of random edits, and I'm not sure if it ever did any better than us humans.

---
---
##### Postscript
This is all outdated, and actually tries to follow the 2001 Stein paper. There are new technologies available, sequencing got cheaper, and there are alternatives for having the assembly- for example, you could just figure out what the transcripts are by sequencing and assembling not DNA but RNA. You can guide an assembly with a similar one, and use more sources of evidence, and view them on nifty GUIs. It's quite a time to be assembling genomes.
