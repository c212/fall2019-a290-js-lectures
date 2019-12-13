# Final Exam Explanation/In-Class #
This will go over what we talk about in class.  

## What we Need ##
* code (all files in your github.com/c212/fall2019-js-... repository).  
* instructions (the steps I need to take to make your code run for me).  
  * This can be as the README.md or as its own file titled "Instructions".  

## Step 1: Angular ##
Follow [the tutorial for Angular](https://www.w3schools.com/angular/ "Angular Tutorial") to have an understanding of it.   
Then, [checkout this application](https://www.w3schools.com/angular/angular_application.asp) from the tutorial.  

## Step 2: MongoDB ##
Add a MongoDB backend to it, such as we did [on November 20th in class](http://silo.cs.indiana.edu:8346/a290-web/fall2016/0929a.phps "MongoDB").  

## Notes ##
* You may have to switch between node 6 and node 10, depending on what you are working on. Checkout the [whats new from december 3rd](https://cs.indiana.edu/classes/a290-js/fall2019/whatsnew.html "Whats new").  

## Common Issues (and Fixes) ##
Below is a list of common issues and solutions to help you.  
### Incorrect Node Version ###
You may run into an issue where you need to switch versions of node. First, determine which version of node you are using:  
`$ node -v`  
Your version will be printed to the terminal.  
  
If you are using version 6.17.1 and want to switch to 10.14.1:  
`$ export PATH=/l/www/classes/a290-js/software/local/bin:$PATH`  
If you are using version 10.14.1 and want to switch to 6.17.1:  
`$ export PATH=/usr/bin:$PATH`  
  
You can also check the path that node is using by the command: `$ which node`.  
  
### I can't find my nobackup folder ###
You may need to create your nobackup folder if you did not do so before. To do this:  
`$ makenobackup` and then reply `yes`.  
Now, if you just logged into *silo*, your current working directory will be `/u/yourUsername/`.  
**The nobackup folder is not located here.** It is located in `/nobackup/yourUsername/`.  
To get there from your home directory, you can use this:  
`$ cd ../..`  
`$ cd nobackup/yourUsername/`  


Let dgerman@indiana.edu know if you have any questions.
