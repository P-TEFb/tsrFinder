# tsrFinder

tsrFinder identifies transcription start regions (TSRs) from PRO-Cap data.

## About:

This script identifies transcription start regions (TSRs) from PRO-Cap data. Every possible TSR window across the genome is evaluated for the sum of 5' reads (transcription start sites) and the average read length. TSRs are then sorted by total reads and are kept only if they don't overlap with any better-scoring window. For every TSR, this script also calculates the position and read depth of the maximum TSS, and the position and standard deviation of the average TSS.

This script accepts mapped sequencing reads in bed format and requires a chrom.sizes file (obtainable using the fetchChromSizes utility). TSR information is output in bed/bigBed and bedGraph/bigWig format for use with UCSC Genome Browser or utilities such as bedTools or deepTools. A comprehensive tab-delimited text file is also provided for manual extraction of data.

Default parameters are hard-coded at the beginning of the script. We recommend manually changing threads and memorySize to match your environment. The other parameters should instead be adjusted as needed on a per-run basis.

## Usage:

tsrFinder is a bash script that relies on common Linux utilities, awk, and the Jim Kent utilities bedToBigBed and bedGraphToBigWig (http://hgdownload.soe.ucsc.edu/admin/exe/linux.x86_64/). It is intended to be run from the command-line and expects the following syntax:

```
tsrFinder -i <mapped-fragments.bed> -c <###.chrom.sizes> [optional parameters]
```

### Required parameters:

```
-i <mapped-fragments.bed>
-c <###.chrom.sizes>
```

This script is intended to work with paired-end PRO-Cap data, where the 5' and 3' boundaries of every sequenced fragment are known, but will function as long as the full coverage of every read is represented (in other words, untruncated pileup data). The 5' position of each read is used for most calculations, but the 3' end is used to establish fragment length. Filtering is performed only on TSRs with unusually short reads, so single-end sequencing is acceptable.

A chromosome size file is also required to generate bigBed and bigWig output and can be generated using the Jim Kent utility fetchChromSizes (http://hgdownload.soe.ucsc.edu/admin/exe/linux.x86_64/).

### Optional parameters:

```
-n <int>        max length of fragment to consider [default 600 nt]
-s <int>	TSR window size [default 20 bp]
-u <int>	buffer zone upstream of each TSR [default 0 bp]
-w <int>	buffer zone downstream of each TSR [default 0 bp]
-d <int>	minimum sequencing depth per TSR [default 20 reads]
-a <int>	minimum average transcript length per TSR [default 30 nt]
-t <int>        number of CPU threads [default 8]
-m <size>       size of memory buffer per thread [default 1G]
```

Depending on your environment, you will likely need to change -t (the number of CPU threads) and -m (the memory buffer used for sort). Because these options are hard-coded, you will need to adjust them to match your computer. These are exclusively used for the sort utility.

The other parameter that will likely require adjustment is -d (the minimum number of reads per TSR). The stringency of this parameter is dependent on the depth of your sequencing and the amount of background signal. This script does not include a method to statistically evaluate where to set this threshold; it is instead set manually.

We observe that TSRs are usually 20 bp in width and are often clustered. If you wish to select only the major TSR for a given region, -u and -w (upstream and downstream buffer zones) can be manually set. For example, if you are only interested in the best TSR within 1 kb windows, set -u and -w to 500 each. We do not recommend increasing -s (the TSR window size) to accomplish this.

Finally, parameters are included to filter out excessively long PRO-Seq reads (-n) and TSRs composed primarily by short fragments (-a). Because promoter proximal pausing tends to occur around 40 bp downstream of most TSS and genuine nascent transcripts are rarely shorter than 18 nt, TSRs tend to have an average read length greater than 30 nt. Setting -a to 30 tends to eliminate TSRs formed exclusively by sequencing artifacts and hard to map fragments.

### Output:

tsrFinder outputs the boundaries of identified TSRs (of -s width) as well as the position of the maximum TSS (or maxTSS) within each TSR in bed and bigBed format. Due to limitations with the bed file format, the score field is clipped at 1000 and this output is best used for its positioning information.

tsrFinder also provides information for every maxTSS in bedGraph and bigWig format. These files are scored by the number of reads in the maxTSS (scoredByMaxTSS), the number of reads within the overall TSR (scoredByTSRSum), or by the standard deviation of the maxTSS position relative to the mean TSS position within the overall TSR (scoredByStdevAvgTSS).

Finally, a comprehensive tab-delimited text file is also provided. It contains information about TSR boundaries, the max TSS position, the average TSS position, maxTSS-avgTSS, and the standard deviation of the average TSS position.

To make downstream data analyses easier, forward and reverse strand files are also provided separately.

## Citation:

tsrFinder was developed by Kyle Nilson (https://github.com/kylenilson) in the lab of David Price at the University of Iowa (https://price.lab.uiowa.edu/).

It was first described in the following manuscript:

```
Soon....
```
