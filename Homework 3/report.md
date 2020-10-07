MD Sami Uddin 

CS625-HW3

Due date: October 6, 2020


[Tableau Link](https://prod-useast-a.online.tableau.com/#/site/oducs625/workbooks/246035?:origin=card_share_link)

[Observable Link](https://observablehq.com/@rayansami/homework-3)

# Data Description

#### VDH Dataset    

VDH-COVID-19-PublicUseDataset-Tests_by-LabReportDate.csv

| Data Attribute                              | Data Type    |
|---------------------------------------------|--------------|
| Lab Report Date                             | Temporal     |
| Health District                             | Categorical  |
| Number of PCR Testing Encounters            | Quantitative |
| Number of Positive PCR Testing Encounters   | Quantitative |
| Total Number of Positive Testing Encounters | Quantitative |
| Total Number of Testing Encounters          | Quantitative |

#### NYT Dataset  

mask-use-by-county.csv

| Data Attribute                              | Data Type    |
|---------------------------------------------|--------------|
| COUNTRYFP                                   | Quantitative |
| NEVER                                       | Quantitative |
| RARELY                                      | Quantitative |
| SOMETIMES                                   | Quantitative |
| FREQUENTLY                                  | Quantitative |
| ALWAYS                                      | Quantitative |

#### FIPS Dataset   

VDH dataset has information based on counties. Whereas, Mask dataset has infos based of countyfips. To join this two dataset for solving our 3rd question, I needed to convert countyfips to Health District on mask dataset. That's why I had to use [state_and_county_fips_master.csv](https://github.com/kjhealy/fips-codes/blob/master/state_and_county_fips_master.csv) , used python scipt to clean it(remove row where fips = 0 and remove ' County' from the names). Also changed the header names manually to match with other dataset.

``` python
import pandas as pd
df = pd.read_csv("https://raw.githubusercontent.com/kjhealy/fips-codes/master/state_and_county_fips_master.csv")
df  = df.drop(df.index[0])
print(df)
df.to_csv (r'county_info_fips.csv', index = None, header=True) 
```

After that, cleaned *County* and *City* from the Health District values using OpenRefine.

county_info_fips.csv

| Data Attribute                              | Data Type    |
|---------------------------------------------|--------------|
| COUNTRYFP                                   | Quantitative |
| Health District                             | Categorical  |
| STATE                                       | Quantitative |

# Part 1: Using Application (Tableau)

URL: https://prod-useast-a.online.tableau.com/#/site/oducs625/workbooks/246035?:origin=card_share_link

### Q1: How does the percentage of positive PCR tests over time compare for Norfolk, Chesapeake, and Virginia Beach? Using [VDH]  

#### Marks & Channels
Here marks are the *Lines* and the channel is *Color Hue*.

#### Justification for this Chart
To compare the positive PCR cases among the health district, over a certain period, I chose to work with a Line chart. Because it is great to show off the trends and contrast among the areas.

#### Chart

To get the daily percentage of positive PCR tests over time, I had to set *Lab Report Date* to columns and *Health District* and *Sum(Number of Positive PCR tests)* to Rows. 

Tableau was showing null values to dates and I filtered it just for showing values only from 2020.

Later I added colors to *Health Districts* and removed it from the rows to get all three lines in one chart. It gives a clear comparative visualization.

After getting the Sum(Number of Positive PCR tests) I opted for *Table Calculation*, where I selected **Calculation Type: Percent of Total** and unselected *Health District* from *Specific Dimensions*.

Here's the line chart I got:
![Picture](/images/qs1-software-part1.png)

#### Analysis and Thoughts

    Cases in all 3 places started to spike after June 6 and reached its peak in July and started to cool down roughly at the end of July.

    In this time frame, Norfolk saw aggressive positive cases on 3 different days. The same thing goes for VA Beach. However, cases at Chesapeake are capricious. It has frequent ups and downs in the positive PCR test percentages.

### Q2: [VDH] For each health district, what percentage of the total tests given were PCR tests?

#### Marks & Channels
Here marks are the *Bars* and the channel is *Color Hue*.

#### Justification for this Chart

*Health District* is a categorical value and *percentage of PCR tests against total tests* is a quantitative value. So to give a clear idea, I chose a bar chart and have it sorted. 

#### Chart

To get the percentage used the formula below and named it *% of Total Tests Given Were PCR TEST For Each Health District*

    100*(SUM([Number of PCR Testing Encounters])/SUM([Total Number of Testing Encounters]))

Added the new field to columns and added *Health District* to rows. Then made a sort on the health districts based on their values. 

Here's the line chart I got for Q2:
![Picture](/images/qs2-software-part1.png)

Keeping *Out of State* and *Unknown*


### Q3: [VDH, NYT] Was there a correlation between the percentage positive PCR tests averaged over July 16-28 (two weeks after the mask survey) and the estimated share of residents that answered "Frequently" or "Always" to the question of mask usage?

#### Marks & Channels
Here marks are the *Bars* and the channel is *Color Hue*.

#### Justification for this Chart(s)

I need to show the visualization for 15 different health districts. For one chart I need to place the health districts against the percentage positive PCR tests averaged over July 16-28, which is quantitative value.

And for another chart, I need to calculate the number of answers for "Frequently" or "Always" against health districts. Which is again a plot for *Categorical* and *Quantitative* values.

#### Chart

**From VDH dataset**, to get the percentage positive PCR tests averaged over July 16-28, at first I had to filter out the *Lab Report Dates* in this range. Also, I had to filter on *Health District* for 15 districts mentioned in the question.

Formula to get average PCR positive encounter:

    SUM([Number of Positive PCR Testing Encounters])/COUNT([Number of Positive PCR Testing Encounters])

    Saved as: Avg Positive PCR Testing Encounters

To get the % positive PCR tests:

    (([Avg Positive PCR Testing Encounters])/WINDOW_SUM([Avg Positive PCR Testing Encounters]))*100

*WINDOW_SUM* gives the summation of all *Avg Positive PCR Testing Encounters* inside the visualization frame.

For further analysis, I had to join **NYT dataset** and **Fips dataset** with VDH. Calculated the sum of *Always* and *Frequency* from the NYT dataset and plotted against the Health Districts. This caused several null values on Health Districts and I removed them and worked with the remaining 9 locations.

Here are the bar charts I got:
![Picture](/images/qs3-software-part1.png)

It gives a pretty neat comparison for masks usage among the districts as well as the percentage positive PCR tests averaged among the districts.

#### Analysis and Thoughts

    It's pretty self-explaining that where the mask usage is high, the PCR positive percentage would be low. And I found exactly that for Richmond. Where Fairfax, Norfolk, Prince William, Henrico shows otherwise and proves the analogy.

    However, some locations like Arlington, Hampton, Roanoke have a lower number in mask usage and also lower in number for PCR test percentage in this time frame. It demands further exploration. But for now, I am assuming that there's some shortage of data on mask usage in these areas.

# Part 2 - Using Programming Language/API (Vega-Lite)

**Observable Repo**: https://observablehq.com/@rayansami/homework-3

I did not try for tooltips here, so I removed them from Tableau too. But one thing I understood that I can not achieve the interactivity on Vega-Lite that I could achieve in Tableau.

#### Q1: 

I was struggling with the calculations at first. But later I grab the ideas and made sure to sync with the one on Tableau.

#### Q2:

The same goes for Q2 as Q1. After implementing this, I had to change the colors manually on Tableau.

#### Q3:

Couldn't complete the calculation using *transform*. I am not sure if there is any limitation like *vl.calculate()* can be used only once! Also could not find a way to show two different datasets in one visualization window. I'd love to take some direction from you so that I don't get stuck like this in the future!


# Reference

- Formatting and working with time-series data: https://www.youtube.com/watch?v=d-_EcgQbVV4

- Getting started video: https://www.tableau.com/learn/tutorials/on-demand/getting-started-9

- Tableau getting started: https://help.tableau.com/current/pro/desktop/en-us/gettingstarted_overview.htm

- Date Function: https://help.tableau.com/current/pro/desktop/en-us/functions_functions_date.htm

- Create Data range in Tableau: https://www.youtube.com/watch?v=fkWrUTlQyuc

- Observable Crash course: https://www.youtube.com/watch?v=ZV_Yjcs5WtM
        - Datatypes: 5.40 minutes
        - coloring: 9.41

- Vega-Lite Docs: https://vega.github.io/vega-lite/docs/

- Vega-lite color scheme: https://vega.github.io/vega/docs/schemes/#reference

- Vega-lite chart types: https://observablehq.com/@observablehq/vega-lite-chart-types

- Observable data transformation: https://observablehq.com/@uwdata/data-transformation


- Vega-lite expression: https://vega.github.io/vega/docs/expressions/

- Time unit in Observable: https://observablehq.com/@ijlyttle/vega-lite-timeunit
