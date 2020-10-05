# Data Description

#### VDH Dataset

| Data Attribute                              | Data Type    |
|---------------------------------------------|--------------|
| Lab Report Date                             | Temporal     |
| Health District                             | Categorical  |
| Number of PCR Testing Encounters            | Quantitative |
| Number of Positive PCR Testing Encounters   | Quantitative |
| Total Number of Positive Testing Encounters | Quantitative |
| Total Number of Testing Encounters          | Quantitative |

#### MASKS Dataset TODO:


#### FIPS Dataset   TODO:

# Part 1: Using Application (Tableau)

### Q1: How does the percentage of positive PCR tests over time compare for Norfolk, Chesapeake, and Virginia Beach? Using [VDH]  

#### Marks & Channels
Here marks are the *Lines* and channel is *Color Hue*.

#### Justification for this Chart
To compare the positive PCR cases among the health district, over a certain time period, I chose to work with Line chart. Because it is great to show off the trends and contrast among the areas.

#### Chart

To get the weekly moving average of positive PCR tests over time, I had to set *Lab Report Date* to columns and *Health District* and *Sum(Number of Positive PCR tests)* to Rows. 

Later I added colors to *Health Districts* to get all three lines in one chart. It gives a clear comparative visualization.

Here's the line chart I got:
![Picture](/images/qs1-software-part1.png)

#### Analysis and Thoughts

Tableau was showing null values to dates and I filtered it just for showing values only from 2020.

But to compare more precisely, it would be easier to see the trends at a certain date points if the lines are stacked onto one another.

Analysis

    Cases in all 3 places started to spike after June 21 and reached to peak after a month before starting to go down gradually.

    Norfolk and Chesapeak were slow in positive cases from the start. Although during the peak Norfolk went top in good margin compared to Chesapeak.
    However, VA Beach topped all the time(except couple of weeks) in this time frame.

    I believe VA Beach has much more crowdy areas and activities to do compared to other two areas, which in turn contributed to more positive cases!


### Q2: [VDH] For each health district, what percentage of the total tests given were PCR tests?

To get the percentage:

    100*(SUM([Number of PCR Testing Encounters])/SUM([Total Number of Testing Encounters]))

Here's the line chart I got for Q2:
![Picture](/images/qs2-software-part1.png)

Keeping *Out of State* and *Unknown*

# Part 2 - Using Programming Language/API (Vega-Lite)

Link: https://observablehq.com/d/996d03399a417c70

# Reference
    - Formatiing and working with time-series data: https://www.youtube.com/watch?v=d-_EcgQbVV4

    - Getting started video: https://www.tableau.com/learn/tutorials/on-demand/getting-started-9
    - Tableau getting started: https://help.tableau.com/current/pro/desktop/en-us/gettingstarted_overview.htm

        - Date Function: https://help.tableau.com/current/pro/desktop/en-us/functions_functions_date.htm

    - Create Data range in Tableau: https://www.youtube.com/watch?v=fkWrUTlQyuc

    - Observable Crash course: https://www.youtube.com/watch?v=ZV_Yjcs5WtM
        - Datatypes: 5.40 minutes
        - coloring: 9.41