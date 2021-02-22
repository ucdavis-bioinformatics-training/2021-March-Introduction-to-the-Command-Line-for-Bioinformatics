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

# Session 3

## Manipulation of a FASTA File

We just found the phiX-174 genome, so let's copy it (using the 'cp' command) to our current directory so we can play with it:

    cp ./PhiX/Illumina/RTA/Sequence/WholeGenomeFasta/genome.fa phix.fa    

Similarly we can also use the move command here, but then ./PhiX/Illumina/RTA/Sequence/WholeGenomeFasta/genome.fa will no longer be there:

    cp ./PhiX/Illumina/RTA/Sequence/WholeGenomeFasta/genome.fa  ./PhiX/Illumina/RTA/Sequence/WholeGenomeFasta/genome2.fa
    ls ./PhiX/Illumina/RTA/Sequence/WholeGenomeFasta/
    mv ./PhiX/Illumina/RTA/Sequence/WholeGenomeFasta/genome2.fa phix.fa
    ls ./PhiX/Illumina/RTA/Sequence/WholeGenomeFasta/

This functionality of mv is why it is used to rename files. 

Note how we copied the 'genome.fa' file to a different name: 'phix.fa'

    wc -l phix.fa

count the number of lines in the file using 'wc' (word count) and parameter '-l' (lines).

We can use the 'grep' command to search for matches to patterns. 'grep' comes from '**g**lobally search for a **r**egular **e**xpression and **p**rint'.

    grep -c '>' phix.fa

Only one FASTA sequence entry, since only one header line ('>gi\|somethingsomething...')

    cat phix.fa

This may not be useful for anything larger than a virus! Let's look at the start codon and the two following codons:

    grep --color "ATG......" phix.fa

'.' characters are the single-character wildcards for grep. So "ATG......" matches any set of 9 characters that starts with ATG.

Use the --color  '-o' option to **o**nly print the pattern matches, one per line

    grep -o "ATG......" phix.fa

Use the 'cut' command with '-c' to select characters 4-6, the second codon

    grep --color  -o "ATG......" phix.fa | cut -c4-6

'sort' the second codon sequences (default order is same as ASCII table; see 'man ascii')

    grep --color  -o "ATG......" phix.fa | cut -c4-6 | sort

Combine successive identical sequences, but count them using the 'uniq' command with the '-c' option

    grep --color  -o "ATG......" phix.fa | cut -c4-6 | sort | uniq -c

Finally sort using reverse numeric order ('-rn')

    grep --color  -o "ATG......" phix.fa | cut -c4-6 | sort | uniq -c | sort -rn 

... which gives us the most common codons first

This may not be a particularly useful thing to do with a genomic FASTA file, but it illustrates the process by which one can build up a string of operations, using pipes, in order to ask quantitative questions about sequence content. More generally than that, this process allows one to ask questions about files and file contents and the operating system, and verify at each step that the process so far is working as expected. The command line is, in this sense, really a modular workflow management system.



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


## Shell Scripts, File Permissions

Often it's useful to define a whole string of commands to run on some input, so that (1) you can be sure you're running the same commands on all data, and (2) so you don't have to type the same commands in over and over! Let's use the 'nano' text editor program that's pretty reliably installed on most linux systems.

    nano test.sh

<img src="figures/cli_figure7.png" alt="cli_figure7" width="800px"/>

nano now occupies the whole screen; see commands at the bottom
type/paste in the following ...
(note that '#!' is an interpreted command to the shell, not a comment)

<div class="script">
#!/bin/bash
grep -o . $1 | \
    sort | \
    uniq -c | \
    sort -rn -k1,1
</div>

Cntrl-X top exit first saving the document. Follow the instruction at the bottom of the screen

Note that '$1' means 'the value of the 1st argument to the shell script' ... in other words, the text that follows the shell script name when we run it (see below).

Though there are ways to run the commands in test.sh right now, it's generally useful to give yourself (and others) 'execute' permissions for test.sh, really making it a shell script. Note the characters in the first (left-most) field of the file listing:

    ls -lh test.sh

<div class="output">-rw-rw-r-- 1 msettles biocore 79 Aug 19 15:05 test.sh
</div>


The first '-' becomes a 'd' if the 'file' is actually a directory. The next three characters represent **r**ead, **w**rite, and e**x**ecute permissions for the file owner (you), followed by three characters for users in the owner's group, followed by three characters for all other users. Run the 'chmod' command to change permissions for the 'test.sh' file, adding execute permissions ('+x') for the user (you) and your group ('ug'):

    chmod ug+x test.sh
    ls -lh test.sh

<div class="output">-rwxr-xr-- 1 msettles biocore 79 Aug 19 15:05 test.sh
</div>

The first 10 characters of the output represent the file and permissions. 
The first character is the file type, the next three sets of three represent the file permissions for the user, group, and everyone respectively. 
- r = read
- w = write
- x = execute

OK! So let's run this script, feeding it the phiX genome. When we put the genome file 1st after the name of the script, this filename becomes variable '1', which the script can access by specifying '$1'. We have to provide a relative reference to the script './' because its not our our "PATH".

    ./test.sh genome.fa

<div class="output">msettles@tadpole:/share/workshop/msettles/cli$ ./test.sh genome.fa
    1686 T
    1292 A
    1253 G
    1155 C
       1 x
       1 p
       1 i
       1 h
       1 >
</div>

The script's grep command splits out every character in the file on a separate line, then sorts them so it can count the occurrences of every unique character and show the most frequent characters first ... a quick and dirty way to get at GC content.


## Quiz 5

<div id="quiz5" class="quiz"></div>
<button id="submit5">Submit Quiz</button>
<div id="results5" class="output"></div>
<script>
quizContainer5 = document.getElementById('quiz5');
resultsContainer5 = document.getElementById('results5');
submitButton5 = document.getElementById('submit5');

myQuestions5 = [
  {
    question: "In the PhiX fasta file (phix.fa), find the stop codon TGA and find the 3rd codon upstream of each stop codon. What is the most common codon?",
    answers: {
      a: "GAT",
      b: "TTT",
      c: "CGC",
      d: "TGC"
    },
    correctAnswer: "d"
  },
  {
    question: "What happens when you remove a symbolic link to a file?",
    answers: {
      a: "The file is deleted",
      b: "The link is deleted, but the file is not",
      c: "The link and the file are both deleted",
      d: "You get an error"
    },
    correctAnswer: "b"
  },
  {
    question: "In your shell script change the pipeline so that it only counts the nucleotides (and not the header) AND it only counts the first 100 bases. How many A's are there?",
    answers: {
      a: "35",
      b: "30",
      c: "31",
      d: "29"
    },
    correctAnswer: "b"
  }
];

buildQuiz(myQuestions5, quizContainer5);
submitButton5.addEventListener('click', function() {showResults(myQuestions5, quizContainer5, resultsContainer5);});
</script>

