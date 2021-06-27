# Golden Rules of Coding (for Classwork)
###### Sarah Jewett | PhD Candidate | London School of Economics


The following tips and instructions are based on common errors students have made in the past. The benefit of following them is in becoming a strong coder, easy to collaborate with, and more prone to get partial credit for incorrect answers in assignments.  

## Notable Functions

Ingrain these in your coding practice as they will become your good friends and make your instructors very happy:


##### suppressMessages()

This will eliminate the messages from loading your packages in your document.

##### options(scipen = 999)

This disables scientific notation in R, which can sometimes appear at inconvenient times (such as in plots) or with one variable of the data but not others. 

##### head() and tail()

This will give you the first and last parts of the data.

   

------------------------

## Good Coding Practices
There are packages like lintr() that help with spelling and continuity in code style within R. 
There are also style guides you can follow:

[RStudio Style Guide](https://rstudio-pubs-static.s3.amazonaws.com/390511_286f47c578694d3dbd35b6a71f3af4d6.html)    |    [tidyverse Style Guide](https://style.tidyverse.org)    |    [Google R Style Guide](https://google.github.io/styleguide/Rguide.html)


##### Naming Variables
Try to name your variables and data something descriptive and avoid meaningless numbers. As your code gets more complicated, it's vital not to have to keep scrolling up to remember what variable is doing or means what. 


##### Comment Your Code
Think about how hard it is to learn new code without any comments, and imagine having to do that over and over. 

  * Comenting your code ensures:
    + The grader knows your intent behind the code and easily correct you if you are mistaken. 
    + Signals you understood what was requested and the point of the exercise.
    + A quick understanding of what you did if you need to revisit it in order to repeat an approach later on. 




##### Show Your Work

  * Don’t assume the person grading your work will know what your data look like from your code alone. Use head() or other functions to illustrate how your data manipulation/scraping progressed. 
  
  * **NEVER** print the entirety of your large data frames, it defeats the purpose of working in R and creates endless scrolling. This is a huge mistake!
  
  * Hidden data + unreproducible code = suspicious grader
  

------------------------

## Presentation of Findings

##### Imagine your submission as if you will use it to present in a seminar or formal academic setting
       
  * Would incomplete data or poor visuals be acceptable in these contexts? **NO.**
      + *Example 1:* The question asks for the name of someone, but your output gives an ID number because you haven’t finished cleaning or linking data. 
      + *Example 2:* A plot is missing labels or legends/keys, requiring a written answer to interpret. 
  * Visualizations need to be mostly interpretable as a stand-alone.  

 

##### Interpret Your Results

  * A question requiring you to explain results does not mean repeating the output in a sentence.
      + *Bad:* The correlation is -.002 and non-significant.
      + *Better: *The correlation of -.002 is weak. The lack of statistical significance underlines that there is no relationship or correlation between the two variables of interest.
      
  * Do not assume that your data visualization conveys statistical significance! You could have a plot that suggests a strong correlation and an impressive finding, but a significance test shows there is no such significance after all. 

------------------------
## Don't Rush or Wait to Code at the Last Minute

Think of your brain like a computer -- it can process tasks in the background, and that includes figuring out how to get unstuck in R!

But, unlike writing an essay, when code doesn't run, there is no fudging it. Most students, particularly those new to R, don't thrive coding under last-minute pressure, particularly because of this. 

Rushing compromises your ability to learn how to code properly, without cutting corners. 

It also means that when you are stuck earlier on, the instructors are available to help you. We can't, however, come to your rescue at 2am the night before the deadline. 



## Last but not least... when in doubt, Restart R and make sure R and RStudio are up to date!