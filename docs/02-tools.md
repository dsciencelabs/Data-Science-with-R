# Get Your Tools Ready {#tools}


\begin{center}\includegraphics[width=0.6\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/tools} \end{center}

>
>
>           工欲善其事，必先利其器
>
>   An artisan must first sharpen his tools if he is to do 
>   his work well.
>
>
>                            -- 孔子《论语》
>                              Confucius <Analects>
>

Since this book is "Do Data Science ...". It means learn data science by doing. First of all, we need to get our weaponry or tools ready.

We already knew that there is a list of tools used by data scientists. Apart from personal preference, the most used tool is R. This book will use R as the tool to do a complete data science project. However this is not an R language book[@Black2021], it will not teach you about R language and how to use it. It will simply demonstrate a data science project completion step by step, which is completed with R language[@Wickham2021]. 

By doing, I mean that you can simply mimic what I have done and follow along by typing or copy paste my code into your working space, observe the effects and the results of each line of code execution. Thinking of why I have to do this and what results can I expect along the line of the data science project's process. monitoring the issue raised and the methods used to resolve the issues. It is a hope that at some points you can have your own thoughts, perhaps your own code, methods, and experiments. Once that is achieved. the goals are reached.

## Brief introductiuon about R and RStudio

R is one of the most widely used programming languages for statistical modelling [@Dalpiaz2021]. It has become the lingua franca of Data Science. Being open-source, R enjoys community support of avid developers who work on releasing new packages, updating R, and making it a steady and fast programming package for Data Science[@Wickham2021].  

### Features of R Programming {-}

R Programming has the following features:

 + R is a comprehensive programming language that provides support for procedural programming involving functions as well as object-oriented programming with generic functions.
 + R can be extended easily. There are over 10,000 packages in the repository of R programming. With these packages, one can make use of extended functions to facilitate easier programming.
+ Being an interpreter-based language, R produces a machine-independent code that is portable in nature. Furthermore, it facilitates easy debugging of the code.
+ R supports complex operations with vectors, arrays, data frames as well as other data objects that have varying sizes. 
+ R can be easily integrated with many other technologies and frameworks like Hadoop and Spark. It can also integrate with other programming languages like C, C++, Python, Java, FORTRAN, and JavaScript.
+ R provides robust facilities for data handling and storage.
As discussed in the above section, R has extensive community support that provides technical assistance, seminars, and several boot camps to get you started with R.
+ R is cross-platform compatible. R packages can be installed and used on any OS in any software environment without any changes.

### R Scripts {-}

R is the primary statistical programming language for performing modelling and graphical tasks. so it can run in the command line as an interpreting language. However, With its extensive support for performing increasingly complex computations such as manipulations on matrix and dataframe\index{dataframe}, R is now mostly running in the script for a variety of tasks that involve complex datasets with complex operations.

There is plenty of editing tools that perform interactions with the native R console. With any one of them, you can edit and run R script. You can also simply import extra packages and use the provided functions to achieve results with minimal number lines of code. There are several editors and IDEs that facilitate GUI features for authoring and executing R scripts. Some of the useful editors that support the R programming language are RGui (R Graphical User Interface) and RStudio\index{RStudio}, an integrated R script development environment. 

This book will NOT teach you how to code in R. Learning R and to code in R language is not so hard. It just requires a lot of trials and time-spending. You can always go online and searching on Google, Baidu, or StackOverflow (https://stackoverflow.com/). There are also plenty of examples and codes. The chances are if you’re trying to figure out how to do something in R, other people have tried as well, so rather than banging your head against the wall, look online. There are also some books available to help you out on this front as well. I suggest looking at other people’s code and run it to see the results. R manual (https://cran.r-project.org/manuals.html) is always handy and is available.

If you want to learn R systematically, there are many sources online providing good tutorials. You can try to learn more R language from R tutorials. Tutorialspoint (http://www.tutorialspoint.com/r/index.htm), Codecademy (https://www.codecademy.com/). If you prefer an online interactive environment to learn R, this free R tutorial by  DataCamp (https://www.datacamp.com/courses/free-introduction-to-r) is a great way to get started.

### R Graphical User Interface (RGui) {-}

RGui is a standard GUI\index{GUI} (Graphic User Interface) platform that comes with an R release. By default, it provides two windows: R Console (on the left) and R Editor (on the right). See: Figure \@ref(fig:rgui) 

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{Capture/RGui} 

}

\caption{Screen capture of RGui: where Console i son the left and Editor is on the right}(\#fig:rgui)
\end{figure}

**R Console** is an essential part of the RGui. In this window, we input various instructions, commands, and scripts for different operations. The results of any operation or instruction execution are displayed at the console window including warning and error messages. Console window utilizes several other useful tools embedded to facilitate and ease various operations. The console window appears whenever you access the RGui. 

**R Editor** is a simple built-in text editor. Where you can create a new R script, edit, test, and debug the script and save it into a file. To lunch R Editor, in the main panel of RGui, go to the "`File`" menu and select the "`New Script`" option. This will lunch R Editor and allow you to create a new script in R. R Editor has a function of "`Run line or selection`". It means you can debug your code by line or selection. It is a very convenient tool for debugging.  

### RStudio {-}

RStudio \index{RStudio}(https://rstudio.com/products/rstudio/) is an integrated and comprehensive Integrated Development Environment (IDE)\index{IDE} for R. It facilitates extensive code editing, debugging, and development. It includes a console, syntax-highlighting editor that supports direct code execution, as well as tools for plotting, history, debugging, and workspace management. Figure \@ref(fig:rstudio) is a screen shot of the RStudio. 

\begin{figure}

{\centering \includegraphics[width=0.9\linewidth]{Capture/RStudio} 

}

\caption{Screen capture of RStudio with integrated R code developemtn environment}(\#fig:rstudio)
\end{figure}

Here are some distinctive features provided by the RStudio:

+ **An IDE that was built just for R**.  With Syntax highlighting, code completion, and smart indentation. It can execute R code directly from the source editor. it can quickly jump to function definitions
+ **Bring your workflow together**. Integrated R help and documentation with easily manage multiple working directories using projects and Workspace browser and data viewer
+ **Powerful authoring & Debugging**. Interactive debugger to diagnose and fix errors quickly and extensive package development tools can authoring with Sweave\index{Sweave} and R Markdown\index{Markdown}

RStudio is available in open source and commercial editions and runs on the desktop (Windows, Mac, and Linux) or in a browser connected to RStudio Server or RStudio Server Pro.

We will use RStudio for the whole book. The detailed RStudio IDE is explained in (https://rstudio.com/products/rstudio/).

## Downlaod and Install R and RStudio

It is simple to download and install both R and RStudio.

### R Download and Installation {-}

To download R, please either directly from here (http://cran.us.r-project.org/bin/windows/base) or your preferred CRAN mirror (https://cran.r-project.org/mirrors.html). If you have questions about R like how to download and install the software, or what the license terms are, please read the answers to frequently asked questions (http://cran.r-project.org/faqs.html).  

Once you have chosen a site and click the download, you will will see Figure \@ref(fig:rd),

\begin{figure}

{\centering \includegraphics[width=0.8\linewidth]{Capture/R} 

}

\caption{Screen capture of R dowanload papge from CRAN}(\#fig:rd)
\end{figure}

Pick up your platform and download the latest version (`4.0.2`), follow instructions to install it (assume you choose Windows). In Windows, double click downloaded executable file, you will see this (as shown in Figure \@ref(fig:rinstall)),

\begin{figure}

{\centering \includegraphics[width=0.6\linewidth]{Capture/Rinstall} 

}

\caption{Screen capture of R install in Windows}(\#fig:rinstall)
\end{figure}

Click '`Run`', and answer the security message with '`Yes`'. Choose your language (`English`),

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{Capture/Rinstlang} 

}

\caption{Screen capture of R install in Windows}(\#fig:rinstlang)
\end{figure}

Click '`Ok`'. And follow the instructions on screen by click '`Next`', until the whole process is complete, click '`Finish`'. You now have a version (choose 64bit) R installed. The installation program will create the directory "`C:`\\`Program Files`\\`R`\\`<your version>`", according to the version of R that you have installed.
The actual R program will be "`C:`\\`Program Files`\\`R`\\\`<your version>`\`bin`\\`Rgui.exe`". A windows "`shortcut`" should have been created on the desktop and/or in the start menu. You can launch it any time you want by click on it. 

### RStudio Download and Installation {-}

To download RStudio, to go Rstudio products Web page (https://rstudio.com/products/rstudio/). Choose "`RStudio Desktop`" between "`RStudio Serve`" and "`RStudio Desktop`". See, Figure \@ref(fig:rstudio1),

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{Capture/Rstudio1} 

}

\caption{Screen capture of RStudio selection}(\#fig:rstudio1)
\end{figure}
After choosing the desktop version it will take you to a page (http://www.RStudio.org/download/desktop). Where several possible downloads are displayed, a different one for each operating system. However, the webpage was designed that it can automatically recommend the download that is most appropriate for your computer. Click on the appropriate link, and the RStudio installer file will start downloading.

Once it is finished downloading, open the installer file and answer all on-screen questions or click "`next`" in the usual way to install RStudio. 

After it is finished installing, you can launch RStudio from the windows `"start"` button.

As we explained in the previous section, Rstudio is a comprehensive and integrated development environment. It can be overwhelming for people who contact it for the first time. Next section we will introduce its interface in great detail. 

### Familiar with RStudio interface {-}

Open RStudio and you will see a rather sophisticated interface. Apart from the usual top-level manual like "`File Edit ...`", there are four panes. I labelled 1 to 4 on the Figure \@ref(fig:RStudio)), these panels are called **pane**\index{pane} in RStudio.

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/Rstudio} 

}

\caption{RStudio interface}(\#fig:RStudio)
\end{figure}

RStudio does allow you to move panes around in the options menu, and also select tabs you want. Before you can miss around and lost yourself on the way. Let us stick to this default layout ion the moment. It is what you see when you first lunch it, so we’ll act as though it’s standard. 

#### Pane 1: Script Pane - View Files and Data {-}

**Script pane** appears by default in the top left of the RStudio interface. it is where you enter your script and code, you can edit and debug your code or your script. 

This pane also displays files When you click on a data file in the `Workspace pane` (top right, number 2 on the above image), or open a file from the `Files` pane (right bottom, number 3 on the above image), the results will appear in Pane 1. Each file opens in its own tab, and you can navigate between tabs by clicking on them (or using keyboard shortcuts).

#### Pane 2: WorkSpace Pane - Environment and History {-}

**Workspace pane** appears by default in the top right of the RStudio interface. It has four tabs by default: **`Environment`**, **`History`**, **`Connection`** and **`Tutorial`**. among these 4, the **`Environment`** is the default and it is selected. It shows a list of all the objects you have loaded into your workspace. For example, all datasets you have loaded will appear here, along with any other objects you have created (special text formats, vectors, etc.). see this image (Figure \@ref(fig:RStudioEven)):

\begin{figure}

{\centering \includegraphics[width=0.8\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/environment} 

}

\caption{RStudio Environemnt Tab in WorkSpace Pane}(\#fig:RStudioEven)
\end{figure}

If you click on the **`History`** tab, you will see the complete history of code you have typed, over all sessions, as in this image (Figure \@ref(fig:RStudiohist)):

\begin{figure}

{\centering \includegraphics[width=0.8\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/history} 

}

\caption{RStudio Histroy Tab in WorkSpace pane}(\#fig:RStudiohist)
\end{figure}

The `history` is searchable, so you can use the search box at the upper right of the pane to search through your code history. If you find a line of code you want to re-run, just select it and click the “`To Console`” button as shown below (\@ref(fig:RStoconsole)): 

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/toconsole} 

}

\caption{RStudio "To Console" button under History Tab in WorkSpace Pane}(\#fig:RStoconsole)
\end{figure}

You can also select any number of lines of scripts (by click with holding the shift key) and click the "`To Source`" button, they will inset into the source, See Figure \@ref(fig:tosource),

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/tosource} 

}

\caption{RStudio "To Source" button under History Tab in WorkSpace Pane}(\#fig:tosource)
\end{figure}

#### Pane 3: Console Pane {-}

By default **`Console pane`** appears at the bottom left. `Console pane` is the most important pane – the Console! This is where you enter your commands to be executed or your R code to do everything in the curriculum. The rest of the document will be largely concerned with working in the `Console`, with occasional references to other panes.
By default it also has 4 tabs: **`Console`**, **`Terminal`**, **`R markdown`** and **`Jobs`**. Apart from the console, Other three, as their name suggested, are the interface between you and other systems. The terminal is the interface between you and the operating system, where you can have direct interaction with OS, in our case it is Windows. R markdown\index{markdown}^[R Markdown is an authoring framework for data science. Using a single R Markdown file, data Scientists can save, execute R code and generate high-quality reports that can be shared with other people.] is the interface between you and the markdown compiler, if authoring a markdown file, every time you compile (`knitr`\index{Knitr}^[knitr is an engine for dynamic report generation with R. It is a package in the programming language R that enables integration of R code into LaTeX, LyX, HTML, Markdown, AsciiDoc, and reStructuredText documents.]) the code, the system will report status in that window. Jobs is the interface between you and your job execution system. it is generally running on a remote server. 

Basically, the `Console pane` is the communication interface between you and the systems. The information that appears here is generally important if any problem occurs.  

#### Pane 4: Multifunction Pane {-}

The **`multifunction pane`** appears by default at the bottom right. It has many tabs. By default it opens the **`Files`** tab. My version it has **`File`**, **`Plots`**, **`Package`**, **`Help`** and **`Viewer`** tabs.   

##### **Files tab** {-}

This tab works like your file explorer. It shows you all the files you have in your RStudio account (your document in windows). The buttons underneath the tab allow you to do operations on the files like create a new folder, delete files. rename files and many more functions. which you normally do on the file system. 

##### **Plots** {-}

When you run code in the `Console pane` that creates a plot, the plots tab will be automatically selected and the result of the plot generated will be displayed. See Figure \@ref(fig:plot))

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/plot} 

}

\caption{RStudio **Plot** Tab under History Tab in Multifunction Pane}(\#fig:plot)
\end{figure}
Any time you want to view plots, you can select this tab manually, but it will be selected when you run plot code. Notice the arrow buttons at the top left of the pane; these allow you to scroll through all the plots you have created in a session.

##### **Packages** {-}

This tab allows you to see the list of all the packages (add-ons to the R code) you have access to, and which are loaded in already. You can also check packages in your system (installed) and the version of them.  

##### **Help** {-}

This tab will be automatically selected whenever you run help code in the Console, by type in console `? function` or type in script `help(function)`. It is very useful for beginners to get quick references on any function or command you are not sure of. here is an example of asking for help with `plot` function: 

```
help(plot)
```
You can access it at any time by clicking on the tab "`Help`" to see what the "`Help`" tab can offer. See Figure \@ref(fig:help),

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/help} 

}

\caption{RStudio **Help** Tab under in Multifunction Pane}(\#fig:help)
\end{figure}

If you want to use the "`Help`" without using the `help` command, you can also use the search bar at the upper right of the tab to search within the R documentation.

##### **Viewer** {-}

The **`Viewer`** tab in the multifunction pane is designed for view or display R markdown\index{R Markdown} results. If you are authoring an R notebook\index{R notebook}^[R Notebooks are an implementation of Literate Programming that allows for direct interaction with R while producing a reproducible document with publication-quality output.] or any Markdown file, your **`Knit`**\index{knit} results can be viewed by select "`Preview` in `Viewer` Pane". Once this selection is made, you will see the notebook or your Markdown\index{Markdown} document will be displayed in the `Viewer` window and you will notice that the `Viewer` tab is automatically selected and the viewer window is also maximized. See Figure \@ref(fig:SSview) 

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/RSview} 

}

\caption{RStudio **Viewer** Tab under in Multifunction Pane}(\#fig:SSview)
\end{figure}

RStudio allows a user to close or minimize certain panes or windows and focused on one or two panes. It also allows users to customize tabs in each pane. Check top-level menu "`View`" for details. Figure \@ref(fig:RSview) illustrates the function.

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/RSPaneview} 

}

\caption{RStudio Pane change under the top level memu View}(\#fig:RSview)
\end{figure}

RStudio provides large number of help functions, which can be explored under **`Help`** top level menu. One help is the `keyBoard shortcuts` help. I find it is very useful. Figure \@ref(fig:rssc) shows the `shortcuts`. 

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/RSsc} 

}

\caption{RStudio KeyBoard Shortcuts}(\#fig:rssc)
\end{figure}

RStudio is a complicated, comprehensive IED for R, R Markdown, R Notebook and many other R language developments, and other languages like Java, Python developments too. Its powerful functions can only be revealed and made useful after you have used it for a while. The more use it, the more likely you will find it is so easy to use. I will leave this for you to explore. 

## Bootsup your RStudio

Once you boot up your RStudio, you are ready to kick off your R coding. However, the first thing you may want to do is to set up your working directory. This will change the default location for all file input and output that you will do in the current session.

RStudio makes this easy, simply click "`Session -> Set Working Directory -> Choose Directory…`". See figure Figure \@ref(fig:setwk) below,

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/setwk} 

}

\caption{Set workling Directory by Session}(\#fig:setwk)
\end{figure}

Then you need to navigate to where you want your project to be sit. For example, in my case I used `"D:/Teach2020/short course/Data analysis - prediction with Rstudio/IntroToDataScience-master"`, it is silly to be so long, you can certainly set up for a shorter one. Anyway, the point is choose your won directory and remember it. If you tried it, you should notice that once you have chosen a directory, A command appeared in the Console pane and this is the command R executes when you set your working directory from the session menu. To achieve the same result you normally would have typed this manually in the console. See Figure \@ref(fig:setwkc) below,

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/setwkc} 

}

\caption{Set a working directory with R command in Console}(\#fig:setwkc)
\end{figure}

Type command or instructions on command line at `Console` is what the general data scientists do when they try to analyse some data or prove some ideas. You can complete this tutorial at the command line in `Console pane`. I would suggest you, instead, creating a script to save all your hard work. This way you can easily reproduce the results or make changes without retyping everything.

To do so, you need to create a new file by click the "`File ->New file`", and select “`R Script`”. See Figure \@ref(fig:newrfile),

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/RStudionew} 

}

\caption{Creat a new file in RStudio}(\#fig:newrfile)
\end{figure}

If you do so, you should notice that a new tab appeared on the script pane with the name of "`Untitled1`" and the script editor is now opened for you with the cursor flashes on line number 1. See Figure \@ref(fig:newrcfile),

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/RStudionewfile} 

}

\caption{Creat a new file in RStudio and ready to enter code}(\#fig:newrcfile)
\end{figure}

Now inside the script editor, you can type your code! Let us try this first, type 

```
# This is my first R code
```
and hit "`Return`", see next image (Figure \@ref(fig:newrcode)),

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/new} 

}

\caption{New R Script file with one line}(\#fig:newrcode)
\end{figure}

Notice that the tab "`Untitled1`" has changed to red colour and with a "*" as superscript. It means that the current file has been changed and not saved.

Go ahead and copy the `setwd` command from the console and paste it into your script. 

Now save the script to your working directory, give it a name my first R, or any name you prefer^[The entire code is in the Appendix and is available online to download too].

Now you have your first R code!

## Instructions

This book is intended to work in two ways: one way is to be used as a manual, you can follow along to accomplish an entire data science project; Another way is to be used as a company to my online video recordings. If you can get the video that is great. But if you cannot, it is also fine, The only drawback is you have to read the whole contents line by line. 

I will use the following stickers to indicate the text is an explanation or an instruction or actions need you to do. So you know what you have to read word by word and what you can skip. 

### Code {-}

Code appears with code sticker. Like this, 

```r
# Load raw data
train <- read.csv("train.csv", header = TRUE)
test <- read.csv("test.csv", header = TRUE)
```
They are the ones you have to read word by word and type (copy-paste) into your script and run them. It is also a good idea to record the results or plot (graph results) into a file. So you can always come back to check them. 

### Tips {-}

Tips, like this one, 

\BeginKnitrBlock{rmdtip}
Within the console, you can use the up and down arrows to find recent commands, and then hitting tab will auto-complete commands and object names if possible.
\EndKnitrBlock{rmdtip}

are general advice. You can skip them if you already know. They can save your time but not affect your learning.

### Actions {-}

Any actions, by default, are assumed you will act upon. It appears in action sticker,

\BeginKnitrBlock{rmdaction}
Change data type
Go back to look into Kaggle to explain pclass: proxy for social class: richer or poor. It should be factor, it does not make sense to stay in int, we are not add or calculate with them

`data.combined$pclass <- as.factor(data.combined$pclass)`

\EndKnitrBlock{rmdaction}

Particularly, they are in sequential order. If you did not take previous actions you cannot do the current. It is possible you have processed some datasets and it is used later on. So you must carry out actions one by one, and not jump to the later ones without accomplishing the earlier ones.  

### Exercise {-}

Exercises at the end of each chapter, are provided for you to periodically explore alternatives of a solution or to enhance some key techniques. It is always good if you can do the exercises. 

The default protocol is that I have some codes written (at the appendix) and you will download them and open in your RStudio. Then you need to run (or copy and past) line by line into your Rstudio. You can understand their functions and the reason to function like that. After you understand them you can change them or write some new code. While you are doing that, you simply comment out my code rather than delete them just in case you need to come back to look at them again. Once you can write your own code, it shows you have learned.  

Before you go, let's try it,

\BeginKnitrBlock{rmdaction}
Open a new project called "MyDataSciece",
Set up working directory as "~/MyDataScienceWithR",
Create a first R program called ``DSPR1``,
`setwk(~/MyDataScienceWithR)`  

\EndKnitrBlock{rmdaction}

Okay. Save your file and move to the next chapter.

## Exercises {-}

1. Learn R basics from R tutorials. 

+ Tutorialspoint (http://www.tutorialspoint.com/r/index.htm), 
+ codecademy (https://www.codecademy.com/). 
+ DataCamp (https://www.datacamp.com/courses/free-introduction-to-r) 

2. Write simple R code with RStudio IDE.

+ Try create a new R script and save it in a file
+ Open it from your file system and edit it 
+ Run it line by line
+ Run it in one go

3. Explore RStudio help functions.

+ Try to type "?plot" in Console
+ Try run help(plot) in editor
+ Explore plot from RStudio help 
+ Search "R plot" from Google or Bing

4. Explore project and working directory from RStudio.

5. Create a R project called "MyDataScienceProject". 



