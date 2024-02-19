# Lab Assignment for Week 07
## Create a Map-Based Visualization from the U.S. Flights Dataset
## Due on [Canvas](https://psu.instructure.com/courses/2306358/assignments/15979273) on Wed., Feb. 28 at 11:59pm

**On this and all labs for the rest of the semester: When you have a choice between the [datascience package](https://www.data8.org/datascience/) and the [pandas library](https://pandas.pydata.org/docs/), you are free to use either method to complete your work. HOWEVER, on Lab 7, we recommend using the datascience package, as the file management required here is already tricky and the map-based features in the datascience package are difficult to implement by themselves.**

The main objective of this week's lab is to produce at least one visualization, consisting of a map (or maps) of the United States, using data from the massive dataset used in [Lab 6](https://github.com/DS200-SP2024-Hunter/Week06-DueFeb23).  

Objective:  Use the example code provided to (a) create a map-based visualizaton of based on the flights.csv dataset, and (b) provide a short narrative based on what the visualization reveals.  For instance, the example code shows average flight time as well as the total number of flights departing from a subset of the airports.  As in the example, you may use a subset of the full dataset if you wish.  You may also produce more than one map if you wish.

Your assignment is as follows:

1. Create a new Jupyter notebook in which you produce at least one map of the United States that you find interesting.  Your notebook should include supporting text, using a text box or boxes in your notebook, explaining in detail what the map shows. The data to use for this map come from the `flights.csv` file used in [Lab 6](https://github.com/DS200-SP2022-Hunter/Week06-Feb15), and you should somehow summarize at least two different variables created from that dataset in your visualization.  Please do not use exactly the same variables and subset of the flights as in the example code.

2. Create a text box that explains interesting aspects of what your map reveals.  In the example above, we might notice that:  The busiest airports served by American and American Eagle are clearly seen in red:  Chicago's O'Hare Airport and Dallas/Ft. Worth Airport, which are consistently two of the world's busiest airports.  Somewhat surprising perhaps is that the solid third place, which is the only airport shown in orange (between 100K and 200K flights), is Miami's Airport.  A more in-depth analysis could reveal whether Miami has so many flights because of international routes served by American Airlines.  We notice that airports that are more remote geographically from other airports tend to have longer average flight times, with Anchorage Alaska being an easy-to-understand outlier as clearly the largest circle.  Interestingly, the Hawaii airports don't have circles nearly as large as Anchorage, which reveals something about flights originating there (can you understand what it is? Further analysis could uncover the full details.)

When you've completed this, you should select "Print" from the File menu, then save to pdf using this option.  The pdf file that you create in this way is the file that you should upload to Canvas for grading.  We have found that if you can select the "A3" paper size from the advanced options, this seems to solve problems with incorrect cropping of the pages.

# CODE THAT SHOULD BE PUT INTO A JUPYTER NOTEBOOK

```
#########################################################
# DS 200, SP 2022 semester
# python code related to Lab 07 if you choose to adapt it
# If you use this code, copy-paste it one block at a time
# and try to understand what each block accomplishes
#########################################################

# Load needed python resources, including folium mapping module
from datascience import *
import matplotlib
matplotlib.use('Agg', warn=False)
%matplotlib inline
import matplotlib.pyplot as plots
plots.style.use('fivethirtyeight')
import numpy as np
import pandas as pd
np.set_printoptions(threshold=50)
import folium

# Read massive dataset.  Your file path may be different,
# depending on where you stored the data files for Lab 6.
file_path = 'drive/My Drive/Colab Notebooks/data/'
flights = Table.read_table(file_path + 'flights.csv')

# Read airports dataset containing airport names and lat/lon coordinates
airports = Table.read_table(file_path + 'airports.csv')

# Read airlines dataset containing airline names
airlines = Table.read_table(file_path + 'airlines.csv')
# This Table is small enough to print the whole thing:
airlines.show()

# Figure out how many flights per airline
# Notice (and try to understand!) the various methods 
# used to create the new Table 
count_by_airline = (flights
     .select('AIRLINE')
     .relabeled('AIRLINE', 'AIRLINE_CODE')
     .group('AIRLINE_CODE')
     .join('AIRLINE_CODE', airlines, 'IATA_CODE')
     .relabeled('AIRLINE', 'AIRLINE_NAME')
     .sort('count', descending = True)
)
# Print the table we just created
count_by_airline.show()

# Check out the columns available in the flights dataset
# Think about which might be interesting to display on a map
flights.labels

# Calculate average departure delay for each airline
# Notice (and try to understand!) the various methods 
# used to create the new Table 
avg_delays = (flights
     .select('AIRLINE', 'DEPARTURE_DELAY')
     .relabeled('AIRLINE', 'AIRLINE_CODE')
     .group('AIRLINE_CODE', np.nanmean)
     .relabeled('DEPARTURE_DELAY nanmean', 'AVG_DELAY')
     .join('AIRLINE_CODE', airlines, 'IATA_CODE')
     .relabeled('AIRLINE', 'AIRLINE_NAME')
     .sort('AVG_DELAY')
)
# Print the table we just created
avg_delays.show()

# As an illustration, let's consider only flights by 
# American and American Eagle
AmFlights = (flights.where('AIRLINE', are.contained_in('AA MQ')))

# Count American flights for each ORIGIN_AIRPORT to get
# an idea of how many airports these airlines serve
airport_counts = AmFlights.group('ORIGIN_AIRPORT').sort('count', descending=True)
airport_counts

# Find average AIR_TIME for each ORIGIN_AIRPORT and join lat/lon info
# so that we can create a map with airport locations
avg_airtime = (AmFlights
     .select('AIR_TIME', 'ORIGIN_AIRPORT')
     .group('ORIGIN_AIRPORT', np.nanmean)
     .relabeled('AIR_TIME nanmean', 'AVG_AIRTIME')
     .join('ORIGIN_AIRPORT', airports, 'IATA_CODE') 
     .join('ORIGIN_AIRPORT', airport_counts, 'ORIGIN_AIRPORT')
     .select('ORIGIN_AIRPORT', 'AVG_AIRTIME', 'AIRPORT', 'LATITUDE', 
                                                'LONGITUDE', 'count')
     .relabeled('AIRPORT', 'AIRPORT_NAME')
)
# Let's see the top 10 rows of this new Table
avg_airtime

# Create folium map with circles having radius proportional to air time
AmericanAirlinesMap = folium.Map(location=[35, -100],
     tiles="OpenStreetMap", zoom_start=4,
     width='100%', height='100%')
# Add Circles one by one using info from avg_airtime Table
for i in np.arange(avg_airtime.num_rows):
     folium.Circle([avg_airtime.column('LATITUDE')[i],
     avg_airtime.column('LONGITUDE')[i]],
     radius = 250 * avg_airtime.column('AVG_AIRTIME')[i],
     fill=True).add_to(AmericanAirlinesMap)
# Once Map object is created, output it:
AmericanAirlinesMap

# Let's color the cities by how many flights:
# Use 'cut' method in pandas library to create a categorical
# variable from a numerical variable such as 'count'
count_colors = pd.cut(avg_airtime.column('count'),
     bins = [0, 10000, 25000, 50000, 100000, 200000],
     labels = ['blue', 'green', 'yellow', 'orange', 
     'red'])
# Add this newly created array of colors to the Table: 
avg_airtime = avg_airtime.with_column('color', count_colors)
# Check out the top ten rows of the new Table:
avg_airtime

# Recreate folium map with circles having radius proportional to air time
# But now using colors to categorize flight counts:
# blue means 1 to 10000
# green means 10001 to 25000
# yellow means 25001 to 50000
# orange means 50001 to 100000
# red means 100001 to 200000
AmericanAirlinesMap = folium.Map(location=[35, -100],
     tiles="OpenStreetMap", zoom_start=3,
     width='100%', height='100%')
# Add Circles one by one using info from avg_airtime Table
for i in np.arange(avg_airtime.num_rows):
     folium.Circle([avg_airtime.column('LATITUDE')[i],
     avg_airtime.column('LONGITUDE')[i]],
     radius = 250 * avg_airtime.column('AVG_AIRTIME')[i],
     color = avg_airtime.column('color')[i],
     fill=False).add_to(AmericanAirlinesMap)
# Once Map object is created, output it.
# This map shows both avg air time (by radius) and airport busyness (by color)
# for all airports served by American or American Eagle
AmericanAirlinesMap
```

