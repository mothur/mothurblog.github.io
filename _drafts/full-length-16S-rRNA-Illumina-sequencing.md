---
layout: post
title: "Full-length 16S rRNA gene sequencing"
author: "PD Schloss"
date: "January XX, 2016"
comments: true
---

With 454 and now Illumina we have the ability to affordably generate thousands of high quality 16S rRNA gene sequences from hundreds of samples. Awesome! Right? Maybe not.

As much as I prefer database-independent methods for working with 16S rRNA gene sequences, people want to know what group of bugs is ~~responsible for~~ associated with phenomenon X. Telling them OTUs 4, 19, and 23 won't cut it. So, we [classify our sequences](http://www.mothur.org/wiki/classify.seqs) against [one of several possible databases](http://mothur.org/wiki/Taxonomy_outline) and then [find the consensus taxonomy](http://www.mothur.org/wiki/classify.otu) for each OTU. Many people contend that a major problem with the short V4 region sequences we generate is that they are hard to classify. We have some data to suggest that this isn't such a big problem or that longer sequences won't help us much (see [Figure 4](https://peerj.com/preprints/778/)). With sequence classification, I think we have perhaps put too much emphasis on the unknowns and not enough on the knowns.

Do a thought experiment with me. For 20 years we generated a few million full-length 16S rRNA gene sequences by Sanger sequencing from a diverse array of communities. These studies rarely had more than 200 sequences per sample. You could argue that the limit of detection was 1 in 200 or 0.5% of the community. We did a great job of filling in the bacterial tree with these studies. But starting around 2006 we started generating thousands or tens of thousands of sequences per sample for a limit of detection below 0.05%. I would argue that with the increased intra-study replication that these methods have allowed the limit of detection is actually much lower. The result? We're going to find populations that we've never seen before because these populations are generally rare and weren't detected by our Sanger-based approaches. If my hypothesis is correct, then we need to get back in the business of generating full-length sequences that we can put into our databases for taxonomic classification.

Considering that Sanger will cost you about $8 per read at most universities, how can we generate full-length sequences? We've looked a bit at the possibility of using PacBio to generate full-length reads. Although we're currently reassessing this technology, the [original analysis was not promising](https://peerj.com/preprints/778/). I've been very intrigued by a couple of Illumina-based approaches for assembling 16S rRNA gene sequence reads. An earlier effort, EMIRGE, came from Chris Miller while he was a postdoc in Jill Banfield's lab. A more recent proposal has come from Catherine Burke working with Aaron Darling. The basic idea of both methods is to amplify the full-length 16S rRNA gene, shear it, use some barcoding jujitsu, sequence, and assemble.


http://microbe.net/2015/09/13/high-throughput-full-length-16s-sequencing/
http://biorxiv.org/content/early/2014/11/06/010967
http://biorxiv.org/content/biorxiv/early/2014/11/06/010967.full.pdf
http://biorxiv.org/content/biorxiv/suppl/2014/11/06/010967.DC1/010967-1.pdf
