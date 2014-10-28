---
layout: post
title: "README for the RDP v10 reference files"
author: "PD Schloss"
date: "October 28, 2014"
comments: true
---

The good people at the [RDP](http://rdp.cme.msu.edu) have released a new version of the RDO database. A little bit of tweaking is needed to get their files to be compatible with mothur. This README document describes the process that I used to generate the [mothur-compatible reference files](http://mothur.org/wiki/RDP_reference_files). The original files are available from the RDPs [sourceforge server](http://sourceforge.net/projects/rdp-classifier/files/RDP_Classifier_TrainingData/) and were used as the starting point for this README.


<pre><code class="prettyprint ">rm -rf RDPClassifier_16S_trainsetNo10_rawtrainingdata* Trainset9_032012.pds.zip
	
wget -N http://sourceforge.net/projects/rdp-classifier/files/RDP_Classifier_TrainingData/RDPClassifier_16S_trainsetNo10_rawtrainingdata.zip
unzip -o RDPClassifier_16S_trainsetNo10_rawtrainingdata.zip
mv RDPClassifier_16S_trainsetNo10_rawtrainingdata/* ./</code></pre>

Now we'd like to start to form the taxonomy file and the fasta file that will be our reference. Again, using bash commands...


<pre><code class="prettyprint ">sed 's/\|/_/' trainset10_082014_rmdup.fasta &gt; trainset10_082014.rdp.fasta
grep &quot;&gt;&quot; trainset10_082014.rdp.fasta | cut -c 2- &gt; trainset10_082014_rmdup.tax</code></pre>

Next, we'd like to get our taxonomy file properly formatted. First we'll read in the taxonomy data...


<pre><code class="prettyprint ">tax &lt;- read.table(file=&quot;trainset10_082014_rmdup.tax&quot;, sep=&quot;\t&quot;)
tax$V2 &lt;- gsub(&quot; &quot;, &quot;_&quot;, tax$V2)	#remove spaces and replace with '_'
tax$V2 &lt;- gsub(&quot;[^;]*_incertae_sedis$&quot;, &quot;&quot;, tax$V2)
tax$V2 &lt;- gsub('\&quot;', '', tax$V2) #remove quote marks</code></pre>

The RDP inserts a variety of sub taxonomic levels (e.g. suborder) that will get in the way of us having a consistent number of taxonomic levels for our analyses. Let's use the data in `trainset10_db_taxid.txt` to remove these extra taxonomic levels:


<pre><code class="prettyprint ">levels &lt;- read.table(file=&quot;trainset10_db_taxid.txt&quot;, sep=&quot;*&quot;, stringsAsFactors=F)
subs &lt;- levels[grep(&quot;sub&quot;, levels$V5),]
sub.names &lt;- subs$V2

tax.split &lt;- strsplit(tax$V2, split=&quot;;&quot;)

remove.subs &lt;- function(tax.vector){
	return(tax.vector[which(!tax.vector %in% sub.names)])
}

no.subs &lt;- lapply(tax.split, remove.subs)
no.subs.str &lt;- unlist(lapply(no.subs, paste, collapse=&quot;;&quot;))
no.subs.str &lt;- gsub(&quot;^Root;(.*)$&quot;, &quot;\\1;&quot;, no.subs.str)</code></pre>

Finally, we can output the taxonomy data to a file we'll call `trainset10_082014.rdp.tax` to have a consistent naming scheme with previous versions of those files:


<pre><code class="prettyprint ">write.table(cbind(as.character(tax$V1), no.subs.str), &quot;trainset10_082014.rdp.tax&quot;, row.names=F, col.names=F, quote=F, sep=&quot;\t&quot;)</code></pre>

The RDP training sets do not include mitochondria or sequences from eukaryotes. We find that it is helpful to have these sequences because we can get non-specific amplification at times and would like to be able to remove these lineages. Let's go ahead and pull down the pds version of training set v.9 and copy those sequences over to our new training set:


<pre><code class="prettyprint ">wget -N http://mothur.org/w/images/5/59/Trainset9_032012.pds.zip
unzip -o Trainset9_032012.pds.zip</code></pre>

Now let's run a mothur command to pull out the extra sequences that are in the pds files:


<pre><code class="prettyprint ">mothur &quot;#get.lineage(fasta=trainset9_032012.pds.fasta, taxonomy=trainset9_032012.pds.tax, taxon=Eukaryota-Mitochondria)&quot;</code></pre>

This last command gets us the extra "pds" sequences that we can now use to paste on to the end of the normal RDP training set


<pre><code class="prettyprint ">cat trainset10_082014.rdp.tax trainset9_032012.pds.pick.tax &gt; trainset10_082014.pds.tax
cat trainset10_082014.rdp.fasta trainset9_032012.pds.pick.fasta &gt; trainset10_082014.pds.fasta</code></pre>




<pre><code>## cat: trainset9_032012.pds.pick.tax: No such file or directory
## cat: trainset9_032012.pds.pick.fasta: No such file or directory
</code></pre>

While we've got the old version of the training set, it might be nice to see what the differences are. It would have been nice for them to provide a README indicating what changed, but, well, no, they didn't.


<pre><code class="prettyprint ">wc -l *.pds.tax</code></pre>




<pre><code>##    10650 trainset10_082014.pds.tax
##    10172 trainset9_032012.pds.tax
##    20822 total
</code></pre>

We see that there are now 601 more sequences in the RDP training set. Now we're ready to compress the taxonomy files. First we do the RDP files...


<pre><code class="prettyprint ">mkdir trainset10_082014.rdp
cp README.* trainset10_082014.rdp.fasta trainset10_082014.rdp.tax trainset10_082014.rdp
tar cvzf trainset10_082014.rdp.tgz  trainset10_082014.rdp/*</code></pre>




<pre><code>## a trainset10_082014.rdp/README.Rmd
## a trainset10_082014.rdp/trainset10_082014.rdp.fasta
## a trainset10_082014.rdp/trainset10_082014.rdp.tax
</code></pre>

... and then the pds files...


<pre><code class="prettyprint ">mkdir trainset10_082014.pds
cp README.* trainset10_082014.pds.fasta trainset10_082014.pds.tax trainset10_082014.pds
tar cvzf trainset10_082014.pds.tgz  trainset10_082014.pds/*</code></pre>




<pre><code>## a trainset10_082014.pds/README.Rmd
## a trainset10_082014.pds/trainset10_082014.pds.fasta
## a trainset10_082014.pds/trainset10_082014.pds.tax
</code></pre>
