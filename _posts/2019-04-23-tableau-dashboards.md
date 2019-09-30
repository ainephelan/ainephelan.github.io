---
layout: post
title:  "Tableau Dashboards"
date:   2019-04-23 14:21:29 +1000
categories: jekyll update
---
>_“To see a world in a grain of sand and a heaven in a wild flower, hold infinity in the palm of your hand and eternity in an hour."_  
>_- William Blake, Auguries of Innocence_

![dash_bunny_full]({{ site.baseurl }}/images/dash_bunny_full.png)


This page will be a home for my Tableau dashboards and stories.  I'll update with it new boards as I make them.

<h2><strong>Story: Bank Customer Segmentation</strong></h2>

The following story deep-dives into the customer segmentation of a fictitious bank in the United Kingdom. The original data set can be downloaded from [here](https://sds-platform-private.s3-us-east-2.amazonaws.com/uploads/P1-UK-Bank-Customers.csv).
 
I have visualised data for customers' geographic locations, bank balances, ages, genders and job classifications.

Click through the below story for some high level insights. Each page is interactive, and each feature acts as a filter for all the other features. You can select different bin values for age and bank balance.

<center>
  
<div class='tableauPlaceholder' id='viz1569802430506' style='position: relative'><noscript><a href='#'><img alt=' ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ba&#47;BankCustomerSegmentationDashboard_15697523286960&#47;SegmentationStory&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='BankCustomerSegmentationDashboard_15697523286960&#47;SegmentationStory' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ba&#47;BankCustomerSegmentationDashboard_15697523286960&#47;SegmentationStory&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /></object></div>                <script type='text/javascript'>                    var divElement = document.getElementById('viz1569802430506');                    var vizElement = divElement.getElementsByTagName('object')[0];                    vizElement.style.width='1016px';vizElement.style.height='991px';                    var scriptElement = document.createElement('script');                    scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    vizElement.parentNode.insertBefore(scriptElement, vizElement);                </script>

</center>


<h2><strong>Dashboard: Sales vs. Target by Department</strong></h2>
<p>This next dashboard represents sales data for a fictitious supplier of office furniture, supplies and technology. The original data set can be downloaded <a href="https://sds-platform-private.s3-us-east-2.amazonaws.com/uploads/P1-AmazingMartEU2.xlsx">here</a>.</p>

<p>The dashboard compares actual sales across time with sales targets. You can filter on the different categories or departments on the right to see how sales have been tracking for each area. I've also included a visualisation of sales value above or below target, to give a clear picture of sales performance for that month for the particular department.</p>



<center>
<div class='tableauPlaceholder' id='viz1568692605220' style='position: relative'><noscript><a href='#'><img alt=' ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Sa&#47;Salesvs_TargetbyDepartment&#47;Dashboard1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='Salesvs_TargetbyDepartment&#47;Dashboard1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Sa&#47;Salesvs_TargetbyDepartment&#47;Dashboard1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /></object></div>                <script type='text/javascript'>                    var divElement = document.getElementById('viz1568692605220');                    var vizElement = divElement.getElementsByTagName('object')[0];                    if ( divElement.offsetWidth > 800 ) { vizElement.style.width='1000px';vizElement.style.height='827px';} else if ( divElement.offsetWidth > 500 ) { vizElement.style.width='1000px';vizElement.style.height='827px';} else { vizElement.style.width='100%';vizElement.style.height='727px';}                     var scriptElement = document.createElement('script');                    scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    vizElement.parentNode.insertBefore(scriptElement, vizElement);                </script>
</center>
