---
layout: post
title:  "Mind the Gap!"
date:   2019-03-15 20:40:42 +1000
categories: jekyll update
---

![train](https://raw.githubusercontent.com/ainephelan/ainephelan.github.io/master/images/People_Train.jpg)

## Visualising the Gender Gap in College Majors  
Today we're going to look at creating visualisations which communicate the historical narrative of the gender gap in different fields of study in the US.

We'll use the already cleaned data set described below, and we're going to take the opportunity to practice some techniques to eliminate [chartjunk](https://en.wikipedia.org/wiki/Chartjunk) and maximise the [data-ink ratio](https://infovis-wiki.net/wiki/Data-Ink_Ratio).  

All the better to see you with my dear!


### Context
The [National Center for Education Statistics](https://nces.ed.gov/programs/digest/current_tables.asp) in the US releases a data set annually containing the percentage of bachelor's degrees granted to women since 1970. The data set is broken up into 17 categories of degrees, with each column as a separate category.

[Randal Olson](http://www.randalolson.com/2014/06/14/percentage-of-bachelors-degrees-conferred-to-women-by-major-1970-2012/), a data scientist from the University of Pennsylvania, has cleaned the data set for years 1970 to 2012 and made it available on his personal website. 

Gender parity is a topic close to my own heart, and at least here in Australia, it appears we still have [a long way to go](https://blog.csiro.au/where-are-all-the-women-in-stem/) in [bridging the gap](https://www.sbs.com.au/news/women-leave-stem-over-pay-gap-study-finds).

News articles aside, let's use Randal's [cleaned data](http://www.randalolson.com/wp-content/uploads/percent-bachelors-degrees-women-usa.csv) to see some historical differences between the genders regarding their chosen fields of study in the US.

Note, due to the nature of the historical data available to us, for the purposes of this analysis we will be treating gender as a binary construct only.

## Load the Data
Let's load our data and do some initial exploration of our data set.

It is not large and we can easily display it in its entirety.

{% highlight python %}
import pandas as pd

women_degrees = pd.read_csv('percent-bachelors-degrees-women-usa.csv')
women_degrees.head()
{% endhighlight %}

![gender_head](https://raw.githubusercontent.com/ainephelan/ainephelan.github.io/master/images/gender_head.png)

The data has been cleaned already. We do a quick check to confirm the data is complete and in a usable format.

There are no missing values, and our percentages are all of data type `float`, which is perfect for our use.

<img src="https://raw.githubusercontent.com/ainephelan/ainephelan.github.io/master/images/gender_datatype.png" alt="drawing" width="300px"/>


## The Gender Gap in STEM

Let's first take a look at the historical narrative of the gender gap in STEM fields (Science, Technology, Engineering and Maths).

As our data set deals with percentages of women who completed Bachelor degrees each field, we can easily impute the corresponding data for men by subtracting our known percentage values from 100.

We will display our comparison of degrees awarded in each field by gender for our 6 STEM subjects in one figure.

We will use our ink judiciously: 
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

# Initialise our figure
fig = plt.figure(figsize=(18, 3))
fig.suptitle('The Gender Gap in STEM College Majors in the US', y=1.1, fontsize='16', fontweight='bold')

# Iterate over our STEM cats, plotting our subplots
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

# Export our figure
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

We want to visualise how fields in STEM compare to fields in the Liberal Arts (e.g. languages, communications, social sciences, arts) and other fields.

To this end, this time we will group our degree fields into the three categories just mentioned (STEM, Liberal Arts, other), and display them column by column.

We will again do this in one figure, with a subplot for each individual field.

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

# Create our figure
fig = plt.figure(figsize=(12, 18))
fig.suptitle('The Gender Gap in College Majors in the US\n\n STEM                            Liberal Arts                            Other',
             fontsize=16, fontweight='bold', y=.94)

# Iterate over our STEM cats, plotting our subplots in the correct location
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


# Iterate over our LIBERAL ARTS cats, plotting our subplots in the correct location
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

# Iterate over our OTHER cats, plotting our subplots in the correct location
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

# Export our figure
plt.savefig('gender_degrees.png')
plt.show()
{% endhighlight %}

![gender_degrees]({{ site.baseurl }}/images/gender_degrees.png)

# Conclusion

In this project we to used visualisations to communicate the historical narrative of the gender gap in different fields of study.

It's quite interesting to see how the gaps have trended over time in different fields, and in my opinion, even more so to notice our responses to the results. These responses raise all sorts of interesting questions about our own conditioning and biases.

What are our biases when we think of certain fields and professions? Who do we picture more in these roles? What are the attributes of these fields and roles that go towards us unconsciously considering them more masculine or feminine? 

What were the events which brought about the changes in mindsets and behaviours and influenced all these transitions over time?

Would you have predicted the results panned out the way they did?
