---
layout: post
title:  "Community Hubs Australia - Datathon for Social Good"
date:   2020-03-30 20:40:42 +1000
categories: jekyll update
---
 

>_"If you want to lift yourself up, lift up someone else."_   
>_- Booker T. Washington_   
  
![hands]({{ site.baseurl }}/images/cha_dfsg_images/Hands.jpg)  
  
&nbsp;  
  
# Community Hubs Australia - Datathon for Social Good

Report by Áine Phelan and Patrick Healy

### Context
Patrick and I participated in Melbourne’s first [Datathon for Social Good](https://www.ourcommunity.com.au/datathon), serving [Community Hubs Australia](https://www.communityhubs.org.au/) (CHA), a not-for-profit organisation which helps to build social cohesion Australia-wide, particularly amongst the migrant community.  

Community Hubs and their programmes are gateways that engage and support migrant women with young children, where isolation can be a serious social issue. There are already dozens of hubs in operation, and future plans to expand. Hubs are hosted in schools to provide a range of activities for children and adults, such as English lessons, maternal and child health, and playgroups. They provide welcoming places for migrant families, and especially these mothers, to come and connect.  

The challenge in this highly motivating datathon was to use the provided data to further insight into the activities and impacts of Community Hubs.  

### Data
The original datathon repostitory can be found [here](https://gitlab.com/mechakozai/datathon-social-good).  
  
### Problem Statement  
Out team chose the Internal Data track challenge. Our challenge was to use internal organisational data to answer questions such as, _"What is a ‘typical’ hub and what is an ‘outlier’?"_

In addition to the above prompt, we were encouraged to be as creative as we liked, should any we see any other way of using the data to help CHA ongoing.  
  
### Scope
Our report is comprised of 2 main parts:  

#### 1. Analysis of the 'Typical Hub' based on Key Measures of Success  
We took the approach of focusing on the measures of success important to CHA, as advised to us during the datathon briefing. These included (but were not limited to):  
- Engagement
- Diversity of programs
- 'Spread' of activities for children and adults

For these and other measures detailed in this report we created:  
- a distribution of values for each measure - finding the 'typical' values, the general ranges, and also noting extreme outliers
- a breakdown of these values at an individual hub level - mapping a value back to its hub, and using heatmapping to compare these values to each other at a hub level  

We have come at each key success measure in a variety of different ways, looking at each from multiple angles, as we believe that [multiple contexts](https://vicki.substack.com/p/all-numbers-are-made-up-some-are) are crucial to help give as full an understanding of the data as possible.  
  
#### 2. Creation of a Hidden Markov Model  
Another key measure of success was hub use over time. To assist with this measure we created a [Hidden Markov Model](https://en.wikipedia.org/wiki/Hidden_Markov_model) (HMM), to allow the oganisation to compare a single hub to itself over time, to see how it is tracking against its own past behaviour.  
    
### Data Considerations  
#### Programme Categories  
For the purposes of our analysis we decided to narrow our focus to activities belonging to the the following categories of programmes only:
- Programmed Activities  
- Education and Training  
  
We opted to focus on these 2 categories for the following reasons:   
- We felt the above 2 categories represented CHA's core business. They are frequent, regular and are proactively planned and scheduled. These are the categories hubs have most control over, and they form the overwhelming bulk of CHA's interactions with the community
- Other categories (One Off Events and Service Referrals) appeared more ad-hoc, reactive and irregular, and we found issues with data availability and reliability. For example, though many Service Referral activities were recorded, there was an almost total lack of participants recorded

#### Participants
Based on advice from CHA representatives, we focused our analysis on Child and Adult participants only.

#### Activity Dates
When analysing for _"typical"_ behaviour, we found the data was heavily skewed by the month of January, with the number of activities proportionally much smaller here than for any other month.  
  
Thus, we made the decision to treat January as an extreme outlier and, where we deemed necessary, removed it from our analysis.  
   
![Jan]({{ site.baseurl }}/images/cha_dfsg_images/Jan_activities.png)   
&nbsp;  
  
  
## 1. What is a Typical Hub? 
### 1.1 Hub Age
  
We first took a look at how long hubs had been running:  
- at the general level   
- at an individial level, and compared with one another  
   
&nbsp;  
  
    
![1_age]({{ site.baseurl }}/images/cha_dfsg_images/1_age.png)  
  
&nbsp;  
  
  
![2_ageheatmap]({{ site.baseurl }}/images/cha_dfsg_images/2_ageheatmap.png)  
  
&nbsp;  



### 1.2 Hub Activities
In this section we took an initial look at activities and answered questions around:  
- how many activities do hubs run?  
- who do these activities cater to?  
  
&nbsp;  
  
    

![3_activities]({{ site.baseurl }}/images/cha_dfsg_images/3_activities.png)  
  
&nbsp;  
  
    
![4_activities]({{ site.baseurl }}/images/cha_dfsg_images/4_activities.png)  
  
&nbsp;  
   
          
![5_activities_heatmap]({{ site.baseurl }}/images/cha_dfsg_images/5_activities_heatmap.png)  
  
&nbsp;  
  
    
### 1.3 Engagement  
A key measure of success for CHA, in this section we dove into the data on participation to help us understand how engaged the community was at each hub.  

At a general and an individual hub level, we asked and answered questions to do with:   
- average participation per activity  
- throughput of participants per month   
- the breakdown of participation by adults and children   
  
&nbsp;  
  
    
![6_participants]({{ site.baseurl }}/images/cha_dfsg_images/6_participants.png)   
  
&nbsp;  
  
      
![7_part_heatmap]({{ site.baseurl }}/images/cha_dfsg_images/7_part_heatmap.png) 
  
&nbsp;  
  
      
  
![8_part]({{ site.baseurl }}/images/cha_dfsg_images/8_part.png)     
  
&nbsp;  
  
    
  
![9_part]({{ site.baseurl }}/images/cha_dfsg_images/9_part.png)   
  
&nbsp;  
  
    
  
![10_part_heatmap]({{ site.baseurl }}/images/cha_dfsg_images/10_part_heatmap.png)  
  
&nbsp;  
  
     

### 1.4 Diversity of Programmes  
Another key measure of success, this section measures and compares hubs on the diversity of programmes offered.   

Looking to Ecology and Information Theory, the concept of diversity can be quantified taking [2 main factors](http://www.countrysideinfo.co.uk/simpsons.htm) into consideration:  
- Richness - the number of different species in a sample. The greater the number of species, the 'richer' and more diverse the sample is
- Evenness - the relative abundance of these species. The more evenly distributed the number of individuals in different species are, the more 'even' and diverse the sample is
  
So, for hub programmes to be highly diverse, we need to see hubs with  
- a high number of different programmes on offer  
- an even distribution of activities within each of those programmes  

For our calculations we used [Simpson's Index of Diveristy](https://en.wikipedia.org/wiki/Diversity_index#Simpson_index), which ranges from 0 to 1, with 0 being least diverse, and 1 being most diverse.   
  
&nbsp;  
  
    
    
![11_diversity.]({{ site.baseurl }}/images/cha_dfsg_images/11_diversity.png)   
  
&nbsp;  
  
    
  
![12_diversity_heatmap]({{ site.baseurl }}/images/cha_dfsg_images/12_diversity_heatmap.png)   
  
&nbsp;  
  
    


### 1.5 'Spread' of activities for children and adults

In this section we addressed another key success measure, asking ourselves questions about the proportion of activities on offer to adults, compared with those on offer to children.   
   
Again, we came at this in a number of different ways to help with our understanding, looking at:    
- adult-only and child-only activities
- mixed activities (those where both children and adults attended)  
   
It is worth noting in that the following ratio could not be calculated for all hubs, as there were:  
- 20 hubs with 0 child-only activities  
- 2 hubs with 0 child-only and 0 adult-only activities  
  
&nbsp;  
  
    
  
![13_ratio]({{ site.baseurl }}/images/cha_dfsg_images/13_ratio.png)   
  
&nbsp;  
  
    
  
![14_ratio]({{ site.baseurl }}/images/cha_dfsg_images/14_ratio.png)   
  
&nbsp;  
  
    
  
![15_ratio_heatmap]({{ site.baseurl }}/images/cha_dfsg_images/15_ratio_heatmap.png)   
  
&nbsp;  
  
    
  
  
# Recap

In summary, we:
- Tackled the Internal Data Track challenge  
- Analysed hubs from the point of view of several Key Success Measures which we believed to be the most important to CHA  
- Looked at hubs from general, individual, and comparative levels  
- Modelled how changes in an individual hub can be tracked over time, comparing itself now to itself in a previous state  
  
Thanks to CHA and the organisers for opening your doors to us for this challenge! We learned a lot and we hope we were able to contribute in a meaningful way to the important work you are doing for women, for migrants and for those at risk from isolation.  

Keep up the amazing work!  
