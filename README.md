---
permalink: /
---
# shell_awk_cheatsheet

## Shell

### Archive
```
# archive and compress in tar.gz format
tar -zcvf archive-name.tar.gz directory-name
# uncompress and unarchive tar.gz archives
tar -zxvf archive-name.tar.gz
# archive and compress in zip format
zip -r folder.zip folder
# uncompress and unarchive zip archives
unzip folder.zip
```

### Save standard output / error
```        
# one file + display on screen
command |& tee log.txt
# 2 different files
command > out.txt 2> err.txt
```

### Launch scripts at a certain day/time with "at"
```
# launch at 12:30
at 12:30 < test.sh
# launch at 10am on July 31st
at 10am Jul 31 < test.sh
# launch at 11am on August 15th
echo "sh test.sh.sh" | at 11am Aug 15
```

### Remove spaces from file names
```
find -name "* *" -type f | rename 's/ /_/g'
```

### Remove files from /tmp if size is 0
```
find /tmp -size  0 -print0 |xargs -0 rm --
```

### check if files are identical
```
cmp --silent file1 file2 || echo "files are different"
```

### Empty folder from all files except files with .fastq extension
```
shopt -s extglob nocaseglob; rm !(*.fastq)
```

### Remove "_" in front of files/directory names
```
# files
for i in `find . -maxdepth 1 -mindepth 1 -type f -printf '%f\n'`; do  mv $i `echo $i | sed 's/^_//'`; done
# directories
for i in `find . -maxdepth 1 -mindepth 1 -type d -printf '%f\n'`; do  mv $i `echo $i | sed 's/^_//'`; done
```

### List directories
```
find . -maxdepth 1 -mindepth 1 -type d -printf '%f\n'
```

### Check if directory $DIR exists
```
if [ -d "$DIR" ]; then echo "$DIR directory  exists!"; else echo "$DIR directory not found!"; fi
```
### Find files with *pattern* and move/copy them to "FOLDER"
```
# move
find . -name '*pattern*' -exec mv {} FOLDER/ \;
# copy
find . -name '*pattern*' -exec cp {} FOLDER/ \;
```

### Delete all files with a .jpg extension in folder and folders
```
find . -type f -iname \*.jpg -delete
```

### Replace one or more spaces by no space in file
```
sed -i "s/^[ \t]*//g" file
```

### Print directory name if empty
```
for i in `ls -d *`; do if [ ! "$(ls -A ${i})" ]; then echo $i; fi; done
```

### Remove spaces, tabs, carriage returns and newlines
```
tr -d '\040\011\012\015' file
```

### Extract 4 digits from string
```
grep -oE "[0-9]{4}"
```




## AWK

### Remove line if value in column 5 duplicated
```
awk -F, '!seen[$5]++' test.txt
```

### Find match and print that line + the 5th following line
```
awk '/N content/{nr[NR]; nr[NR+5]}; NR in nr' file
```

### Extract minimum and maximum value of column 7
```
awk '{if(min==""){min=max=$7}; if($7>max) {max=$7}; if($7< min) {min=$7};} END {print min, max}' file
```

### Find line with pattern, delete it + delete one line before and one line after
```
awk '/ATTGGC/{for(x=NR-1;x<=NR+2;x++)d[x];}{a[NR]=$0}END{for(i=1;i<=NR;i++)if(!(i in d))print a[i]}' file
```

### Delete 3 lines after match
```
awk '/ATTGGC/{for(x=NR-1;x<=NR+2;x++)d[x];}{a[NR]=$0}END{for(i=1;i<=NR;i++)if(!(i in d))print a[i]}' file
```

### Select lines between pattern "@M99724" and "+"
```
awk '/^@M00724/{a=1;next}/^+$/{a=0}a' file
```

### Extract sequence from fastq files
```
zcat read1.fastq.gz | awk '/^@HWI/{a=1;next}/^+$/{a=0}a' > sequences_read1.txt
# or
zcat read1.fastq.gz | awk '0==(NR + 2) %4' > sequences_read1.txt
# or
zcat read1.fastq.gz | paste - - - - | cut -f2
```

### Trim reads and quality score string (from fastq file)
```
awk '{if (count++%2==1) print substr ($0, 0, 50); else print $0;}' file.fastq
```

### "If" in awk
```
awk '{if ($3 =="" || $4 == "" || $5 == "") print "Some data for",$1,"is missing";' file
```

### awk input and output field separator
```
awk -F':' 'BEGIN{OFS="\t";} {print $3,$4;}' file
```

### Remove scientific notations
```
awk '{printf "%s\t%.0f\t%.0f\n", $1,$2,$3}' file
```

### Pass objects as argument
```
awk -v a=$i -v b=$g '$2==a && $3==b {print}' file
```

### Sum column 2
``` 
awk '{sum += $2} END {print sum}' inputfilename
```


