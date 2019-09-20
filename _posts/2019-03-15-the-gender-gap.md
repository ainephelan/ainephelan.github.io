---
layout: post
title:  "Mind the Gap"
date:   2019-03-15 20:40:42 +1000
categories: jekyll update
---

![train](https://raw.githubusercontent.com/ainephelan/ainephelan.github.io/master/images/People_Train.jpg)

## Visualising the Gender Gap in College Majors  
Today I'll be creating visualisations which communicate the historical narrative of the gender gap in different fields of study in the US.

I'll use the already cleaned data set described below, and I'm going to take the opportunity to practice some techniques to eliminate [chartjunk](https://en.wikipedia.org/wiki/Chartjunk) and maximise the [data-ink ratio](https://infovis-wiki.net/wiki/Data-Ink_Ratio).  I want these visuals to be communicative and complete, not requiring any ancillary data to be understood.

All the better to see you with my dear!


### Context
The [National Center for Education Statistics](https://nces.ed.gov/programs/digest/current_tables.asp) in the US releases a data set annually containing the percentage of bachelor's degrees granted to women since 1970. The data set is broken up into 17 categories of degrees, with each column as a separate category.

[Randal Olson](http://www.randalolson.com/2014/06/14/percentage-of-bachelors-degrees-conferred-to-women-by-major-1970-2012/), a data scientist from the University of Pennsylvania, has cleaned the data set for years 1970 to 2012 and made it available on his personal website. 

Gender parity is a topic close to my own heart, and we still have a long way to go in understanding it or in some instances even [agreeing that it exists](https://www.smartcompany.com.au/business-advice/politics/frydenberg-pay-gap-closed/), let alone bridging it.

Right, let's use Randal's [cleaned data](http://www.randalolson.com/wp-content/uploads/percent-bachelors-degrees-women-usa.csv) to see some historical differences between the genders regarding their chosen fields of study in the US.

For the purposes of this analysis I'll be treating gender as a binary construct only.

## Load the Data
Let's load our data and do some initial exploration of the data set.

It is not large and I can easily display it in its entirety.

{% highlight python %}
import pandas as pd

women_degrees = pd.read_csv('percent-bachelors-degrees-women-usa.csv')
women_degrees
{% endhighlight %}


|   Year |   Agriculture |   Architecture |   Art and Performance |   Biology |   Business |   Communications and Journalism |   Computer Science |   Education |   Engineering |   English |   Foreign Languages |   Health Professions |   Math and Statistics |   Physical Sciences |   Psychology |   Public Administration |   Social Sciences and History |
|-------:|--------------:|---------------:|----------------------:|----------:|-----------:|--------------------------------:|-------------------:|------------:|--------------:|----------:|--------------------:|---------------------:|----------------------:|--------------------:|-------------:|------------------------:|------------------------------:|
|   1970 |       4.2298  |        11.921  |                  59.7 |   29.0884 |    9.06444 |                            35.3 |               13.6 |     74.5353 |           0.8 |   65.5709 |                73.8 |                 77.1 |                  38   |                13.8 |         44.4 |                    68.4 |                          36.8 |
|   1971 |       5.4528  |        12.0031 |                  59.9 |   29.3944 |    9.50319 |                            35.5 |               13.6 |     74.1492 |           1   |   64.5565 |                73.9 |                 75.5 |                  39   |                14.9 |         46.2 |                    65.5 |                          36.2 |
|   1972 |       7.42071 |        13.2146 |                  60.4 |   29.8102 |   10.559   |                            36.6 |               14.9 |     73.5545 |           1.2 |   63.6643 |                74.6 |                 76.9 |                  40.2 |                14.8 |         47.6 |                    62.6 |                          36.1 |
|   1973 |       9.6536  |        14.7916 |                  60.2 |   31.1479 |   12.8046  |                            38.4 |               16.4 |     73.5018 |           1.6 |   62.9415 |                74.9 |                 77.4 |                  40.9 |                16.5 |         50.4 |                    64.3 |                          36.4 |
|   1974 |      14.0746  |        17.4447 |                  61.9 |   32.9962 |   16.2049  |                            40.5 |               18.9 |     73.3368 |           2.2 |   62.4134 |                75.3 |                 77.9 |                  41.8 |                18.2 |         52.6 |                    66.1 |                          37.3 |
|   1975 |      18.3332  |        19.134  |                  60.9 |   34.4499 |   19.6862  |                            41.5 |               19.8 |     72.8019 |           3.2 |   61.6472 |                75   |                 78.9 |                  40.7 |                19.1 |         54.5 |                    63   |                          37.7 |
|   1976 |      22.2528  |        21.3945 |                  61.3 |   36.0729 |   23.43    |                            44.3 |               23.9 |     72.1665 |           4.5 |   62.1482 |                74.4 |                 79.2 |                  41.5 |                20   |         56.9 |                    65.6 |                          39.2 |
|   1977 |      24.6402  |        23.7405 |                  62   |   38.3314 |   27.1634  |                            46.9 |               25.7 |     72.4564 |           6.8 |   62.7231 |                74.3 |                 80.5 |                  41.1 |                21.3 |         59   |                    69.3 |                          40.5 |
|   1978 |      27.1462  |        25.8492 |                  62.5 |   40.1125 |   30.5275  |                            49.9 |               28.1 |     73.1928 |           8.4 |   63.6191 |                74.3 |                 81.9 |                  41.6 |                22.5 |         61.3 |                    71.5 |                          41.8 |
|   1979 |      29.6334  |        27.7705 |                  63.2 |   42.0656 |   33.6216  |                            52.3 |               30.2 |     73.8211 |           9.4 |   65.0884 |                74.2 |                 82.3 |                  42.3 |                23.7 |         63.3 |                    73.3 |                          43.6 |
|   1980 |      30.7594  |        28.0804 |                  63.4 |   43.9993 |   36.7657  |                            54.7 |               32.5 |     74.981  |          10.3 |   65.2841 |                74.1 |                 83.5 |                  42.8 |                24.6 |         65.1 |                    74.6 |                          44.2 |
|   1981 |      31.3187  |        29.8417 |                  63.3 |   45.2495 |   39.2662  |                            56.4 |               34.8 |     75.8451 |          11.6 |   65.8383 |                73.9 |                 84.1 |                  43.2 |                25.7 |         66.9 |                    74.7 |                          44.6 |
|   1982 |      32.6367  |        34.8162 |                  63.1 |   45.9673 |   41.9494  |                            58   |               36.3 |     75.8436 |          12.4 |   65.8474 |                72.7 |                 84.4 |                  44   |                27.3 |         67.5 |                    76.8 |                          44.6 |
|   1983 |      31.6353  |        35.8263 |                  62.4 |   46.7131 |   43.5421  |                            58.6 |               37.1 |     75.9506 |          13.1 |   65.9184 |                71.8 |                 84.6 |                  44.3 |                27.6 |         67.9 |                    76.1 |                          44.1 |
|   1984 |      31.0929  |        35.4531 |                  62.1 |   47.6691 |   45.124   |                            59.1 |               36.8 |     75.8691 |          13.5 |   65.7499 |                72.1 |                 85.1 |                  46.2 |                28   |         68.2 |                    75.9 |                          44.1 |
|   1985 |      31.3797  |        36.1333 |                  61.8 |   47.9099 |   45.7478  |                            59   |               35.7 |     75.9234 |          13.5 |   65.7982 |                70.8 |                 85.3 |                  46.5 |                27.5 |         69   |                    75   |                          43.8 |
|   1986 |      31.1987  |        37.2402 |                  62.1 |   48.3007 |   46.5329  |                            60   |               34.7 |     76.143  |          13.9 |   65.9826 |                71.2 |                 85.7 |                  46.7 |                28.4 |         69   |                    75.7 |                          44   |
|   1987 |      31.4864  |        38.7307 |                  61.7 |   50.2099 |   46.6905  |                            60.2 |               32.4 |     76.9631 |          14   |   66.706  |                72   |                 85.5 |                  46.5 |                30.4 |         70.1 |                    76.4 |                          43.9 |
|   1988 |      31.0851  |        39.3989 |                  61.7 |   50.0998 |   46.7648  |                            60.4 |               30.8 |     77.6277 |          13.9 |   67.1445 |                72.3 |                 85.2 |                  46.2 |                29.7 |         70.9 |                    75.6 |                          44.4 |
|   1989 |      31.6124  |        39.0965 |                  62   |   50.7747 |   46.7816  |                            60.5 |               29.9 |     78.1119 |          14.1 |   67.0171 |                72.4 |                 84.6 |                  46.2 |                31.3 |         71.6 |                    76   |                          44.2 |
|   1990 |      32.7034  |        40.824  |                  62.6 |   50.8181 |   47.2009  |                            60.8 |               29.4 |     78.8669 |          14.1 |   66.9219 |                71.2 |                 83.9 |                  47.3 |                31.6 |         72.6 |                    77.6 |                          45.1 |
|   1991 |      34.7118  |        33.6799 |                  62.1 |   51.4688 |   47.2243  |                            60.8 |               28.7 |     78.9912 |          14   |   66.2415 |                71.1 |                 83.5 |                  47   |                32.6 |         73.2 |                    78.2 |                          45.5 |
|   1992 |      33.9317  |        35.2024 |                  61   |   51.3497 |   47.2194  |                            59.7 |               28.2 |     78.4352 |          14.5 |   65.6225 |                71   |                 83   |                  47.4 |                32.6 |         73.2 |                    77.3 |                          45.8 |
|   1993 |      34.9468  |        35.7772 |                  60.2 |   51.1248 |   47.6393  |                            58.7 |               28.5 |     77.2673 |          14.9 |   65.731  |                70   |                 82.4 |                  46.4 |                33.6 |         73.1 |                    78   |                          46.1 |
|   1994 |      36.0327  |        34.4335 |                  59.4 |   52.2462 |   47.9839  |                            58.1 |               28.5 |     75.8149 |          15.7 |   65.642  |                69.1 |                 81.8 |                  47   |                34.8 |         72.9 |                    78.8 |                          46.8 |
|   1995 |      36.8448  |        36.0632 |                  59.2 |   52.5994 |   48.5732  |                            58.8 |               27.5 |     75.1253 |          16.2 |   65.9369 |                69.6 |                 81.5 |                  46.1 |                35.9 |         73   |                    78.8 |                          47.9 |
|   1996 |      38.9698  |        35.9265 |                  58.6 |   53.7899 |   48.6474  |                            58.7 |               27.1 |     75.0352 |          16.7 |   66.4378 |                69.7 |                 81.3 |                  46.4 |                37.3 |         73.9 |                    79.8 |                          48.7 |
|   1997 |      40.6857  |        35.1019 |                  58.7 |   54.9995 |   48.5611  |                            60   |               26.8 |     75.1637 |          17   |   66.7864 |                70   |                 81.9 |                  47   |                38.3 |         74.4 |                    81   |                          49.2 |
|   1998 |      41.9124  |        37.5985 |                  59.1 |   56.3512 |   49.2585  |                            60   |               27   |     75.4862 |          17.8 |   67.2554 |                70.1 |                 82.1 |                  48.3 |                39.7 |         75.1 |                    81.3 |                          50.5 |
|   1999 |      42.8872  |        38.6315 |                  59.2 |   58.2288 |   49.8102  |                            61.2 |               28.1 |     75.8382 |          18.6 |   67.8202 |                70.9 |                 83.5 |                  47.8 |                40.2 |         76.5 |                    81.1 |                          51.2 |
|   2000 |      45.0578  |        40.0236 |                  59.2 |   59.3899 |   49.8036  |                            61.9 |               27.7 |     76.6921 |          18.4 |   68.366  |                70.9 |                 83.5 |                  48.2 |                41   |         77.5 |                    81.1 |                          51.8 |
|   2001 |      45.866   |        40.6903 |                  59.4 |   60.7123 |   50.2751  |                            63   |               27.6 |     77.3752 |          19   |   68.5785 |                71.2 |                 85.1 |                  47   |                42.2 |         77.5 |                    80.9 |                          51.7 |
|   2002 |      47.1347  |        41.133  |                  60.9 |   61.8951 |   50.5523  |                            63.7 |               27   |     78.6442 |          18.7 |   68.83   |                70.5 |                 85.8 |                  45.7 |                41.1 |         77.7 |                    81.3 |                          51.5 |
|   2003 |      47.9352  |        42.7585 |                  61.1 |   62.1695 |   50.3456  |                            64.6 |               25.1 |     78.5449 |          18.8 |   68.8945 |                70.6 |                 86.5 |                  46   |                41.7 |         77.8 |                    81.5 |                          50.9 |
|   2004 |      47.8871  |        43.4665 |                  61.3 |   61.9146 |   49.9509  |                            64.2 |               22.2 |     78.6507 |          18.2 |   68.4547 |                70.8 |                 86.5 |                  44.7 |                42.1 |         77.8 |                    80.7 |                          50.5 |
|   2005 |      47.6728  |        43.1004 |                  61.4 |   61.501  |   49.7919  |                            63.4 |               20.6 |     79.0671 |          17.9 |   68.5712 |                69.9 |                 86   |                  45.1 |                41.6 |         77.5 |                    81.2 |                          50   |
|   2006 |      46.7903  |        44.4993 |                  61.6 |   60.1728 |   49.2109  |                            63   |               18.6 |     78.6863 |          16.8 |   68.2976 |                69.6 |                 85.9 |                  44.1 |                40.8 |         77.4 |                    81.2 |                          49.8 |
|   2007 |      47.605   |        43.1005 |                  61.4 |   59.412  |   49.0005  |                            62.5 |               17.6 |     78.7214 |          16.8 |   67.8749 |                70.2 |                 85.4 |                  44.1 |                40.7 |         77.1 |                    82.1 |                          49.3 |
|   2008 |      47.5708  |        42.7117 |                  60.7 |   59.3058 |   48.888   |                            62.4 |               17.8 |     79.1963 |          16.5 |   67.594  |                70.2 |                 85.2 |                  43.3 |                40.7 |         77.2 |                    81.7 |                          49.4 |
|   2009 |      48.6672  |        43.3489 |                  61   |   58.4896 |   48.8405  |                            62.8 |               18.1 |     79.5329 |          16.8 |   67.9698 |                69.3 |                 85.1 |                  43.3 |                40.7 |         77.1 |                    82   |                          49.4 |
|   2010 |      48.73    |        42.0667 |                  61.3 |   59.0103 |   48.758   |                            62.5 |               17.6 |     79.6186 |          17.2 |   67.9281 |                69   |                 85   |                  43.1 |                40.2 |         77   |                    81.7 |                          49.3 |
|   2011 |      50.0372  |        42.7734 |                  61.2 |   58.7424 |   48.1804  |                            62.2 |               18.2 |     79.4328 |          17.5 |   68.4267 |                69.5 |                 84.8 |                  43.1 |                40.1 |         76.7 |                    81.9 |                          49.2 |


The data has been cleaned already. I do a quick check to confirm the data is complete and in a usable format.

There are no missing values, and the percentages are all of data type `float`, which is perfect for my purposes.

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 42 entries, 0 to 41
Data columns (total 18 columns):
Year                             42 non-null int64
Agriculture                      42 non-null float64
Architecture                     42 non-null float64
Art and Performance              42 non-null float64
Biology                          42 non-null float64
Business                         42 non-null float64
Communications and Journalism    42 non-null float64
Computer Science                 42 non-null float64
Education                        42 non-null float64
Engineering                      42 non-null float64
English                          42 non-null float64
Foreign Languages                42 non-null float64
Health Professions               42 non-null float64
Math and Statistics              42 non-null float64
Physical Sciences                42 non-null float64
Psychology                       42 non-null float64
Public Administration            42 non-null float64
Social Sciences and History      42 non-null float64
dtypes: float64(17), int64(1)
memory usage: 6.0 KB
```

## The Gender Gap in STEM

Let's first take a look at the historical narrative of the gender gap in STEM fields (Science, Technology, Engineering and Maths).

As the data set deals with percentages of women who completed Bachelor degrees each field, I can easily impute the corresponding data for men by subtracting the known percentage values from 100.

I will display the comparison of degrees awarded in each field by gender for the 6 STEM subjects in one figure.

I aim to use my ink judiciously: 
- using a colour palette suitable for colourblind readers
- removing plot spines
- removing plot tick parameters
- doing away with labels and legends
- using annotations, sparingly

{% highlight python %}
# Import
%matplotlib inline
import matplotlib.pyplot as plt

# Set our RBG colour palette
cb_dark_blue = (0/255,107/255,164/255)
cb_orange = (255/255, 128/255, 14/255)

# List our STEM fields
stem_cats = ['Engineering', 'Computer Science', 'Psychology', 'Biology', 'Physical Sciences', 'Math and Statistics']

# Initialise the figure
fig = plt.figure(figsize=(18, 3))
fig.suptitle('The Gender Gap in STEM College Majors in the US', y=1.1, fontsize='16', fontweight='bold')

# Iterate over our STEM categories, plotting the subplots
for sp in range(0,6):
    ax = fig.add_subplot(1,6,sp+1)
    ax.plot(women_degrees['Year'], women_degrees[stem_cats[sp]], c=cb_dark_blue, label='Women', linewidth=3)
    
    # Plot percentages for men
    ax.plot(women_degrees['Year'], 100-women_degrees[stem_cats[sp]], c=cb_orange, label='Men', linewidth=3)
    
    # Set axis limits and title
    ax.set_xlim(1968, 2011)
    ax.set_ylim(0,100)
    ax.set_title(stem_cats[sp])

    # Remove spines
    for key, value in ax.spines.items():
        value.set_visible(False)
    
    # Remove tick params
    ax.tick_params(bottom=False, top=False, left=False, right=False)
    
    # Annotate
    if sp == 0:
        ax.text(2005, 87, 'Men')
        ax.text(2002, 8, 'Women')
    elif sp == 5:
        ax.text(2005, 62, 'Men')
        ax.text(2001, 35, 'Women')

# Export the figure
plt.savefig('gender_stem_degrees.png')        
plt.show()
{% endhighlight %}

![gender_stem]({{ site.baseurl }}/images/gender_stem.png)

Interesting to note:

- Men are highly over-represented compared to women in the fields of `Engineering` and `Computer Science`  

- For the `Physical Sciences` and `Math and Statistics`, while men have historically been over-represented, we can see that the gap has narrowed over time to something potentially approaching parity  

- It is especially interesting to me to note that in the more 'human-centred' fields of `Psychology` and `Biology`, women take the lead, and are especially significantly over-represented in `Psychology`   

It would be an interesting project to investigate further the cultural shifts and evolving societal norms that influenced these transitions over time in all fields.

## Gender Gap across all Fields

Let's now do the same for all fields of education in our data set. 

I want to visualise how fields in STEM compare to fields in the Liberal Arts (e.g. languages, communications, social sciences, arts) and other fields.

To this end, this time I will group our degree fields into the three categories just mentioned (STEM, Liberal Arts, other), and display them column by column.

I will again do this in one figure, with a subplot for each individual field.

{% highlight python %}
%matplotlib inline
import matplotlib.pyplot as plt

# Set colourblind colour palette
cb_dark_blue = (0/255,107/255,164/255)
cb_orange = (255/255, 128/255, 14/255)

# List our 3 sets of degree categories
stem_cats = ['Psychology', 'Biology', 'Math and Statistics', 'Physical Sciences', 'Computer Science', 'Engineering']
lib_arts_cats = ['Foreign Languages', 'English', 'Communications and Journalism', 'Art and Performance', 'Social Sciences and History']
other_cats = ['Health Professions', 'Public Administration', 'Education', 'Agriculture','Business', 'Architecture']

# Create figure
fig = plt.figure(figsize=(12, 18))
fig.suptitle('The Gender Gap in College Majors in the US\n\n STEM                            Liberal Arts                            Other',
             fontsize=16, fontweight='bold', y=.94)

# Iterate over STEM cats, plotting subplots in the correct location
for i in range(0,18,3):
    ax = fig.add_subplot(6,3,i+1)
    r = int(i/3)
    ax.plot(women_degrees['Year'], women_degrees[stem_cats[r]], c=cb_dark_blue, label='Women', linewidth=3)
    ax.plot(women_degrees['Year'], 100-women_degrees[stem_cats[r]], c=cb_orange, label='Men', linewidth=3)
    
    # Set axis limits, yticks, titles
    ax.set_xlim(1968, 2011)
    ax.set_ylim(0,100)
    ax.set_yticks([0,100])
    ax.set_title(stem_cats[r])
    
    # Remove spines
    for key, value in ax.spines.items():
        value.set_visible(False)
    
    # Disable the tick label bottom for all charts
    ax.tick_params(bottom=False, top=False, left=False, right=False, labelbottom=False)

    
    # Add a horizontal line at the half-way point
    ax.axhline(50, c=(171/255, 171/255, 171/255), alpha=0.3)
    
    if i == 0:
        ax.text(2002, 82, 'Women')
        ax.text(2005, 14, 'Men')
    elif i == 15:
        ax.text(2005, 87, 'Men')
        ax.text(2002, 7, 'Women')


# Enable tick lables on the bottom chart
ax.tick_params(labelbottom=True)


# Iterate over LIBERAL ARTS cats, plotting our subplots in the correct location
for i in range(1, 14, 3):
    ax = fig.add_subplot(6,3,i+1)
    r = int((i-1)/3)
    ax.plot(women_degrees['Year'], women_degrees[lib_arts_cats[r]], c=cb_dark_blue, label='Women', linewidth=3)
    ax.plot(women_degrees['Year'], 100-women_degrees[lib_arts_cats[r]], c=cb_orange, label='Men', linewidth=3)
    
    # Set axis limits, yticks, titles
    ax.set_xlim(1968, 2011)
    ax.set_ylim(0,100)
    ax.set_yticks([0,100])
    ax.set_title(lib_arts_cats[r])
    
    # Remove spines
    for key, value in ax.spines.items():
        value.set_visible(False)
    
    # Disable the tick label bottom for all charts
    ax.tick_params(bottom=False, top=False, left=False, right=False, labelbottom=False)
    
    # Add a horizontal line at the half-way point
    ax.axhline(50, c=(171/255, 171/255, 171/255), alpha=0.3)
    
    if i == 1:
        ax.text(2002, 77, 'Women')
        ax.text(2005, 20, 'Men')

        
# Enable tick lables on bottom chart
ax.tick_params(labelbottom=True)

# Iterate over OTHER cats, plotting our subplots in the correct location
for i in range(2, 18, 3):
    ax = fig.add_subplot(6,3,i+1)
    r = int((i-2)/3)
    ax.plot(women_degrees['Year'], women_degrees[other_cats[r]], c=cb_dark_blue, label='Women', linewidth=3)
    ax.plot(women_degrees['Year'], 100-women_degrees[other_cats[r]], c=cb_orange, label='Men', linewidth=3)
    
    # Set axis limits, yticks, titles
    ax.set_xlim(1968, 2011)
    ax.set_ylim(0,100)
    ax.set_yticks([0,100])
    ax.set_title(other_cats[r])
    
    # Remove spines
    for key, value in ax.spines.items():
        value.set_visible(False)
    
    # Disable the tick label bottom for all charts
    ax.tick_params(bottom=False, top=False, left=False, right=False, labelbottom=False)
    
    # Add a horizontal line at the half-way point
    ax.axhline(50, c=(171/255, 171/255, 171/255), alpha=0.3)
    
    if i == 2:
        ax.text(2002, 92, 'Women')
        ax.text(2005, 5, 'Men')
    elif i == 17:
        ax.text(2005, 62, 'Men')
        ax.text(2003, 34, 'Women')
        
# Enable tick lables on bottom chart
ax.tick_params(labelbottom=True)

# Export figure
plt.savefig('gender_degrees.png')
plt.show()
{% endhighlight %}

![gender_degrees]({{ site.baseurl }}/images/gender_degrees.png)

# Conclusion

In this project I created visualisations to communicate the historical narrative of the gender gap in different fields of study.

I also practiced some techniques to remove chartjunk and maximise the data-ink ratio, with the aim of having these visuals work effectively as standalone artifacts.

It's quite interesting to see how the gaps have trended over time in different fields, and in my opinion, even more so to notice my responses to the results, which raise all sorts of interesting questions about my own conditioning and biases.

What are our biases when we think of certain fields and professions? Who do we picture more in these roles? What are the attributes of these fields and roles that go towards us unconsciously considering them more masculine or feminine? What were the events which brought about the changes in mindsets and behaviours and influenced all these transitions over time?

Would you have predicted the results panned out the way they did?
