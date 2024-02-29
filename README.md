# Lab Assignment for Week 07
## Create a Map-Based Visualization from the U.S. Flights Dataset
## Due on [Canvas](https://psu.instructure.com/courses/2306358/assignments/15979273) on Fri., Mar. 1 at 11:59pm
## NOTICE:  DEADLINE EXTENDED

**On this and all labs for the rest of the semester: When you have a choice between the [datascience package](https://www.data8.org/datascience/) and the [pandas library](https://pandas.pydata.org/docs/), you are free to use either method to complete your work. HOWEVER, on Lab 7, we recommend using the datascience package, as the file management required here is already tricky and the map-based features in the datascience package are difficult to implement by themselves.**

The main objective of this week's lab is to produce at least one visualization, consisting of a map (or maps) of the United States, using data from the massive dataset used in [Lab 6](https://github.com/DS200-SP2024-Hunter/Week06-DueFeb23).  

Objective:  Use the example code provided to (a) create a map-based visualizaton of based on the flights.csv dataset, and (b) provide a short narrative based on what the visualization reveals.  For instance, the example code shows average flight time as well as the total number of flights departing from a subset of the airports.  As in the example, you may use a subset of the full dataset if you wish.  You may also produce more than one map if you wish.

Here is [a supplementary jupyter notebook for this lab](https://github.com/DS200-SP2024-Hunter/Week07-DueFeb28/blob/main/Lab07SupplementaryNotebook.ipynb) and
[a screenshot of the map produced by the final code block](https://github.com/DS200-SP2024-Hunter/Week07-DueFeb28/blob/main/Lab07_MapScreenshot.png). In that map, both the colors of the circles and the sizes of the circles are determined by the same variable, the count of flights.  Can you create a map that uses color for one variable and circle size for another?

Your assignment is as follows:

1. Create a new Jupyter notebook in which you produce at least one map of the United States that you find interesting.  Your notebook should include supporting text, using a text box or boxes in your notebook, explaining in detail what the map shows. The data to use for this map come from the `flights.csv` file used in [Lab 6](https://github.com/DS200-SP2024-Hunter/Week06-DueFeb23), and you should somehow summarize at least two different variables created from that dataset in your visualization.  Please do not use exactly the same variables and subset of the flights as in the example code.

2. Create a text box that explains interesting aspects of what your map reveals.  In the example above, we might notice that:  The busiest airports served by American and American Eagle are clearly seen in red:  Chicago's O'Hare Airport and Dallas/Ft. Worth Airport, which are consistently two of the world's busiest airports.  Somewhat surprising perhaps is that the solid third place, which is the only airport shown in orange (between 100K and 200K flights), is Miami's Airport.  A more in-depth analysis could reveal whether Miami has so many flights because of international routes served by American Airlines.  We notice that airports that are more remote geographically from other airports tend to have longer average flight times, with Anchorage Alaska being an easy-to-understand outlier as clearly the largest circle.  Interestingly, the Hawaii airports don't have circles nearly as large as Anchorage, which reveals something about flights originating there (can you understand what it is? Further analysis could uncover the full details.)

When you've completed this, you should select "Print" from the File menu, then save to pdf using this option.  The pdf file that you create in this way is the file that you should upload to Canvas for grading.  We have found that if you can select the "A3" paper size from the advanced options, this seems to solve problems with incorrect cropping of the pages.



