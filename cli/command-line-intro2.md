<script>
function buildQuiz(myq, qc){
  // variable to store the HTML output
  const output = [];

  // for each question...
  myq.forEach(
    (currentQuestion, questionNumber) => {

      // variable to store the list of possible answers
      const answers = [];

      // and for each available answer...
      for(letter in currentQuestion.answers){

        // ...add an HTML radio button
        answers.push(
          `<label>
            <input type="radio" name="question${questionNumber}" value="${letter}">
            ${letter} :
            ${currentQuestion.answers[letter]}
          </label><br/>`
        );
      }

      // add this question and its answers to the output
      output.push(
        `<div class="question"> ${currentQuestion.question} </div>
        <div class="answers"> ${answers.join('')} </div><br/>`
      );
    }
  );

  // finally combine our output list into one string of HTML and put it on the page
  qc.innerHTML = output.join('');
}

function showResults(myq, qc, rc){

  // gather answer containers from our quiz
  const answerContainers = qc.querySelectorAll('.answers');

  // keep track of user's answers
  let numCorrect = 0;

  // for each question...
  myq.forEach( (currentQuestion, questionNumber) => {

    // find selected answer
    const answerContainer = answerContainers[questionNumber];
    const selector = `input[name=question${questionNumber}]:checked`;
    const userAnswer = (answerContainer.querySelector(selector) || {}).value;

    // if answer is correct
    if(userAnswer === currentQuestion.correctAnswer){
      // add to the number of correct answers
      numCorrect++;

      // color the answers green
      answerContainers[questionNumber].style.color = 'lightgreen';
    }
    // if answer is wrong or blank
    else{
      // color the answers red
      answerContainers[questionNumber].style.color = 'red';
    }
  });

  // show number of correct answers out of total
  rc.innerHTML = `${numCorrect} out of ${myq.length}`;
}
</script>

# Session 2

## History Repeats Itself

Linux remembers everything you've done (at least in the current shell session), which allows you to pull steps from your history, potentially modify them, and redo them. This can obviously save a lot of time and typing.

The 'head' command views the first 10 (by default) lines of a file. The 'tail' commands views the last 10 (by default) lines of a file. Type 'man head' or 'man tail' to consult their manuals.

    <up arrow>  # last command
    <up>  # next-to-last command
    <down>  # last command, again
    <down>  # current command, empty or otherwise
    history  # usually too much for one screen, so ...
    history | head # we discuss pipes (the vertical bar) below
    history | tail
    history | less # use 'q' to exit less
    ls -l
    pwd
    history | tail
    !560  # re-executes 560th command (yours will have different numbers; choose the one that recreates your really important result!)

## Editing Yourself

Here are some more ways to make editing previous commands, or novel commands that you're building up, easier:

    <up><up>  # go to some previous command, just to have something to work on
    <ctrl-a>  # go to the beginning of the line
    <ctrl-e>  # go to the end of the line
    # now use left and right to move to a single word (surrounded by whitespace: spaces or tabs)
    <ctrl-k>  # delete from here to end of line
    <ctrl-w>  # delete from here to beginning of preceeding word
    blah blah blah<ctrl-w><ctrl-w>  # leaves you with only one 'blah'

You can also search your history from the command line:

    <ctrl-r>fir  # should find most recent command containing 'fir' string: echo 'first' > test.txt
    <enter>  # to run command
    <ctrl-c>  # get out of recursive search
    <ctr-r>  # repeat <ctrl-r> to find successively older string matches


## Create and Destroy

We already learned one command that will create a file, touch. Lets create a folder in /share/workshop for you to work in and then another directory cli. We will use the environment variable $USER, that contains your username.

    cd  # home again
    echo $USER # echo to screen the contents of the variable $USER
    mkdir ~/tmp2
    cd ~/tmp2
    echo 'Hello, world!' > first.txt

echo text then redirect ('>') to a file.

    cat first.txt  # 'cat' means 'concatenate', or just spit the contents of the file to the screen

why 'concatenate'? try this:

    cat first.txt first.txt first.txt > second.txt
    cat second.txt

OK, let's destroy what we just created:

    cd ../
    rmdir tmp2  # 'rmdir' meands 'remove directory', but this shouldn't work!
    rm tmp2/first.txt
    rm tmp2/second.txt  # clear directory first
    rmdir tmp2  # should succeed now

So, 'mkdir' and 'rmdir' are used to create and destroy (empty) directories. 'rm' to remove files. To create a file can be as simple as using 'echo' and the '>' (redirection) character to put text into a file. Even simpler is the 'touch' command.

    mkdir ~/cli
    cd ~/cli
    touch newFile
    ls -ltra  # look at the time listed for the file you just created
    cat newFile  # it's empty!
    sleep 60  # go grab some coffee
    touch newFile
    ls -ltra  # same time?

So 'touch' creates empty files, or updates the 'last modified' time. Note that the options on the 'ls' command you used here give you a Long listing, of All files, in Reverse Time order (l, a, r, t).

## Forced Removal

When you're on the command line, there's no 'Recycle Bin'. Since we've expanded a whole directory tree, we need to be able to quickly remove a directory without clearing each subdirectory and using 'rmdir'.

    cd
    mkdir -p rmtest/dir1/dir2 # the -p option creates all the directories at once
    rmdir rmtest # gives an error since rmdir can only remove directories that are empty
    rm -rf rmtest # will remove the directory and EVERYTHING in it

Here -r = recursively remove sub-directories, -f means *force*. Obviously, be careful with 'rm -rf', there is no going back, if you delete something with rm, rmdir its gone! **There is no Recycle Bin on the Command-Line!**

## Quiz 3

<div id="quiz3" class="quiz"></div>
<button id="submit3">Submit Quiz</button>
<div id="results3" class="output"></div>
<script>
quizContainer3 = document.getElementById('quiz3');
resultsContainer3 = document.getElementById('results3');
submitButton3 = document.getElementById('submit3');

myQuestions3 = [
  {
    question: "In the command 'rm -rf rmtest', what is 'rmtest'?",
    answers: {
      a: "An option",
      b: "An argument",
      c: "A command",
      d: "A choice"
    },
    correctAnswer: "b"
  },
  {
    question: "Make a directory called test and then run 'rm test'. What happens?",
    answers: {
      a: "Nothing happens",
      b: "The directory is removed",
      c: "The terminal exits",
      d: "You get an error message"
    },
    correctAnswer: "d"
  },
  {
    question: "Use ls to find the size (in bytes) of the last file in the /sbin directory.",
    answers: {
      a: "33211",
      b: "77064",
      c: "1058216",
      d: "1103"
    },
    correctAnswer: "b"
  }
];

buildQuiz(myQuestions3, quizContainer3);
submitButton3.addEventListener('click', function() {showResults(myQuestions3, quizContainer3, resultsContainer3);});
</script>

## Piping and Redirection

Pipes ('\|') allow commands to hand output to other commands, and redirection characters ('>' and '>>') allow you to put output into files.

    echo 'first' > test.txt
    cat test.txt # outputs the contents of the file to the terminal
    echo 'second' > test.txt
    cat test.txt
    echo 'third' >> test.txt
    cat test.txt

The '>' character redirects output of a command that would normally go to the screen instead into a specified file. '>' overwrites the file, '>>' appends to the file.

The 'cut' command pieces of lines from a file line by line. This command cuts characters 1 to 3, from every line, from file 'test.txt'

    cut -c 1-3 test.txt  

same thing, piping output of one command into input of another

    cat test.txt | cut -c 1-3  

This pipes (i.e., sends the output of) cat to cut to sort (-r means reverse order sort), and then grep searches for pattern ('s') matches (i.e. for any line where an 's' appears anywhere on the line.)

    cat test.txt | cut -c 1-3 | sort -r
    cat test.txt | cut -c 1-3 | sort -r | grep s

This is a great way to build up a set of operations while inspecting the output of each step in turn. We'll do more of this in a bit.


## Compression and Archives

As file sizes get large, you'll often see compressed files, or whole compressed folders. Note that **any good bioinformatics software** should be able to work with compressed file formats. 

    gzip test.txt
    cat test.txt.gz

To uncompress a file

    gunzip -c test.txt.gz

The '-c' leaves the original file alone, but dumps expanded output to screen

    gunzip test.txt.gz  # now the file should change back to uncompressed test.txt

Tape archives, or .tar files, are one way to compress entire folders and all contained folders into one file. When they're further compressed they're called 'tarballs'. We can use wget (web get).

    wget http://igenomes.illumina.com.s3-website-us-east-1.amazonaws.com/PhiX/Illumina/RTA/PhiX_Illumina_RTA.tar.gz

The .tar.gz and .tgz are *commonly used* extensions for compressed tar files, when gzip compression is used. The application tar is used to uncompress .tar files

    tar -xzvf PhiX_Illumina_RTA.tar.gz

Here -x = extract, -z = use gzip/gunzip, -v = verbose (show each file in archive), -f filename

Note that, unlike Windows, linux does not depend on file extensions to determine file behavior. So you could name a tarball 'fish.puppy' and the extract command above should work just fine. The only thing that should be different is that tab-completion doesn't work within the 'tar' command if it doesn't see the 'correct' file extension.    


## BASH Wildcard Characters

We can use 'wildcard characters' when we want to specify or operate on sets of files all at once.

    ls ?hiX/Illumina

list files in Illumina sub-directory of any directory ending in 'hiX'

    ls PhiX/Illumina/RTA/Sequence/*/*.fa

list all files ending in '.fa' a few directories down. So, '?' fills in for zero or one character, '\*' fills in for zero or more characters. The 'find' command can be used to locate files using a similar form.

    find . -name "*.f*"
    find . -name "*.f?"

how is this different from the previous ls commands?

#### Quick Note About the Quote(s)

The quote characters " and ' are different. In general, single quotes preserve the *literal* meaning of all characters between them. On the other hand, double quotes allow the shell to see what's between them and make substitutions when appropriate. For example:

    VRBL=someText
    echo '$VRBL'
    echo "$VRBL"

However, some commands try to be 'smarter' about this behavior, so it's a little hard to predict what will happen in all cases. It's safest to experiment first when planning a command that depends on quoting ... list filenames first, instead of changing them, etc. Finally, the 'backtick' characters \` (same key - unSHIFTED - as the tilde ~) causes the shell to interpret what's between them as a command, and return the result.

     # counts the number of lines in file and stores result in the LINES variable
    LINES=`cat PhiX/Illumina/RTA/Sequence/Bowtie2Index/genome.1.bt2 | wc -l` 
    echo $LINES


## Symbolic Links

Since copying or even moving large files (like sequence data) around your filesystem may be impractical, we can use links to reference 'distant' files without duplicating the data in the files. Symbolic links are disposable pointers that refer to other files, but behave like the referenced files in commands. I.e., they are essentially 'Shortcuts' (to use a Windows term) to a file or directory.

The 'ln' command creates a link. **You should, by default, always create a symbolic link using the -s option.**

    ln -s PhiX/Illumina/RTA/Sequence/WholeGenomeFasta/genome.fa .
    ls -ltrhaF  # notice the symbolic link pointing at its target
    grep -c ">" genome.fa

## STDOUT & STDERR

Programs can write to two separate output streams, 'standard out' (STDOUT), and 'standard error' (STDERR). The former is generally for direct output of a program, while the latter is supposed to be used for reporting problems. I've seen some bioinformatics tools use STDERR to report summary statistics about the output, but this is probably bad practice. Default behavior in a lot of cases is to dump both STDOUT and STDERR to the screen, unless you specify otherwise. In order to nail down what goes where, and record it for posterity:

    wc -c genome.fa 1> chars.txt 2> any.err

the 1st output, STDOUT, goes to 'chars.txt'  
the 2nd output, STDERR, goes to 'any.err'  

    cat chars.txt

Contains the character count of the file genome.fa

    cat any.err

Empty since no errors occured.

Saving STDOUT is pretty routine (you want your results, yes?), but remember that explicitly saving STDERR is important on a remote server, since you may not directly see the 'screen' when you're running jobs.


## Quiz 4

<div id="quiz4" class="quiz"></div>
<button id="submit4">Submit Quiz</button>
<div id="results4" class="output"></div>
<script>
quizContainer4 = document.getElementById('quiz4');
resultsContainer4 = document.getElementById('results4');
submitButton4 = document.getElementById('submit4');

myQuestions4 = [
  {
    question: "In the PhiX directory, count the number of the files ending in '.fa'. You will need to use the 'wc' command:",
    answers: {
      a: "15",
      b: "9",
      c: "7",
      d: "10"
    },
    correctAnswer: "d"
  },
  {
    question: "Which of the following commands will list all of the txt files in all the 'Genes' directories underneath the 'Archives' directory?",
    answers: {
      a: "ls PhiX/Illumina/RTA/*o*/Archives/*/*/*.txt",
      b: "ls PhiX/Illumina/RTA/A*/Archives/*/*.txt",
      c: "ls PhiX/Illumina/RTA/S*/Archives/*/*/*.txt",
      d: "ls PhiX/Illumina/RTA/Annotation/Genes/*.text"
    },
    correctAnswer: "a"
  },
  {
    question: "Find 'genome.fa' file in the PhiX directory. Use pipes to get characters 640 to 700 in the second line of the file:",
    answers: {
      a: "GCTCGTCGCTGCGTTGAGGCTTGCGTTTATGGTACGCTGGACTTTGTAGGATACCCTCGCT",
      b: "TATTAAGCTCATTCAGGCTTCTGCCGTTTTGGATTTAACCGAAGATGATTTCGATTTTCTG",
      c: "ATTATGTTCATCCCGTCAACATTCAAACGGCCTGTCTCATCATGGAAGGCGCTGAATTTAC",
      d: "GGATTACTATCTGAGTCCGATGCTGTTCAACCACTAATAGGTAAGAAATCATGAGTCAAGT"
    },
    correctAnswer: "c"
  }
];

buildQuiz(myQuestions4, quizContainer4);
submitButton4.addEventListener('click', function() {showResults(myQuestions4, quizContainer4, resultsContainer4);});
</script>


The sed command
----------------

Let's take a look at the 'sed' command. sed (short for stream editor) is a command that allows you to manipulate character data in various ways. One useful thing it can do is substitution. First, make a directory called "advanced" in your user directory and go into it. The "$USER" variable contains your username.

    cd /share/workshop/prereq_workshop/$USER/
    mkdir advanced
    cd advanced/

Let's download a simple file to work on:

    wget https://raw.githubusercontent.com/ucdavis-bioinformatics-training/2020-Bioinformatics_Prerequisites_Workshop/master/Advanced_CLI/region.bed -O region.bed

Take a look at the file:

    cat region.bed

Now, let's make all the uppercase "CHR"s into lowercase:

    cat region.bed | sed 's/CHR/chr/'

What happened? Only the first CHR changed. That is because we need to add the "g" option:

    cat region.bed | sed 's/CHR/chr/g'

We can also do the the substitution without regards to case:

    cat region.bed | sed 's/chr/chr/gi'

Let's break down the argument to sed (within the single quotes)... The "s" means "substitute", the word between the 1st and 2nd forward slashes (i.e. /) is the word the substitute for, the word between the 2nd and 3rd slashes is the word to substitute with, and finally the "gi" at the end are flags for global substitution (i.e. substituting along an entire line instead of just the first occurence on a line), and for case insenstivity (i.e. it will ignore the case of the letters when doing the substitution).

Note that this **doesn't** change the file itself, it is simply piping the output of the cat command to sed and outputting to the screen. If you wanted to change the file itself, you could use the "-i" option to sed:

    cat region.bed
    sed -i 's/chr/chr/gi' region.bed

Now if you look at the file, the lines have changed.

    cat region.bed

Another useful use of sed is for capturing certain lines from a file. You can select certain lines from a file:

    sed '4q;d' region.bed

This will just select the 4th line from the file.

You can also extract a range of lines from a file:

    sed -n '10,20p' region.bed

This gets the 10th through 20th lines from the file.

**CHALLENGE:**
See if you can find a way to use sed to remove all the "CHR"s from the file.

More pipes
-----------

Now, let's delve into pipes a little more. Pipes are a very powerful way to look at and manipulate complex data using a series of simple programs. Let's look at some fastq files. Get a few small fastq files:

    wget https://github.com/ucdavis-bioinformatics-training/2021-March-Introduction-to-the-Command-Line-for-Bioinformatics/raw/main/cli/C61.subset.fq.gz
    https://github.com/ucdavis-bioinformatics-training/2021-March-Introduction-to-the-Command-Line-for-Bioinformatics/raw/main/cli/I561.subset.fq.gz
    https://github.com/ucdavis-bioinformatics-training/2021-March-Introduction-to-the-Command-Line-for-Bioinformatics/raw/main/cli/I894.subset.fq.gz
    
Since the files are gzipped files we need to use "zcat" to look at them. zcat is just like cat except for gzipped files:

    zcat C61.subset.fq.gz | head

Fastq records are 4 lines per sequence, a header line, the sequence, a plus sign (which is historical), and then the quality encoding for the sequence. Notice that each header line has the barcode for that read at the end of the line. Let's count the number of each barcode. In order to do that we need to just capture the header lines from this file. We can use "sed" to do that:

    zcat C61.subset.fq.gz | sed -n '1~4p' | head

By default sed prints every line. In this case we are giving the "-n" option to sed which will **not** print every line. Instead, we are giving it the argument "1\~4p", which means to print the first line, then skip 4 lines and print again, and then continue to do that.

Now that we have a way to get just the headers, we need to isolate the part of the header that is the barcode. There are multiple ways to do this... we will use the cut command:

    zcat C61.subset.fq.gz | sed -n '1~4p' | cut -d: -f10 | head

So we are using the "-d" option to cut with ":" as the argument to that option, meaning that we will be using the delimiter ":" to split the input. Then we use the "-f" option with argument "10", meaning that we want the 10th field after the split. In this case, that is the barcode.

Finally, as before, we need to sort the data and then use "uniq -c" to count. Then put it all together and run it on the entire dataset (This will take about a minute to run):

    zcat C61.subset.fq.gz | sed -n '1~4p' | cut -d: -f10 | sort | uniq -c

Now you have a list of how many reads were categorized into each barcode. Here is a [sed tutorial](https://www.digitalocean.com/community/tutorials/the-basics-of-using-the-sed-stream-editor-to-manipulate-text-in-linux) for more exercises.

**CHALLENGE:**
Find the distribution of the first 5 bases of all the reads in C61_S67_L006_R1_001.fastq.gz. I.e., count the number of times the first 5 bases of every read occurs across all reads.


Loops
------

Loops are useful for quickly telling the shell to perform one operation after another, in series. For example:

    for i in {1..21}; do echo $i >> a; done  # put multiple lines of code on one line, each line terminated by ';'
    cat a
    # <1 through 21 on separate lines>

The general form is:

<div class="output">for name in {list}; do
    commands
done
</div>

The list can be a sequence of numbers or letters, or a group of files specified with wildcard characters:

    for i in {3,2,1,liftoff}; do echo $i; done  # needs more excitement!
    for i in {3,2,1,"liftoff!"}; do echo $i; done  # exclamation point will confuse the shell unless quoted

 A "while" loop is more convenient than a "for" loop ... if you don't readily know how many iterations of the loop you want:

<div class="output">while {condition}; do
    commands
done
</div>

Now, let's do some bioinformatics-y things with loops and pipes. First, let's write a command to get the nucleotide count of the first 10,000 reads in a file. Use zcat and sed to get only the read lines of a file, and then only take the first 10,000:

    zcat C61.subset.fq.gz | sed -n '2~4p' | head -10000 | less

Use grep's "-o" option to get each nucleotide on a separate line (take a look at the man page for grep to understand how this works):

    zcat C61.subset.fq.gz | sed -n '2~4p' | head -10000 | grep -o . | less

Finally, use sort and uniq to get the counts:

    zcat C61.subset.fq.gz | sed -n '2~4p' | head -10000 | grep -o . | sort | uniq -c

<div class="output"> 264012 A
 243434 C
 215045 G
    278 N
 277231 T
</div>

And, voila, we have the per nucleotide count for these reads!

We just did this for one file, but what if we wanted to do it for all of our files? We certainly don't want to type the command by hand dozens of times. So we'll use a while loop. You can pipe a command into a while loop and it will iterate through each line of the input. First, get a listing of all your files:

    ls -1 *.fq.gz

Pipe that into a while loop and read in the lines into a variable called "x". We use "$x" to get the value of the variable in that iteration of the loop:

    ls -1 *.fq.gz | while read x; do echo $x is being processed...; done

Add the command we created above into the loop, placing $x where the filename would be and semi-colons inbetween commands:

    ls -1 *.fq.gz | while read x; do echo $x is being processed...; zcat $x | sed -n '2~4p' | head -10000 | grep -o . | sort | uniq -c; done

When this runs it will print the name of every single file being processed and the nucleotide count for the reads from those files.

Now, let's say you wanted to write the output of each command to a separate file. We would redirect the output to a filename, but we need to create a different file name for each command and we want the file name to reflect its contents, i.e. the output file name should be based on the input file name. So we use "parameter expansion", which is fancy way of saying substitution:

    ls -1 *.fq.gz | while read x; do echo $x is being processed...; zcat $x | sed -n '2~4p' | head -10000 | grep -o . | sort | uniq -c > ${x%.fastq.gz}.nucl_count.txt; done

This will put the output of the counting command into a file whose name is the prefix of the input file plus ".nucl_count.txt". It will do this for every input file.