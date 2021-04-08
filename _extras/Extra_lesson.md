---
layout: page
title: Bonus materials
---

<script>
function goBack() {
  window.history.back();
}
</script>

[1.0 Using the Carpentries AWS images - from lesson 01](#AWS_info)    
[2.0 Automation using loops and basename- from lesson 04](#Loops)    
[3.0 File manipulation and pipes - from lesson 04](#File_man)    
[4.0 Moving files to/from the cloud - from lesson 05](#Move_data).    
[next_item_here](#name)        

## <a name="AWS_info"></a> Using the Carpentries AWS images
You can log-in to the remote server using the instructions 
[here](http://www.datacarpentry.org/cloud-genomics/02-logging-onto-cloud/#logging-onto-a-cloud-instance). 
Your instructor will supply the `ip_address` and password that you need to login.

Each of you will have a different `ip_address`. This will 
prevent us from accidentally changing each other's files as we work through the
exercises. The password will be the same for everyone. 

<button onclick="goBack()">Go Back</button>

##  <a name="Loops"></a> Writing for loops

Loops are key to productivity improvements through automation as they allow us to execute commands repeatedly. 
Similar to wildcards and tab completion, using loops also reduces the amount of typing (and typing mistakes). 
Loops are helpful when performing operations on groups of sequencing files, such as unzipping or trimming multiple
files. We will use loops for these purposes in subsequent analyses, but will cover the basics of them for now.

When the shell sees the keyword `for`, it knows to repeat a command (or group of commands) once for each item in a list. 
Each time the loop runs (called an iteration), an item in the list is assigned in sequence to the **variable**, and 
the commands inside the loop are executed, before moving on to the next item in the list. Inside the loop, we call for 
the variable's value by putting `$` in front of it. The `$` tells the shell interpreter to treat the **variable**
as a variable name and substitute its value in its place, rather than treat it as text or an external command. In shell programming, this is usually called "expanding" the variable.

Sometimes, we want to expand a variable without any whitespace to its right.
Suppose we have a variable named `foo` that contains the text `abc`, and would
like to expand `foo` to create the text `abcEFG`.

~~~
$ foo=abc
$ echo foo is $foo
foo is abc
$ echo foo is $fooEFG      # doesn't work
foo is
~~~
{: .bash}

The interpreter is trying to expand a variable named `fooEFG`, which (probably)
doesn't exist. We can avoid this problem by enclosing the variable name in 
braces (`{` and `}`, sometimes called "squiggle braces"). `bash` treats the `#`
character as a comment character. Any text on a line after a `#` is ignored by
bash when evaluating the text as code.

~~~
$ foo=abc
$ echo foo is $foo
foo is abc
$ echo foo is ${foo}EFG      # now it works!
foo is abcEFG
~~~
{: .bash}

Let's write a for loop to show us the first two lines of the fastq files we downloaded earlier. You will notice the shell prompt changes from `$` to `>` and back again as we were typing in our loop. The second prompt, `>`, is different to remind us that we haven’t finished typing a complete command yet. A semicolon, `;`, can be used to separate two commands written on a single line.

~~~
$ cd ../untrimmed_fastq/
~~~
{: .bash}

~~~
$ for filename in *.fastq
> do
> head -n 2 ${filename}
> done
~~~
{: .bash}

The for loop begins with the formula `for <variable> in <group to iterate over>`. In this case, the word `filename` is designated 
as the variable to be used over each iteration. In our case `SRR097977.fastq` and `SRR098026.fastq` will be substituted for `filename` 
because they fit the pattern of ending with .fastq in the directory we've specified. The next line of the for loop is `do`. The next line is 
the code that we want to execute. We are telling the loop to print the first two lines of each variable we iterate over. Finally, the
word `done` ends the loop.

After executing the loop, you should see the first two lines of both fastq files printed to the terminal. Let's create a loop that 
will save this information to a file.

~~~
$ for filename in *.fastq
> do
> head -n 2 ${filename} >> seq_info.txt
> done
~~~
{: .bash}

When writing a loop, you will not be able to return to previous lines once you have pressed Enter. Remember that we can cancel the current command using

- <kbd>Ctrl</kbd>+<kbd>C</kbd>

If you notice a mistake that is going to prevent your loop for executing correctly.

Note that we are using `>>` to append the text to our `seq_info.txt` file. If we used `>`, the `seq_info.txt` file would be rewritten
every time the loop iterates, so it would only have text from the last variable used. Instead, `>>` adds to the end of the file.

## Using Basename in for loops
Basename is a function in UNIX that is helpful for removing a uniform part of a name from a list of files. In this case, we will use basename to remove the `.fastq` extension from the files that we’ve been working with. 

~~~
$ basename SRR097977.fastq .fastq
~~~
{: .bash}

We see that this returns just the SRR accession, and no longer has the .fastq file extension on it.

~~~
SRR097977
~~~
{: .output}

If we try the same thing but use `.fasta` as the file extension instead, nothing happens. This is because basename only works when it exactly matches a string in the file.

~~~
$ basename SRR097977.fastq .fasta
~~~
{: .bash}

~~~
SRR097977.fastq
~~~
{: .output}

Basename is really powerful when used in a for loop. It allows to access just the file prefix, which you can use to name things. Let's try this.

Inside our for loop, we create a new name variable. We call the basename function inside the parenthesis, then give our variable name from the for loop, in this case `${filename}`, and finally state that `.fastq` should be removed from the file name. It’s important to note that we’re not changing the actual files, we’re creating a new variable called name. The line > echo $name will print to the terminal the variable name each time the for loop runs. Because we are iterating over two files, we expect to see two lines of output.

~~~
$ for filename in *.fastq
> do
> name=$(basename ${filename} .fastq)
> echo ${name}
> done
~~~
{: .bash}



> ## Exercise
>
> Print the file prefix of all of the `.txt` files in our current directory.
>
>> ## Solution
>>  
>>
>> ~~~
>> $ for filename in *.txt
>> > do
>> > name=$(basename ${filename} .txt)
>> > echo ${name}
>> > done
>> ~~~
>> {: .bash}
>>
> {: .solution}
{: .challenge}

One way this is really useful is to move files. Let's rename all of our .txt files using `mv` so that they have the years on them, which will document when we created them. 

~~~
$ for filename in *.txt
> do
> name=$(basename ${filename} .txt)
> mv ${filename}  ${name}_2019.txt
> done
~~~
{: .bash}


> ## Exercise
>
> Remove `_2019` from all of the `.txt` files. 
>
>> ## Solution
>>  
>>
>> ~~~
>> $ for filename in *_2019.txt
>> > do
>> > name=$(basename ${filename} _2019.txt)
>> > mv ${filename} ${name}.txt
>> > done
>> ~~~
>> {: .bash}
>>
> {: .solution}
{: .challenge}

<button onclick="goBack()">Go Back</button>

##  <a name="File_man"></a> File manipulation and more practice with pipes

Let's use the tools we've added to our tool kit so far, along with a few new ones, to example our SRA metadata file. First, let's navigate to the correct directory.

~~~
$ cd
$ cd shell_data/sra_metadata
~~~
{: .bash}

This file contains a lot of information about the samples that we submitted for sequencing. We
took a look at this file in an earlier lesson. Here we're going to use the information in this
file to answer some questions about our samples.

#### How many of the read libraries are paired end?

The samples that we submitted to the sequencing facility were a mix of single and paired end
libraries. We know that we recorded information in our metadata table about which samples used 
which library preparation method, but we don't remember exactly where this data is recorded. 
Let's start by looking at our column headers to see which column might have this information. Our
column headers are in the first row of our data table, so we can use `head` with a `-n` flag to
look at just the first row of the file.

~~~
$ head -n 1 SraRunTable.txt
~~~
{: .bash}

~~~
BioSample_s	InsertSize_l	LibraryLayout_s	Library_Name_s	LoadDate_s	MBases_l	MBytes_l	ReleaseDate_s Run_s SRA_Sample_s Sample_Name_s Assay_Type_s AssemblyName_s BioProject_s Center_Name_s Consent_s Organism_Platform_s SRA_Study_s g1k_analysis_group_s g1k_pop_code_s source_s strain_s
~~~
{: .output}

That is only the first line of our file, but because there are a lot of columns, the output
likely wraps around your terminal window and appears as multiple lines. Once we figure out which
column our data is in, we can use a command called `cut` to extract the column of interest.

Because this is pretty hard to read, we can look at just a few column header names at a time by combining the `|` redirect and `cut`.

~~~
$ head -n 1 SraRunTable.txt | cut -f1-4
~~~
{: .bash}

`cut` takes a `-f` flag, which stands for "field". This flag accepts a list of field numbers,
in our case, column numbers. Here we are extracting the first four column names.

~~~
BioSample_s InsertSize_l      LibraryLayout_s	Library_Name_s    
~~~
{: .output}

The LibraryLayout_s column looks like it should have the information we want.  Let's look at some
of the data from that column. We can use `cut` to extract only the 3rd column from the file and
then use the `|` operator with `head` to look at just the first few lines of data in that column.

~~~
$ cut -f3 SraRunTable.txt | head -n 10
~~~
{: .bash}

~~~
LibraryLayout_s
SINGLE
SINGLE
SINGLE
SINGLE
SINGLE
SINGLE
SINGLE
SINGLE
PAIRED
~~~
{: .output}

We can see that there are (at least) two categories, SINGLE and PAIRED.  We want to search all entries in this column
for just PAIRED and count the number of matches. For this, we will use the `|` operator twice
to combine `cut` (to extract the column we want), `grep` (to find matches) and `wc` (to count matches).

~~~
$ cut -f3 SraRunTable.txt | grep PAIRED | wc -l
~~~
{: .bash}

~~~
2
~~~
{: .output}

We can see from this that we have only two paired-end libraries in the samples we submitted for 
sequencing.

> ## Exercise
>
> How many single-end libraries are in our samples? 
>
>> ## Solution
>> ~~~
>> $ cut -f3 SraRunTable.txt | grep SINGLE | wc -l
>> ~~~
>> {: .bash}
>> 
>> ~~~
>> 35
>> ~~~
>> {: .output}
>>
> {: .solution}
{: .challenge}

#### How many of each class of library layout are there?  

We can extract even more information from our metadata table if we add in some new tools: `sort` and `uniq`. The `sort` command will sort the lines of a text file and the `uniq` command will
filter out repeated neighboring lines in a file. You might expect `uniq` to
extract all of the unique lines in a file. This isn't what it does, however, for reasons
involving computer memory and speed. If we want to extract all unique lines, we 
can do so by combining `uniq` with `sort`. We'll see how to do this soon.

For example, if we want to know how many samples of each library type are recorded in our table,
we can extract the third column (with `cut`), and pipe that output into `sort`. 

~~~
$ cut -f3 SraRunTable.txt | sort
~~~
{: .bash}

If you look closely, you might see that we have one line that reads "LibraryLayout_s". This is the 
header of our column. We can discard this information using the `-v` flag in `grep`, which means 
return all the lines that **do not** match the search pattern.

~~~
$ cut -f3 SraRunTable.txt | grep -v LibraryLayout_s | sort
~~~
{: .bash}

This command returns a sorted list (too long to show here) of PAIRED and SINGLE values. We can use
the `uniq` command to see a list of all the different categories that are present. If we do this,
we see that the only two types of libraries we have present are labelled PAIRED and SINGLE. There 
aren't any other types in our file.

~~~
$ cut -f3 SraRunTable.txt | grep -v LibraryLayout_s | sort | uniq
~~~
{: .bash}

~~~
PAIRED
SINGLE
~~~
{: .output}

If we want to count how many of each we have, we can use the `-c` (count) flag for `uniq`. 

~~~
$ cut -f3 SraRunTable.txt | grep -v LibraryLayout_s | sort | uniq -c
~~~
{: .bash}

~~~
2 PAIRED
35 SINGLE
~~~
{: .output}

> ## Exercise
>
> 1. How many different sample load dates are there?   
> 2. How many samples were loaded on each date?  
> 
>> ## Solution
>>  
>> 1. There are two different sample load dates.  
>>
>>    ~~~
>>    cut -f5 SraRunTable.txt | grep -v LoadDate_s | sort | uniq
>>    ~~~
>>    {: .bash}
>>
>>    ~~~
>>    25-Jul-12
>>    29-May-14
>>    ~~~
>>    {: .output}
>>
>> 2. Six samples were loaded on one date and 31 were loaded on the other.
>>
>>    ~~~
>>    cut -f5 SraRunTable.txt | grep -v LoadDate_s | sort | uniq -c
>>    ~~~
>>    {: .bash}
>>
>>    ~~~
>>     6 25-Jul-12
>>    31 29-May-14
>>    ~~~
>>    {: .output}
>>
> {: .solution}
{: .challenge}


#### Can we sort the file by library layout and save that sorted information to a new file?  

We might want to re-order our entire metadata table so that all of the paired-end samples appear
together and all of the single-end samples appear together. We can use the `-k` (key) flag for `sort` to
sort based on a particular column. This is similar to the `-f` flag for `cut`.

Let's sort based on the third column (`-k3`) and redirect our output to a new file.

~~~
$ sort -k3 SraRunTable.txt > SraRunTable_sorted_by_layout.txt
~~~
{: .bash}

#### Can we extract only paired-end records into a new file?  

We also might want to extract the information for all samples that meet a specific criterion 
(for example, are paired-end) and put those lines of our table in a new file. First, we need
to check to make sure that the pattern we're searching for ("PAIRED") only appears in the column
where we expect it to occur (column 3). We know from earlier that there are only two paired-end
samples in the file, so we can `grep` for "PAIRED" and see how many results we get.

~~~
$ grep PAIRED SraRunTable.txt | wc -l
~~~
{: .bash}

~~~
2
~~~
{: .output}

There are only two results, so we can use "PAIRED" as our search term to extract the paired-end 
samples to a new file.

~~~
$ grep PAIRED SraRunTable.txt > SraRunTable_only_paired_end.txt
~~~
{: .bash}

> ## Exercise
> Sort samples by load date and export each of those sets to a new file (one new file per
> unique load date). 
> 
> > ## Solution
> > 
> > ~~~ 
> > $ grep 25-Jul-12 SraRunTable.txt > SraRunTable_25-Jul-12.txt
> > $ grep 29-May-14 SraRunTable.txt > SraRunTable_29-May-14.txt
> > ~~~
> > {: .bash}
> >
> {: .solution}
{: .challenge}

## Making code more customizeable using command line arguments ##

In Lesson 05 (Writing Scripts) we used the `grep` command line tool to look for FASTQ records with 
lots of Ns from all the .fastq files in our current folder using the following code: 

~~~
$ grep -B1 -A2 -h NNNNNNNNNN *.fastq | grep -v '^--' > scripted_bad_reads.txt
~~~
{: .bash}

This is very useful, but could be more customizeable. We may want to be able to run this command 
over and over again without needing to copy and paste it and allow the user to specify exactly which
file they want to examine for bad reads. 

We can accomplish these goals by including the above command in a script that takes in user input
via a command line argument. We can slightly modify our `bad-reads-script.sh` file to do so. Use
`c` to copy your `bad-reads-script.sh` into a new script called `custom-bad-reads-script.sh`. Make
the following modifications to `custom-bad-reads-script.sh`:

~~~
filename=$1
grep -B1 -A2 -h NNNNNNNNNN $filename | grep -v '^--' > scripted_bad_reads.txt
~~~
{: .bash}

`$1` is our command line argument. The line `filename=$1` tells Bash to take the first thing you type
after the name of the script itself and assign that value to a variable called filename.

For example, this script can be run in the following way to output the bad reads just from one file: 

~~~
bash custom-bad-reads-script.sh SRR098026.fastq
~~~
{: .bash}

We can then take a look at what the output file currently contains using `head scripted_bad_reads.txt`: 
~~~
@SRR098026.1 HWUSI-EAS1599_1:2:1:0:968 length=35
NNNNNNNNNNNNNNNNCNNNNNNNNNNNNNNNNNN
+SRR098026.1 HWUSI-EAS1599_1:2:1:0:968 length=35
!!!!!!!!!!!!!!!!#!!!!!!!!!!!!!!!!!!
@SRR098026.2 HWUSI-EAS1599_1:2:1:0:312 length=35
NNNNNNNNNNNNNNNNANNNNNNNNNNNNNNNNNN
+SRR098026.2 HWUSI-EAS1599_1:2:1:0:312 length=35
!!!!!!!!!!!!!!!!#!!!!!!!!!!!!!!!!!!
@SRR098026.3 HWUSI-EAS1599_1:2:1:0:570 length=35
NNNNNNNNNNNNNNNNANNNNNNNNNNNNNNNNNN
~~~
{: .output}

This should be same output as using our original code and manually modifying the
original standalone code on the command line to "SRR098026.fastq" on the command line,
which should give us the same output as above:
~~~
$ grep -B1 -A2 -h NNNNNNNNNN SRR098026.fastq | grep -v '^--' > scripted_bad_reads.txt
head scripted_bad_reads.txt
~~~
{: .bash}

~~~
@SRR098026.1 HWUSI-EAS1599_1:2:1:0:968 length=35
NNNNNNNNNNNNNNNNCNNNNNNNNNNNNNNNNNN
+SRR098026.1 HWUSI-EAS1599_1:2:1:0:968 length=35
!!!!!!!!!!!!!!!!#!!!!!!!!!!!!!!!!!!
@SRR098026.2 HWUSI-EAS1599_1:2:1:0:312 length=35
NNNNNNNNNNNNNNNNANNNNNNNNNNNNNNNNNN
+SRR098026.2 HWUSI-EAS1599_1:2:1:0:312 length=35
!!!!!!!!!!!!!!!!#!!!!!!!!!!!!!!!!!!
@SRR098026.3 HWUSI-EAS1599_1:2:1:0:570 length=35
NNNNNNNNNNNNNNNNANNNNNNNNNNNNNNNNNN
~~~
{: .output}

<button onclick="goBack()">Go Back</button>
<hr>

##  <a name="Move_data"></a> Moving and Downloading Data

So far, we've worked with data that is pre-loaded on the instance in the cloud. Usually, however,
most analyses begin with moving data onto the instance. Below we'll show you some commands to 
download data onto your instance, or to move data between your computer and the cloud.

### Getting data from the cloud

There are two programs that will download data from a remote server to your local
(or remote) machine: ``wget`` and ``curl``. They were designed to do slightly different
tasks by default, so you'll need to give the programs somewhat different options to get
the same behaviour, but they are mostly interchangeable.

 - ``wget`` is short for "world wide web get", and it's basic function is to *download*
 web pages or data at a web address.

 - ``cURL`` is a pun, it is supposed to be read as "see URL", so its basic function is
 to *display* webpages or data at a web address.

Which one you need to use mostly depends on your operating system, as most computers will
only have one or the other installed by default.

Let's say you want to download some data from Ensembl. We're going to download a very small
tab-delimited file that just tells us what data is available on the Ensembl bacteria server.
Before we can start our download, we need to know whether we're using ``curl`` or ``wget``.

To see which program you have, type:
 
~~~
$ which curl
$ which wget
~~~
{: .bash}

``which`` is a BASH program that looks through everything you have
installed, and tells you what folder it is installed to. If it can't
find the program you asked for, it returns nothing, i.e. gives you no
results.

On Mac OSX, you'll likely get the following output:

~~~
$ which curl
~~~
{: .bash}

~~~
/usr/bin/curl
~~~
{: .output}

~~~
$ which wget
~~~
{: .bash}

~~~
$
~~~
{: .output}

This output means that you have ``curl`` installed, but not ``wget``.

Once you know whether you have ``curl`` or ``wget``, use one of the
following commands to download the file:

~~~
$ cd
$ wget ftp://ftp.ensemblgenomes.org/pub/release-37/bacteria/species_EnsemblBacteria.txt
~~~
{: .bash}

or

~~~
$ cd
$ curl -O ftp://ftp.ensemblgenomes.org/pub/release-37/bacteria/species_EnsemblBacteria.txt
~~~
{: .bash}

Since we wanted to *download* the file rather than just view it, we used ``wget`` without
any modifiers. With ``curl`` however, we had to use the -O flag, which simultaneously tells ``curl`` to
download the page instead of showing it to us **and** specifies that it should save the
file using the same name it had on the server: species_EnsemblBacteria.txt

It's important to note that both ``curl`` and ``wget`` download to the computer that the
command line belongs to. So, if you are logged into AWS on the command line and execute
the ``curl`` command above in the AWS terminal, the file will be downloaded to your AWS
machine, not your local one.

### Moving files between your laptop and your instance

What if the data you need is on your local computer, but you need to get it *into* the
cloud? There are also several ways to do this, but it's *always* easier
to start the transfer locally. **This means if you're typing into a terminal, the terminal
should not be logged into your instance, it should be showing your local computer. If you're
using a transfer program, it needs to be installed on your local machine, not your instance.**

## Transferring Data Between your Local Machine and the Cloud

These directions are platform specific, so please follow the instructions for your system:

**Please select the platform you wish to use for the exercises: <select id="id_platform" name="platformlist" onchange="change_content_by_platform('id_platform');return false;"><option value="unix" id="id_unix" selected> UNIX </option><option value="win" id="id_win" selected> Windows </option></select>**



<div id="div_unix" style="display:block" markdown="1">

### Uploading Data to your Virtual Machine with scp

`scp` stands for 'secure copy protocol', and is a widely used UNIX tool for moving files
between computers. The simplest way to use `scp` is to run it in your local terminal,
and use it to copy a single file:

~~~
scp <file I want to move> <where I want to move it>
~~~
{: .bash}

Note that you are always running `scp` locally, but that *doesn't* mean that
you can only move files from your local computer. In order to move a file from your local computer to an AWS instance, the command would look like this:

~~~
$ scp <local file> <AWS instance>
~~~
{: .bash}

To move it back to your local computer, you re-order the `to` and `from` fields:

~~~
$ scp <AWS instance> <local file>
~~~
{: .bash}

#### Uploading Data to your Virtual Machine with scp

Open the terminal and use the `scp` command to upload a file (e.g. local_file.txt) to the dcuser home directory:

~~~
$  scp local_file.txt dcuser@ip.address:/home/dcuser/
~~~
{: .bash}

#### Downloading Data from your Virtual Machine with scp

Let's download a text file from our remote machine. You should have a file that contains bad reads called ~/shell_data/scripted_bad_reads.txt.

**Tip:** If you are looking for another (or any really) text file in your home directory to use instead, try:

~~~
$ find ~ -name *.txt
~~~
{: .bash}


Download the bad reads file in ~/shell_data/scripted_bad_reads.txt to your home ~/Download directory using the following command **(make sure you substitute dcuser@ip.address with your remote login credentials)**:

~~~
$ scp dcuser@ip.address:/home/dcuser/shell_data/untrimmed_fastq/scripted_bad_reads.txt ~/Downloads
~~~
{: .bash}

Remember that in both instances, the command is run from your local machine, we've just flipped the order of the to and from parts of the command.
</div>


<div id="div_win" style="display:block" markdown="1">

### Uploading Data to your Virtual Machine with PSCP

If you're using a PC, we recommend you use the *PSCP* program. This program is from the same suite of
tools as the PuTTY program we have been using to connect.

1. If you haven't done so, download pscp from [http://the.earth.li/~sgtatham/putty/latest/x86/pscp.exe](http://the.earth.li/~sgtatham/putty/latest/x86/pscp.exe)
2. Make sure the *PSCP* program is somewhere you know on your computer. In this case,
your Downloads folder is appropriate.
3. Open the windows [PowerShell](https://en.wikipedia.org/wiki/Windows_PowerShell);
go to your start menu/search enter the term **'cmd'**; you will be able to start the shell
(the shell should start from C:\Users\your-pc-username>).
4. Change to the Downloads directory:

~~~
> cd Downloads
~~~
{: .bash}

5. Locate a file on your computer that you wish to upload (be sure you know the path). Then upload it to your remote machine **(you will need to know your AMI instance address (which starts with ec2), and login credentials)**. You will be prompted to enter a password, and then your upload will begin. **(make sure you substitute 'your-pc-username' for your actual pc username and 'ec2-54-88-126-85.compute-1.amazonaws.com' with your AMI instance address)**

~~~
C:\User\your-pc-username\Downloads> pscp.exe local_file.txt dcuser@ec2-54-88-126-85.compute-1.amazonaws.com:/home/dcuser/
~~~
{: .bash}

### Downloading Data from your Virtual Machine with PSCP

1. Follow the instructions in the Upload section to download (if needed) and access the *PSCP* program (steps 1-3)
2. Download the text file to your current working directory (represented by a .) using the following command **(make sure you substitute 'your-pc-username' for your actual pc username and 'ec2-54-88-126-85.compute-1.amazonaws.com' with your AMI instance address)**

~~~
C:\User\your-pc-username\Downloads> pscp.exe dcuser@ec2-54-88-126-85.compute-1.amazonaws.com:/home/dcuser/shell_data/untrimmed_fastq/scripted_bad_reads.txt .

C:\User\your-pc-username\Downloads
~~~
{: .bash}

<button onclick="goBack()">Go Back</button>
<hr>
