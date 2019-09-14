---
layout: post
title:  "Homes Under the Hammer - a Prediction Pipeline with Linear Regression"
date:   2019-08-15 14:40:42 +1000
categories: jekyll update
---

## Predicting House Sale Prices with Linear Regression

# Introduction

The purpose of this project is to build a Linear Regression work flow by creating a pipeline of functions as follows:

#### data in -> `transform_features()` -> `select_features()` -> `train_and_test()` -> evaluation out


Once I have my functions set up it should be easy to iterate on different models, playing around with the parameters to see what results are yielded.

`transform_features()`  
This is where I will do my feature engineering - imputing values and stripping out nulls, unwanted data types, columns with data leakage and incorrect values.

`select_features()`  
I'll use this function to select columns to include as features. I'll look at correlation, in-column variance and at transforming nominal and ordinal data to categorical values.

`train_and_test()`  
Here I'll train and test the model, building it to handle holdout and k-fold cross validation.


### Data Set

I'm working with housing data for the city of Ames, Iowa, US for the period 2006 - 2010.

- Data column descriptions [here](https://s3.amazonaws.com/dq-content/307/data_description.txt).
- Info about why the data was collected [here](https://www.tandfonline.com/doi/abs/10.1080/10691898.2011.11889627)
- The dataset itself can be downloaded from [here](https://dsserver-prod-resources-1.s3.amazonaws.com/235/AmesHousing.txt)

Let's import the data.

{% highlight python %}
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import cross_val_score, KFold, train_test_split

data = pd.read_csv('AmesHousing.tsv', sep='\t')
pd.set_option('max_columns', 100)
data.head()
{% endhighlight %}

```
|   Order |       PID |   MS SubClass | MS Zoning   |   Lot Frontage |   Lot Area | Street   |   Alley | Lot Shape   | Land Contour   | Utilities   | Lot Config   | Land Slope   | Neighborhood   | Condition 1   | Condition 2   | Bldg Type   | House Style   |   Overall Qual |   Overall Cond |   Year Built |   Year Remod/Add | Roof Style   | Roof Matl   | Exterior 1st   | Exterior 2nd   | Mas Vnr Type   |   Mas Vnr Area | Exter Qual   | Exter Cond   | Foundation   | Bsmt Qual   | Bsmt Cond   | Bsmt Exposure   | BsmtFin Type 1   |   BsmtFin SF 1 | BsmtFin Type 2   |   BsmtFin SF 2 |   Bsmt Unf SF |   Total Bsmt SF | Heating   | Heating QC   | Central Air   | Electrical   |   1st Flr SF |   2nd Flr SF |   Low Qual Fin SF |   Gr Liv Area |   Bsmt Full Bath |   Bsmt Half Bath |   Full Bath |   Half Bath |   Bedroom AbvGr |   Kitchen AbvGr | Kitchen Qual   |   TotRms AbvGrd | Functional   |   Fireplaces | Fireplace Qu   | Garage Type   |   Garage Yr Blt | Garage Finish   |   Garage Cars |   Garage Area | Garage Qual   | Garage Cond   | Paved Drive   |   Wood Deck SF |   Open Porch SF |   Enclosed Porch |   3Ssn Porch |   Screen Porch |   Pool Area |   Pool QC | Fence   | Misc Feature   |   Misc Val |   Mo Sold |   Yr Sold | Sale Type   | Sale Condition   |   SalePrice |
|--------:|----------:|--------------:|:------------|---------------:|-----------:|:---------|--------:|:------------|:---------------|:------------|:-------------|:-------------|:---------------|:--------------|:--------------|:------------|:--------------|---------------:|---------------:|-------------:|-----------------:|:-------------|:------------|:---------------|:---------------|:---------------|---------------:|:-------------|:-------------|:-------------|:------------|:------------|:----------------|:-----------------|---------------:|:-----------------|---------------:|--------------:|----------------:|:----------|:-------------|:--------------|:-------------|-------------:|-------------:|------------------:|--------------:|-----------------:|-----------------:|------------:|------------:|----------------:|----------------:|:---------------|----------------:|:-------------|-------------:|:---------------|:--------------|----------------:|:----------------|--------------:|--------------:|:--------------|:--------------|:--------------|---------------:|----------------:|-----------------:|-------------:|---------------:|------------:|----------:|:--------|:---------------|-----------:|----------:|----------:|:------------|:-----------------|:-----------:|
|       1 | 526301100 |            20 | RL          |            141 |      31770 | Pave     |     nan | IR1         | Lvl            | AllPub      | Corner       | Gtl          | NAmes          | Norm          | Norm          | 1Fam        | 1Story        |              6 |              5 |         1960 |             1960 | Hip          | CompShg     | BrkFace        | Plywood        | Stone          |            112 | TA           | TA           | CBlock       | TA          | Gd          | Gd              | BLQ              |            639 | Unf              |              0 |           441 |            1080 | GasA      | Fa           | Y             | SBrkr        |         1656 |            0 |                 0 |          1656 |                1 |                0 |           1 |           0 |               3 |               1 | TA             |               7 | Typ          |            2 | Gd             | Attchd        |            1960 | Fin             |             2 |           528 | TA            | TA            | P             |            210 |              62 |                0 |            0 |              0 |           0 |       nan | nan     | nan            |          0 |         5 |      2010 | WD          | Normal           |      215000 |
|       2 | 526350040 |            20 | RH          |             80 |      11622 | Pave     |     nan | Reg         | Lvl            | AllPub      | Inside       | Gtl          | NAmes          | Feedr         | Norm          | 1Fam        | 1Story        |              5 |              6 |         1961 |             1961 | Gable        | CompShg     | VinylSd        | VinylSd        | None           |              0 | TA           | TA           | CBlock       | TA          | TA          | No              | Rec              |            468 | LwQ              |            144 |           270 |             882 | GasA      | TA           | Y             | SBrkr        |          896 |            0 |                 0 |           896 |                0 |                0 |           1 |           0 |               2 |               1 | TA             |               5 | Typ          |            0 | nan            | Attchd        |            1961 | Unf             |             1 |           730 | TA            | TA            | Y             |            140 |               0 |                0 |            0 |            120 |           0 |       nan | MnPrv   | nan            |          0 |         6 |      2010 | WD          | Normal           |      105000 |
|       3 | 526351010 |            20 | RL          |             81 |      14267 | Pave     |     nan | IR1         | Lvl            | AllPub      | Corner       | Gtl          | NAmes          | Norm          | Norm          | 1Fam        | 1Story        |              6 |              6 |         1958 |             1958 | Hip          | CompShg     | Wd Sdng        | Wd Sdng        | BrkFace        |            108 | TA           | TA           | CBlock       | TA          | TA          | No              | ALQ              |            923 | Unf              |              0 |           406 |            1329 | GasA      | TA           | Y             | SBrkr        |         1329 |            0 |                 0 |          1329 |                0 |                0 |           1 |           1 |               3 |               1 | Gd             |               6 | Typ          |            0 | nan            | Attchd        |            1958 | Unf             |             1 |           312 | TA            | TA            | Y             |            393 |              36 |                0 |            0 |              0 |           0 |       nan | nan     | Gar2           |      12500 |         6 |      2010 | WD          | Normal           |      172000 |
|       4 | 526353030 |            20 | RL          |             93 |      11160 | Pave     |     nan | Reg         | Lvl            | AllPub      | Corner       | Gtl          | NAmes          | Norm          | Norm          | 1Fam        | 1Story        |              7 |              5 |         1968 |             1968 | Hip          | CompShg     | BrkFace        | BrkFace        | None           |              0 | Gd           | TA           | CBlock       | TA          | TA          | No              | ALQ              |           1065 | Unf              |              0 |          1045 |            2110 | GasA      | Ex           | Y             | SBrkr        |         2110 |            0 |                 0 |          2110 |                1 |                0 |           2 |           1 |               3 |               1 | Ex             |               8 | Typ          |            2 | TA             | Attchd        |            1968 | Fin             |             2 |           522 | TA            | TA            | Y             |              0 |               0 |                0 |            0 |              0 |           0 |       nan | nan     | nan            |          0 |         4 |      2010 | WD          | Normal           |      244000 |
|       5 | 527105010 |            60 | RL          |             74 |      13830 | Pave     |     nan | IR1         | Lvl            | AllPub      | Inside       | Gtl          | Gilbert        | Norm          | Norm          | 1Fam        | 2Story        |              5 |              5 |         1997 |             1998 | Gable        | CompShg     | VinylSd        | VinylSd        | None           |              0 | TA           | TA           | PConc        | Gd          | TA          | No              | GLQ              |            791 | Unf              |              0 |           137 |             928 | GasA      | Gd           | Y             | SBrkr        |          928 |          701 |                 0 |          1629 |                0 |                0 |           2 |           1 |               3 |               1 | TA             |               6 | Typ          |            1 | TA             | Attchd        |            1997 | Fin             |             2 |           482 | TA            | TA            | Y             |            212 |              34 |                0 |            0 |              0 |           0 |       nan | MnPrv   | nan            |          0 |         3 |      2010 | WD          | Normal           |      189900 |
```

## Outliers
As per the [documentation](http://jse.amstat.org/v19n3/decock/DataDocumentation.txt), I remove the below 5 outliers.

{% highlight python %}
data.plot.scatter('SalePrice', 'Gr Liv Area')
plt.savefig('ames_outliers.png')
plt.show()

print(data[data['Gr Liv Area'] > 4000])

data.drop(data[data['Gr Liv Area'] > 4000].index, inplace=True)

print(data[data['Gr Liv Area'] > 4000])
{% endhighlight %}

![ames_outliers]({{ site.baseurl }}/images/ames_outliers.png)

```
Before: (5, 82)
After: (0, 82)
```

Let's begin by setting up my basic pipeline of helper functions that will let me quickly iterate on different models.

- `transform_features()`  
    For now, this just returns the dataframe
- `select_features()`  
    For now, this just returns the `Gr Liv` and `SalePrice` columns from the `data` dataframe
- `train_and_test()`  
    For now, this 
    - splits the `data` dataframe into `train` and `test`
    - trains a model using all numerical columns except our target `SalePrice` from the dataframe returned from `select_features()`
    - tests the model on the test set and evaluates, returning the `RMSE` value

### A Note on RMSE
The RMSE is the absolute fit of the model to the data - the measure of how close the model's predicted values are to the observed data. It is the standard deviation of the prediction errors, so tells us the average variability of our predictions above and below the line of best fit or regression line. As such, the lower the RMSE the more accurate we can say our model is at predicting the response. 

The RMSE is in the same units as our target variable, which makes for intuitive understanding of the model's evaluation.

e.g. if my RMSE is 50,000, I know that I am on average predicting \$50,000 above or below the observed house sale price.

{% highlight python %}
def transform_features(data):
    return data

def select_features(data):
    return data[['Gr Liv Area', 'SalePrice']]

def train_and_test(data):
    train, test = train_test_split(data, test_size=0.50, random_state = 1)

    target = 'SalePrice'
    features_df = select_features(data)
    feature_cols = features_df.select_dtypes(include=['integer', 'float']).drop('SalePrice', axis=1).columns
    
    lr = LinearRegression()
    lr.fit(train[feature_cols], train[target])
    
    predictions = lr.predict(test[feature_cols])
    
    mse = mean_squared_error(predictions, test[target])
    rmse = np.sqrt(mse)
    return rmse

# Return RMSE
train_and_test(data)   
{% endhighlight %}

```
54020.48763702078
```

## Feature Engineering

Let's experiment with transforming our features, then once I'm satisfied, build these transformations into my `transform_features()` function.


### Nulls / Missing Values
First I want to get a sense of how many nulls there are. I'm going to use the following to help me out.
- heatmap
- null count


{% highlight python %}
# Build heatmap to check for nulls

import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline

def plot_null_matrix(df, figsize=(18,15)):
    # Initiate the figure
    plt.figure(figsize=figsize)
    
    # Create a boolean dataframe based on whether values are null
    df_null = df.isnull()
    
    # Create a heatmap of the boolean dataframe: null=dark, non-null = light
    sns.heatmap(~df_null, cbar=False, yticklabels=False)   
    plt.xticks(rotation=90, size='x-large')
    plt.savefig('ames_heatmap.png')
    plt.show()

    
plot_null_matrix(data, figsize=(6,6))
{% endhighlight %}

![ames_heatmap]({{ site.baseurl }}/images/ames_heatmap.png)

Some columns appear almost totally null. Let's get some quantitive data around that.

{% highlight python %}
# Get percentage of nulls in a col
(data.isnull().sum().sort_values(ascending=False) / data.shape[0] *100)[:30]
{% endhighlight %}


```
Pool QC           99.623932
Misc Feature      96.410256
Alley             93.230769
Fence             80.478632
Fireplace Qu      48.615385
Lot Frontage      16.752137
Garage Qual        5.435897
Garage Yr Blt      5.435897
Garage Cond        5.435897
Garage Finish      5.435897
Garage Type        5.367521
Bsmt Exposure      2.837607
BsmtFin Type 2     2.769231
BsmtFin Type 1     2.735043
Bsmt Cond          2.735043
Bsmt Qual          2.735043
Mas Vnr Type       0.786325
Mas Vnr Area       0.786325
Bsmt Full Bath     0.068376
Bsmt Half Bath     0.068376
Garage Area        0.034188
Garage Cars        0.034188
Total Bsmt SF      0.034188
Bsmt Unf SF        0.034188
BsmtFin SF 2       0.034188
BsmtFin SF 1       0.034188
Electrical         0.034188
Exterior 2nd       0.000000
Exterior 1st       0.000000
Roof Matl          0.000000
dtype: float64
```




![gender_stem]({{ site.baseurl }}/images/ames_outliers.png)
![gender_stem]({{ site.baseurl }}/images/ames_outliers.png)
![gender_stem]({{ site.baseurl }}/images/ames_outliers.png)
![gender_stem]({{ site.baseurl }}/images/ames_outliers.png)

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}
