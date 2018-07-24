# shell_awk_cheatsheet

## AWK

### Find match and print that line + the 5th following line
awk '/N content/{nr[NR]; nr[NR+5]}; NR in nr' file

### Extract minimum and maximum value of column 7
awk '{if(min==""){min=max=$7}; if($7>max) {max=$7}; if($7< min) {min=$7};} END {print min, max}' file

### Find line with pattern, delete it + delete one line before and one line after
awk '/ATTGGC/{for(x=NR-1;x<=NR+2;x++)d[x];}{a[NR]=$0}END{for(i=1;i<=NR;i++)if(!(i in d))print a[i]}' file

### Delete 3 lines after match
awk '/ATTGGC/{for(x=NR-1;x<=NR+2;x++)d[x];}{a[NR]=$0}END{for(i=1;i<=NR;i++)if(!(i in d))print a[i]}' file

### Select lines between pattern "@M99724" and "+"
awk '/^@M00724/{a=1;next}/^+$/{a=0}a' file

### Extract sequence from fastq files
zcat read1.fastq.gz | awk '/^@HWI/{a=1;next}/^+$/{a=0}a' > sequences_read1.txt
or
zcat read1.fastq.gz | awk '0==(NR + 2) %4' > sequences_read1.txt
or
zcat read1.fastq.gz | paste - - - - | cut -f2

### Trim reads and quality score string (from fastq file)
awk '{if (count++%2==1) print substr ($0, 0, 50); else print $0;}' file.fastq

### "If" in awk
awk '{if ($3 =="" || $4 == "" || $5 == "") print "Some data for",$1,"is missing";' file

### awk input and output field separator
awk -F':' 'BEGIN{OFS="\t";} {print $3,$4;}' file



