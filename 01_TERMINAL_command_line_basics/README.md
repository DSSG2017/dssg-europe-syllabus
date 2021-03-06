# Command Line 
The command line has many great advantages that can really make you a more efficient and productive data scientist. Roughly grouping the advantages, the command line is: agile, augmenting, scalable, extensible, and ubiquitous. We elaborate on each advantage below.
* Agile - read-eval-print-loop (REPL) meaning you execute a command and it is evaluated immediately, rather than a edit-compile-run-debug cycle associated with scripts.
* Augmenting - command line integrates well with other technologies. Python and R, for instance, allow you to run command-line tools and capture their output. 
* Scalable - everything that you type manually on the command line, can also be automated through scripts and tools. This makes it easy to re-run scripts.
* Extensible - functionality of command line is being extended, tools are constantly being written.
* Ubiquitous - available on all UNIX-like operating systems.

Data comes in many forms and from many sources. You may for example get a database dump or CSV files directly from a data partner, or you may need to scrape the data from the web. Either way, once you've got your hands on some data, you'll need to bring it into a database, and start cleaning and "wrangling" it. You'll definitely want to keep track of the steps to take your data from its original, raw form to being model-ready, command line tools will start tocome in handy here.

## The basics
### Where am I?
`pwd` print working directory - this prints the name of the current working directory  
`cd ..` changes directory to one level/folder up  
`cd ~/` goes to the home directory  
`cd -` return to the previous directory

### What's in my folder?
`ls` lists the contents in your current dictory.  
`ls -l` "long listing" format (`-l`) shows the filesize, date of last change, and file permissions  
`ls -l` "long listing" format (`-l`), shows all files (`-a`) including hidden dotfiles
`tree` lists the contents of the current directory and all sub-directories as a tree structure (great for peeking into folder structures!)  
`tree -L 2` limits the tree expansion to 2 levels  
`tree -hs` shows file sizes (`-s`) in human-readable format (`-h`)  

### What's in my file?  
`head -n10 $f` shows the "head" of the file, in this case the top 10 lines  
`tail -n10 $f` shows the "tail" of the file  
`tail -n10 $f | watch -n1` watches the tail of the file for any changes every second (`-n1`)  
`tail -f -n10 $f` follows (`-f`) the tail of the file every time it changes, useful if you are checking the log of a running program  
`wc $f` counts words, lines and characters in a file  (separate counts using `-w` or `-l` or `-c`)

### Where is my file?
`find -name "<lost_file_name>" -type f` finds files by name  
`find -name "<lost_dir_name>" -type d` finds directories by name  

### Renaming files
Rename files with `rename`. For example, to replace all space bars with underscores:  
`rename 's/ /_/g' space\ bars\ .txt`  

This command substitutes (`s`) space bars (`/ /`) for underscores (`/_/`) in the entire file name (globally, `g`). (The 3 slashes can be replaced by any sequence of 3 characters, so `'s# #_#g'` would also work and can sometimes be more legible, for example when you need to escape a special character with a backslash.)  

You can replace multiple characters at a time by using a simple logical OR "regular expression" (`|`) such as [ |?] which will replace every space bar or question mark.    
`rename 's/[ |?]/_/g' space\ bars?.txt`

(The file will be renamed to `space_bars_.txt`)

Bonus points:  
`rename 'y/A-Z/a-z/'` renames files to all-lowercase  
`rename 'y/a-z/A-Z/'` renames files to all-uppercase  

## Some useful things to know
* Be careful what you wish for, the command line is very powerful, it will do exactly what you ask. This can be dangerous when you're running commands like `rm` (remove), or `mv` (move). You can "echo" your commands to just print the command text without actually running the command.  
* Use tab completion to type commands faster and find filenames, press the tab key whilst typing to see suggestions  `tab`
* Prepend `man` to a command to read the manual for example `man rm`
* You can use `ctrl + r` to search the command line history, and search for previously searched commands. Or type `history` to see the history`.
* Beware of spaces when creating filenames, this is not generally good practice, if you must you can use the `\` escape character to add blank spaces in a file name. For example `touch space\ bars\ .txt`, if you run `touch space bars .txt` this will create three files `space`, `bars`, and `.txt`.
* Have a look into using `tmux` or a similar terminal multiplexer for working with multiple terminals (see further reading living-in-the-terminal).
* Use `htop` or `top` for monitoring the usage of your instance.
* Have a go at learning the basics of `vim`, since it is ubiquitous on unix servers (see further reading living-in-the-terminal).
* If you are not familiar with regular expressions, have a look at further readings (learning regular expressions the practical way).

# Using the Command line for Data Science 
If you have not yet installed the useful csvkit tool please follow the [link](https://csvkit.readthedocs.io/en/1.0.3/tutorial/1_getting_started.html#installing-csvkit) and install before doing these exercises.

### Let's talk about the weather
Since there's been so much controversy over weather predictions from paid vs free apps this year, we're going to just do it ourselves and create out own predictions using weather data from NOAA. 

You can find daily data for the US here:

`ftp://ftp.ncdc.noaa.gov/pub/data/ghcn/daily/by_year/2016.csv.gz`

(The documentation is [here](http://www1.ncdc.noaa.gov/pub/data/cdo/documentation/GHCND_documentation.pdf))

### Getting Data from the Command Line

First we have to get the data. For that we're going to use curl.

`curl ftp://ftp.ncdc.noaa.gov/pub/data/ghcn/daily/by_year/2016.csv.gz`

Whoa! Terminal is going crazy! This may impress your less savvy friends, but it's not going to help you answer your question. We need to stop this process. Try control-c. This is the universal escape command in terminal.

We obviously didn't use curl right. Let's look up the manual for the command using `man`.

`man curl`

Looks like if we want to write this to a file, we've got to pass the `-O` argument. 

`curl -O ftp://ftp.ncdc.noaa.gov/pub/data/ghcn/daily/by_year/2016.csv.gz`

Let's check to see if it worked.

`ls -la`

Great. Now we need to know the file format so we know what tool to use to unpack it.

`file 2016.csv.gz`

Looks like it's a gzip so we'll have to use `gunzip`.

`gunzip 2016.csv.gz`

`ls -la`

Now we've got a .csv file we can start playing with. Let's see how big it is using `wc`

### Viewing Data from the Command Line

The simpilest streaming command is `cat`. This dumps the whole file, line by line, into standard out and prints.

`cat 2016.csv`

That's a bit much. Let's see if we can slow it down by viewing the file page by page using `more` or `less`.

`less 2016.csv`

Great. But let's say I just want to see the top of the file to get a sense of it's structure. We can use `head` for that.

`head 2016.csv`

`head -n 3 2016.csv`

Similarly, if I'm only interested in viewing the end of the file, I can use `tail`.

`tail 2016.csv`

These commands all print things out raw and bunched together. I want to take advantage of the fact that I know this is a csv to get a prettier view of the data. This is where `csvkit` starts to shine. The first command we'll use from csvkit is `csvlook`.

`csvlook 2016.csv`

But that's everything again. We just want to see the top. If only we could take the output from `head` and send it to `csvlook`. 

We can! It's called *piping*, and you do it like this:

`head 2016.csv | csvlook`

The output from `head` was sent to `csvlook` for processing. Piping and redirection (more on that later) are two of the most important concepts to keep in mind when using command line tools. Because most commands use text as the interface, you can chain commands together to create simple and powerful data processing pipelines!


### Filtering Data from the Command Line

It looks like in order for us to make sense of the weather dataset, we're going to need to figure out what these station numbers mean. Let's grab the station dictionary from NOAA and take a look at it.

`curl -O https://www1.ncdc.noaa.gov/pub/data/ghcn/daily/ghcnd-stations.txt`

`head ghcnd-stations.txt`

Looks like the sation description might come in handy. We want to look at just the stations in Chicago.

`grep CHICAGO ghcnd-stations.txt | csvlook -H`

Let's pick OHARE as the station we'll use for now. Its ID is 'USW00094846'

Let's take a look at just the ID column from the weather file. We can do this using `cut`.

`cut -f 1 2016.csv`

Looks like `cut` isn't smart enough to know that we're using a csv. We can either use csvcut, or pass a delimiter argument that specifies comma.

`cut -d , -f 1 2016.csv | head`

Now let's filter out just the oberservations from OHARE.

`cut -d , -f 1 2016.csv | grep USW00094846 | head`

Another powerful tool that can do filtering (and much more) is `awk`. `awk` treats every file as a set of row-based records and allows you to create contition/{action} pairs for the records in the file. The default {action} in `awk` is to print the records that meet the condition. Let's try reproducing the above statement using `awk`.

`cut -d , -f 1 2016.csv | awk '/USW00094846/' | head`

`awk` requires familiarity with regular expressions for contitions and has its own language for actions, so `man` and stack overflow will be your friends if you want to go deep with `awk`.

### Editing and Transforming Data
Let's say we want to replace values in the files. PRCP is confusing. Let's change PRCP to RAIN. 

To do this, we use `sed`. `sed` stands for streaming editor, and is very useful for editing large text files because it doesn't have to load all the data into memory to make changes. Here's how we can use `sed` to replace a string.

`sed s/PRCP/RAIN/ 2016.csv | head`

Notice the strings have changed!

But when we look at the source file

`head 2016.csv`

Noting has changed. That's because we didn't write it to a file. In fact, none of the changes we've made have. 

`sed s/PRCP/RAIN/ 2016.csv > 2016_clean.csv`

`head 2016_clean.csv`

We can also use awk for subsitution, but this time, let's replace "WSFM" with "WINDSPEED" in all the weather files in the directory. Once again, stackoverflow is your friend here. 

`ls -la > files.txt`

`awk '$9 ~/2016*/ {gsub(/WSFM/, "WINDSPEED"); print;}' files.txt`


# Further Reading:
To learn more about useful command line tools for data science and shell scripting review the following:
* https://github.com/dssg/hitchhikers-guide/tree/master/curriculum/1_getting_and_keeping_data/command-line-tools 
* https://github.com/dssg/hitchhikers-guide/tree/master/curriculum/4_programming_best_practices/living-in-the-terminal
* https://medium.com/@kadek/command-line-tricks-for-data-scientists-c98e0abe5da
* https://hugogiraudel.com/2015/08/19/learning-regular-expressions-the-practical-way/
