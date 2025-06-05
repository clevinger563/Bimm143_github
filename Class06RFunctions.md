# Class06-R-Functions
Christopher Levinger (A17390693)

- [1 Function Basics](#1-function-basics)
- [2 Generate DNA function](#2-generate-dna-function)
- [3. Generate Protein function](#3-generate-protein-function)

## 1 Function Basics

Let’s start writing our first silly function to add some numbers:

Every R function has 3 things:

-name (we get to pick this) -input arguments (there can be loads of
these separated by a comma) - the body (the R code that does the work)

``` r
add <- function(x, y=10, z=0) {
  x + y + z
}
```

I can just use this function like any other function as long as R knows
about it (i.e run the code chunk)

``` r
add(1, 100)
```

    [1] 101

``` r
add(x=c(1,2,3,4),y=100)
```

    [1] 101 102 103 104

``` r
add(1)
```

    [1] 11

Functions can have “required” input arguments and “optional” input
arguments. The optional arguments are defined with an equals default
value. (`y=10`) in the function definition.

``` r
add(1,100,10)
```

    [1] 111

## 2 Generate DNA function

> Q: Write a function to return a nucleotide sequence of a user
> specified length? Call it `generate_dna()` The \`\` function can help
> here

``` r
#generate_dna <- function(size=5) {}

students <- c("jeff","jeremy","peter")

sample(students, size=5, replace=TRUE)
```

    [1] "jeff"   "jeremy" "jeff"   "jeremy" "peter" 

Now work with `bases` rather than `students`

``` r
bases <- c("A","C","G","T")
sample(bases, size=10, replace=TRUE)
```

     [1] "C" "C" "A" "T" "A" "G" "A" "C" "C" "C"

Now I have a working ‘snippet’ of code I can use this as the body of my
first function here:

``` r
generate_dna <- function(size=5){
  bases <- c("A","C","G","T")
sample(bases, size=size, replace=TRUE)
}
```

``` r
generate_dna(100)
```

      [1] "G" "A" "C" "A" "C" "A" "A" "A" "A" "G" "T" "A" "C" "G" "G" "G" "G" "G"
     [19] "G" "G" "G" "T" "A" "G" "A" "A" "T" "A" "G" "G" "A" "G" "A" "T" "C" "A"
     [37] "G" "G" "T" "A" "T" "T" "G" "G" "T" "T" "C" "T" "G" "G" "A" "A" "A" "G"
     [55] "C" "G" "A" "T" "G" "C" "T" "A" "T" "A" "C" "A" "A" "C" "T" "T" "A" "G"
     [73] "A" "C" "A" "T" "G" "T" "G" "T" "T" "A" "G" "C" "C" "G" "A" "A" "G" "G"
     [91] "A" "G" "A" "C" "T" "A" "G" "T" "G" "A"

``` r
generate_dna()
```

    [1] "A" "G" "A" "G" "G"

I want the ability to return a sequence like “AGTACCTG” i.e a one
element vector where the bases are all together.

``` r
generate_dna <- function(size=5, together=TRUE) {
bases <- c("A","C","G","T")
sequence <- sample(bases, size=size, replace=TRUE)

if(together) {
  paste(sequence, collapse="")
}
return(sequence)
}
```

``` r
generate_dna(together=F)
```

    [1] "T" "A" "G" "A" "T"

## 3. Generate Protein function

> Q. Write a protein sequence generating function that will return
> sequences of a user specified length?

> Q Generate random protein sequences of length 6 to 12 amino acids

> Q Determine if these sequences can be found in nature We can get the
> set of 20 natural amino-acids from the “bio3d” package

``` r
aa <- bio3d::aa.table$aa1[1:20]
```

and use this in our function

``` r
  generate_protein <- function(size=6, together=TRUE){
  ## Get the 20 amino acids as a vector
  aa <- bio3d::aa.table$aa1[1:20]
  sequence <- sample(aa, size, replace=TRUE)
  
  ## Optionally return a single element string
  if(together){
    sequence <- paste(sequence, collapse="")
  }
  return(sequence)
}
```

We can fix this inability to generate multiple sequences by either
editing and adding to the function body code ( eg. a for loop) or by
using the R **apply** family of utility functions.

``` r
sapply(6:12,generate_protein)
```

    [1] "QAYALV"       "KQSNARR"      "MYPRATIH"     "VMYIHAHKP"    "VSFGFQKFMQ"  
    [6] "TVEPFCPKMPN"  "WFWFWTTDSYSY"

It would be cool and useful if I could get FAFSTA format output.

``` r
ans <- sapply(6:12, generate_protein)
ans
```

    [1] "HHCKSS"       "FHSFFYL"      "HPYHFDTE"     "TCGQNSQTT"    "KYVTVKIPQE"  
    [6] "HSMRGDHPYYW"  "ELWGIHLISDPY"

``` r
cat(ans, sep="/n")
```

    HHCKSS/nFHSFFYL/nHPYHFDTE/nTCGQNSQTT/nKYVTVKIPQE/nHSMRGDHPYYW/nELWGIHLISDPY

I want this to look like FAFSTA format:

    >ID.6
    HLDWLV
    >ID.7
    VREAIQN
    >ID.8
    WPRSKACN

The functions paste() and cat() can help us here…

``` r
cat( paste (">ID.", 7:12, "/n", ans, sep=""), sep="\n")
```

    >ID.7/nHHCKSS
    >ID.8/nFHSFFYL
    >ID.9/nHPYHFDTE
    >ID.10/nTCGQNSQTT
    >ID.11/nKYVTVKIPQE
    >ID.12/nHSMRGDHPYYW
    >ID.7/nELWGIHLISDPY

``` r
id.line <- paste(">ID.",6:12, sep="")
```

``` r
id.line <- paste(">10.",6:12, sep="")
seq.line <- paste(id.line,ans, sep="\n")
cat(seq.line, sep="\n", file="myseq.fa")
```

> Q: Determine if thse sequences can be found in anture or are they
> unique? Why or why not? I BLASTp searched my FAFSTA format sequences
> against NR and found that length 6,7,8, are not unique and can be
> found in databases with 100% coverage and 100% identity.

Random sequences of length 9 and above are unique and can’t be found in
the databases.
