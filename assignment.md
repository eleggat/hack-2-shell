# Question 1: Get the data files
Download the following data files from the internet using the curl command: `http://eaton-lab.org/pdsb/test.fastq.gz` and `http://eaton-lab.org/pdsb/iris-data-dirty.csv`. Use the `less` or `zless` commands to look at each file. Describe what these commands do. Finally, use the `head` command to print the first 5 lines of the file `iris-data-dirty.csv`.

I found information about using the `curl` command on the Geeks for Geeks website. I then downloaded the two files into my course folder (my working directory) and used the `-L` option to handle the HTTP and the `-O` option to download it. I used `man` to read about `less`, `zless`, and `head`. `less` shows the contents of the file in an interactive pane, but cannot read an unzipped file. `zless` can read the unzipped file, so must be used for the `test.fastq.gz` file. `head` prints the top lines of the files, and I used the option `-n 5` to specify the number of lines I wanted as 5.

```shell
curl -LO http://eaton-lab.org/pdsb/test.fastq.gz
curl -LO http://eaton-lab.org/pdsb/iris-data-dirty.csv
zless test.fastq.gz
less iris-data-dirty.csv
head -n 5 iris-data-dirty.csv
```

```shell
5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
```

# Queston 2: Clean the data
Use `grep`, `uniq`, and `sed` for this question. Check that all of the species names are spelled correctly in the file `iris-data-dirty.csv`. Also check for missing values stored as `NA`. Create a new file where mispelled names are replaced with the correct values, and lines with `NA` are excluded, and save it as `iris-data-clean.csv`. Use `cut`, `sort` and `uniq` to list the number of data values there are for each species in the new cleaned data file. Describe your work.

I again used `man` to learn about each command, and also read StackOverflow threads for more help. I used `-s 16` with the `uniq` command to identify unique species spellings and find the mispelled ones, skipping the first 16 characters of the line. Then I used `grep` to identify rows with `NA` values. Using this info, I used `sed` to fix the species names, `grep -v` to select rows without missing values, `grep '\S'` to remove empty rows, and the pipe function to put them together into the new file. Finally, I used `cut` with `-d ,` to specify comma delimited and `-f 5` to specify the fifth column only, `sort` to arrange the rows, and `uniq -c` to give the number of times each row is repeated.

```shell
uniq -s 16 iris-data-dirty.csv
grep NA iris-data-dirty.csv
sed 's/setsa/setosa/' iris-data-dirty.csv | sed 's/colour/color/' | grep -v NA | grep '\S' > iris-data-clean.csv
cut -d , -f 5 iris-data-clean.csv | sort | uniq -c
```

```shell
  50 Iris-setosa
  48 Iris-versicolor
  50 Iris-virginica
```

# Question 3: Find a sequence
Find how many lines in the data file `test.fastq.gz` start with "TGCAG" and end with "GAG". Describe your work.

I used `zless` first so that `grep` could read the file. The caret at the beginning of the sequence is to indicate the line should start with TGCAG, and the dollar sign indicates the end. The sequences are within single quotes so that `grep` reads them properly, and not as characters in the pattern. The `-c` option prints only the count of rows.

```shell
zless test.fastq.gz | grep '^TGCAG' | grep -c 'GAG$'
```

```shell
44
```

# Question 4: Find a sequence chunk
Using `grep` and other tools if necessary find all lines that contain the sequence "AAAACCCC" and for each print that line, the line above it, and two lines below it (so that a 4-line chunk around each search hit is printed). Describe your work.

I again started with `zless` so that `grep` could read the file.  The `-A` option let me specify how many lines after to print, and the `-B` option let me specify how many lines before to print. The output automatically separated the four-line chunks with two dashes.

```shell
zless test.fastq.gz | grep -A 2 -B 1 AAAACCCC
```

```shell
@32082_przewalskii.98 GRC13_0027_FC:4:1:5669:1669 length=74
TGCAGAATAGATAGGAAACGTTTTGGCGCTGTAGACATTAAAACCCCAGTAGGACACGGGTATCACAACGTACA
+32082_przewalskii.98 GRC13_0027_FC:4:1:5669:1669 length=74
IIIIIIIIIIIIIIIIIIHIHIIIIIIIIGIIIGIIIIIIHIIIIIIIHIIIIHIIIIIIEHIHHIIIIICIHI
--
@33413_thamno.59 GRC13_0027_FC:4:1:5000:1620 length=74
TGCAGTGGATCGAAAACCCCGAGGCTCAAGGTCACGCCACCGTCTTCGTGGCCAAGTTCTTCGGCCGCGCCGGC
+33413_thamno.59 GRC13_0027_FC:4:1:5000:1620 length=74
IIIIIIIIIIIIIIIIIIIIDHIIHHIIIIIEIBGBGGGIIHEHHHIEBBHHIEGGDGIGGHAEFDBFBDDB?D
```
