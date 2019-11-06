---
layout: "post"
title: "Chicago Census"
---
After seeing that life expectancy has been increasing all throughout chicago since the 90's. It brought some interesting questions to mind. How were the demographics in Chicago
starting from the 1990's up to 2010. As I began browsing, I quickly learned census data was the way to go. Fortunately, the datahub from illinois.gov provided a summarized data file
for 2010. However, for the years 2000 and 1990, files were scanned to create PDFs. I scraped the census data for each community from the planning and development site at chicago.gov.
I then proceeded to manually transfer those numbers to excel with the same column names used in the 2010 census. Resources such as tabula only work on text-based PDFs. 
Most documents from the earlier census were scanned documents. I still need to manually transfer the numbers for 1990, once I do. I will update the post. 
```python
#import the necessary libraries
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib as mpl
```
```python
#using pandas library and 'read_csv' funcation to read csv file downloaded from chicago data portal
chicago_2010 = pd.read_excel('census_2010_2.xlsx')
chicago_2000 = pd.read_excel('census_2000.xlsx')
```
```python
#check for any null values in 2010 dataset
chicago_2010.isnull().values.any()
```
```python
#checking length of dataset
len(chicago_2010)
```
```python
#obtaining list of column titles
chicago_2010.columns
```
Looking over the column names I decided to rename them, for easier labeling.
```python
#renaming columns for simplicity
chicago_2010 = chicago_2010.rename(columns = {'Not Hispanic or Latino, White alone': 'White alone',
                                         'Not Hispanic or Latino, Black or African American alone' : 'Black or African American',
                                         'Not Hispanic or Latino, American Indian and Alaska Native alone': 'American Indian',
                                         'Not Hispanic or Latino, Asian alone' : 'Asian',
                                         'Not Hispanic or Latino, Native Hawaiian and Other Pacific Islander alone' : 'Native Hawaiian and other Pacific Islander',
                                         'Not Hispanic or Latino, Some Other Race alone': 'Some Other Race Alone',
                                         'Not Hispanic or Latino, Two or More Races': 'Two or More Races'})

chicago_2000 = chicago_2000.rename(columns = {'Not Hispanic or Latino, White alone': 'White alone',
                                          'Not Hispanic or Latino, Black or African American alone' : 'Black or African American',
                                          'Not Hispanic or Latino, American Indian and Alaska Native alone': 'American Indian',
                                          'Not Hispanic or Latino, Asian alone' : 'Asian',
                                          'Not Hispanic or Latino, Native Hawaiian and Other Pacific Islander alone' : 'Native Hawaiian and other Pacific Islander',
                                          'Not Hispanic or Latino, Some Other Race alone': 'Some Other Race Alone',
                                          'Not Hispanic or Latino, Two or More Races': 'Two or More Races'})
```
I then combined the renamed columns for column selection.
```python
#creating list of ethnicity choices for easy column selction
ethnicity_list = ['White alone', 'Black or African American', 'American Indian', 'Asian', 'Native Hawaiian and other Pacific Islander',
                  'Some Other Race Alone', 'Two or More Races', 'Hispanic or Latino']

```
Looking over the dataset, I realized the population for the male and female population weren't provided. Therefore, I combined the categories for the male and female population
to then use to create a column equaling to the sum of the male or female list.
```python
#creating list for male and female age choices for easy column selection
male_list = ['Male: Under 5 years old', 'Male: 5 to 9 years', 'Male: 10 to 14 years', 'Male: 15 to 17 years', 'Male: 18 and 19 years',
             'Male: 20 years', 'Male: 21 years', 'Male: 22 to 24 years', 'Male: 25 to 29 years', 'Male: 30 to 34 years',
             'Male: 35 to 39 years', 'Male: 40 to 44 years', 'Male: 45 to 49 years', 'Male: 50 to 54 years',
             'Male: 55 to 59 years', 'Male: 60 and 61 years', 'Male: 62 to 64 years', 'Male: 65 and 66 years',
             'Male: 67 to 69 years', 'Male: 70 to 74 years', 'Male: 75 to 79 years', 'Male: 80 to 84 years',
             'Male: 85 years and over']
female_list = ['Female: Under 5 years old', 'Female: 5 to 9 years', 'Female: 10 to 14 years', 'Female: 15 to 17 years',
               'Female: 18 and 19 years','Female: 20 years','Female: 21 years','Female: 22 to 24 years',
               'Female: 25 to 29 years','Female: 30 to 34 years','Female: 35 to 39 years',
               'Female: 40 to 44 years','Female: 45 to 49 years','Female: 50 to 54 years',
               'Female: 55 to 59 years','Female: 60 and 61 years','Female: 62 to 64 years','Female: 65 and 66 years',
               'Female: 67 to 69 years','Female: 70 to 74 years','Female: 75 to 79 years','Female: 80 to 84 years',
               'Female: 85 years and over']

```

Instead of looking at each age category independently. I decided to combine the ages into seven stages of life as described by Dr. Thomas Armstrong, executive director of the
American Institute for learning and Human Development. These combinations aren't universal amongst the field. However, for data visualization it allows to gain an insight to the 
age distribution amongst the different chicago communities.

```python
#combining categories of commonly defined life stages for both genres as well as individual.

#ages 0-9
infancy_childhood_combined = ['Male: Under 5 years old', 'Male: 5 to 9 years', 'Female: Under 5 years old', 'Female: 5 to 9 years']
infancy_childhood_males = ['Male: Under 5 years old', 'Male: 5 to 9 years']
infancy_childhood_females = ['Female: Under 5 years old', 'Female: 5 to 9 years']
#ages 10-19
adolescence_combined = ['Male: 10 to 14 years', 'Male: 15 to 17 years', 'Male: 18 and 19 years', 'Female: 10 to 14 years', 'Female: 15 to 17 years',
               'Female: 18 and 19 years']
adolescence_males = ['Male: 10 to 14 years', 'Male: 15 to 17 years', 'Male: 18 and 19 years']
adolescence_females =['Female: 10 to 14 years', 'Female: 15 to 17 years', 'Female: 18 and 19 years']
#ages 20-34
early_adulthood_combined = ['Male: 20 years', 'Male: 21 years', 'Male: 22 to 24 years', 'Male: 25 to 29 years', 'Male: 30 to 34 years', 
                            'Female: 20 years','Female: 21 years','Female: 22 to 24 years', 'Female: 25 to 29 years',
                            'Female: 30 to 34 years']
early_adulthood_males = ['Male: 20 years', 'Male: 21 years', 'Male: 22 to 24 years', 'Male: 25 to 29 years', 'Male: 30 to 34 years']
early_adulthood_females = ['Female: 20 years','Female: 21 years','Female: 22 to 24 years', 'Female: 25 to 29 years', 
                           'Female: 30 to 34 years']
#ages 35-49
midlife_combined = ['Male: 35 to 39 years', 'Male: 40 to 44 years', 'Male: 45 to 49 years', 'Female: 35 to 39 years',
                    'Female: 40 to 44 years','Female: 45 to 49 years'] 
midlife_males = ['Male: 35 to 39 years', 'Male: 40 to 44 years', 'Male: 45 to 49 years']
midlife_females = ['Female: 35 to 39 years', 'Female: 40 to 44 years','Female: 45 to 49 years']

#ages 50-79
mature_adulthood_combined = ['Male: 50 to 54 years',
                             'Male: 55 to 59 years', 'Male: 60 and 61 years', 'Male: 62 to 64 years', 'Male: 65 and 66 years',
                             'Male: 67 to 69 years', 'Male: 70 to 74 years', 'Male: 75 to 79 years', 'Female: 50 to 54 years',
                             'Female: 55 to 59 years','Female: 60 and 61 years','Female: 62 to 64 years','Female: 65 and 66 years',
                             'Female: 67 to 69 years','Female: 70 to 74 years','Female: 75 to 79 years']
mature_adulthood_males = ['Male: 50 to 54 years',
                          'Male: 55 to 59 years', 'Male: 60 and 61 years', 'Male: 62 to 64 years', 'Male: 65 and 66 years',
                          'Male: 67 to 69 years', 'Male: 70 to 74 years', 'Male: 75 to 79 years']
mature_adulthood_females = ['Female: 50 to 54 years',
                            'Female: 55 to 59 years','Female: 60 and 61 years','Female: 62 to 64 years','Female: 65 and 66 years',
                            'Female: 67 to 69 years','Female: 70 to 74 years','Female: 75 to 79 years']

#ages >80
late_adulthood_combined = ['Male: 80 to 84 years', 'Male: 85 years and over','Female: 80 to 84 years', 'Female: 85 years and over']
late_adulthood_males = ['Male: 80 to 84 years', 'Male: 85 years and over']
late_adulthood_females = ['Female: 80 to 84 years', 'Female: 85 years and over']

```
To double check all the numbers added up to the total population reported in the data set, I proceeded to create a column for the male and female categories separately as well as 
combined.

```python
#creating columns pertaining to the combined categories for 2010 census

chicago_2010['infancy_childhood_combined'] = chicago_2010[infancy_childhood_combined].sum(axis = 1)
chicago_2010['infancy_childhood_males'] = chicago_2010[infancy_childhood_males].sum(axis = 1)
chicago_2010['infancy_childhood_females'] = chicago_2010[infancy_childhood_females].sum(axis = 1)

chicago_2010['adolescence_combined'] = chicago_2010[adolescence_combined].sum(axis = 1)
chicago_2010['adolescence_males'] = chicago_2010[adolescence_males].sum(axis = 1)
chicago_2010['adolescence_females'] = chicago_2010[adolescence_females].sum(axis = 1)

chicago_2010['early_adulthood_combined'] = chicago_2010[early_adulthood_combined].sum(axis = 1)
chicago_2010['early_adulthood_males'] = chicago_2010[early_adulthood_males].sum(axis = 1)
chicago_2010['early_adulthood_females'] = chicago_2010[early_adulthood_females].sum(axis = 1)

chicago_2010['midlife_combined'] = chicago_2010[midlife_combined].sum(axis = 1)
chicago_2010['midlife_males'] = chicago_2010[midlife_males].sum(axis = 1)
chicago_2010['midlife_females'] = chicago_2010[midlife_females].sum(axis = 1)

chicago_2010['mature_adulthood_combined'] = chicago_2010[mature_adulthood_combined].sum(axis = 1)
chicago_2010['mature_adulthood_males'] = chicago_2010[mature_adulthood_males].sum(axis = 1)
chicago_2010['mature_adulthood_females'] = chicago_2010[mature_adulthood_females].sum(axis = 1)

chicago_2010['late_adulthood_combined'] = chicago_2010[late_adulthood_combined].sum(axis = 1)
chicago_2010['late_adulthood_males'] = chicago_2010[late_adulthood_males].sum(axis = 1)
chicago_2010['late_adulthood_females'] = chicago_2010[late_adulthood_females].sum(axis = 1)
#%%
```
```python
#creating male and female population total using lists created prior
#male population
chicago_2010['Male Population'] = chicago_2010[male_list].sum(axis = 1)
#female population
chicago_2010['Female Population'] = chicago_2010[female_list].sum(axis = 1)
```
```python
#creating list of predefined life stages for both genres

categories_combined = ['infancy_childhood_combined', 'adolescence_combined', 'early_adulthood_combined', 'midlife_combined',
                       'mature_adulthood_combined', 'late_adulthood_combined']
```
Now, I wanted to make sure the Total population was equal to the combined categories as well as the male and female population that were created using the lists for male
and female categories.

```python
#checking predefined life stages for both genress are equal to 'total population column'
chicago_2010['Total Population'] == chicago_2010[categories_combined].sum(axis = 1) 
```

```python
#checking if the seperate male and female populations are equal to the total population reported in dataset
chicago_2010['Total Population'] == chicago_2010[['Male Population', 'Female Population']].sum(axis = 1) 
```
I subsetted my pertaining to the male, female, and combined population. This way to visualize the percents across the communities.
```python
#subsetting data for combined predefined life stages for 2010.

combined_population = ['infancy_childhood_combined', 'adolescence_combined', 'early_adulthood_combined', 'midlife_combined', 
                         'mature_adulthood_combined', 'late_adulthood_combined']
combined_population_df = chicago_2010[combined_population].copy()
combined_population_df['Geog'] = chicago_2010['Geog'].copy()
combined_population_df = combined_population_df.set_index('Geog')
#%%

#subsetting data for male predefined life stages for 2010.

male_categories_population = ['infancy_childhood_males', 'adolescence_males', 'early_adulthood_males', 'midlife_males', 
                              'mature_adulthood_males', 'late_adulthood_males']
male_categories_pop_df = chicago_2010[male_categories_population].copy()
male_categories_pop_df['Geog'] = chicago_2010['Geog'].copy()
male_categories_pop_df = male_categories_pop_df.set_index('Geog')
#%%

#subsetting data for female predefined life stages for 2010.

female_categories_population = ['infancy_childhood_females', 'adolescence_females', 'early_adulthood_females', 'midlife_females', 
                                'mature_adulthood_females', 'late_adulthood_females']
female_categories_pop_df = chicago_2010[female_categories_population].copy()
female_categories_pop_df['Geog'] = chicago_2010['Geog'].copy()
female_categories_pop_df = female_categories_pop_df.set_index('Geog')

```
Now, I can visualize the age demographics amongst the communities. By looking at the graphs one can see popular areas for early adulthood (20-34 years) population are Lake View, The Loop,
and Lincoln Park. While the rest of the communities have a rather even distribution. This information can be vital for companies understanding the age range of their customers
in an area they wish to do business in.
```python
#visualization of each neighborhood and their predefined life stages   
    
#adjusting the text size for the labels    
mpl.rcParams['font.size'] = 8
   
fig = plt.figure(figsize = (18, 12))    

for i, (idx, row) in enumerate(combined_population_df.iterrows()):
    ax = fig.add_subplot(7, 11, i + 1) #each row of the dataframe is a plot
    ax.pie(row, labels= list(('',) * len(row)), autopct='%1.1f%%', pctdistance = 1.0, startangle=30) #labels are set to len of row to equal the number of values returned
    ax.set_title(idx)  

fig.legend(combined_population, ncol = len(combined_population), loc = 'lower center')
plt.savefig('combined_population.png')  
```
<img class = "image fit" src ="{{ 'assets/images/combined_population.png' | relative_url }}">

```python
#visualization of each neighborhood and their male age demographics

#initiate figure outside of for loop    
fig = plt.figure(figsize = (18, 12))      

for i, (idx, row) in enumerate(male_categories_pop_df.iterrows()):
    ax = fig.add_subplot(7, 11, i + 1) 
    ax.pie(row, labels= list(('',) * len(row)), autopct='%1.1f%%', pctdistance = 1.0, startangle=30) 
    ax.set_title(idx)  
    
fig.legend(male_categories_population, ncol = len(male_categories_population), loc = 'lower center')
plt.savefig('male_categories_population.png')   
```
<img class = "image fit" src ="{{ 'assets/images/male_categories_population.png' | relative_url }}">

```python
#visualization of each neighborhood and their female age demographics

#initiate figure outside of for loop    
fig = plt.figure(figsize = (18, 12))      

for i, (idx, row) in enumerate(female_categories_pop_df.iterrows()):
    ax = fig.add_subplot(7, 11, i + 1) #each row of the dataframe is a plot
    ax.pie(row, labels= list(('',) * len(row)), autopct='%1.1f%%', pctdistance = 1.0, startangle=30) 
    ax.set_title(idx)  
    
fig.legend(female_categories_population, ncol = len(female_categories_population), loc = 'lower center')
plt.savefig('female_categories_population.png')
```
<img class = "image fit" src ="{{ 'assets/images/female_categories_population.png' | relative_url }}">

Another area of interest for businesses is for them to know the race distribution amongst the different communities. People will consume different  products depending on their 
race because of difference in cultures. Such as food, and clothes. 

```python

#demographics of ethnicity
ethnicity_df = chicago_2010[ethnicity_list].copy()
ethnicity_df['Geog'] = chicago_2010['Geog'].copy()
ethnicity_df = ethnicity_df.set_index('Geog')
```

```python
#2010 census
fig = plt.figure(figsize = (18, 12))    

for i, (idx, row) in enumerate(ethnicity_df.iterrows()):
    ax = fig.add_subplot(7, 11, i + 1) #each row of the dataframe is a plot
    row = row #returns a series of each neighborhood and their corresponding numbers
    ax.pie(row, labels= list(('',) * len(row)), autopct='%1.1f%%', pctdistance = 1.0, startangle=30)
    ax.set_title(idx)  

fig.suptitle('2010 Census')  
fig.legend(ethnicity_list, ncol = len(ethnicity_list), loc = 'lower center')
plt.savefig('ethnicity_list_2010.png') 
```
<img class = "image fit" src ="{{ 'assets/images/ethnicity_list_2010.png' | relative_url }}">

Afterwards, I wish to visualized the difference from 2000 to 2010.
```python
#2000 census 

#demographics of ethnicity
ethnicity_df2 = chicago_2000[ethnicity_list].copy()
ethnicity_df2['Geog'] = chicago_2000['Geog'].copy()
ethnicity_df2 = ethnicity_df2.set_index('Geog')
```

```python
#2000 census
fig = plt.figure(figsize = (18, 12)) 

for i, (idx, row) in enumerate(ethnicity_df2.iterrows()):
    ax = fig.add_subplot(7, 11, i + 1) #each row of the dataframe is a plot
    row = row #returns a series of each neighborhood and their corresponding numbers
    ax.pie(row, labels= list(('',) * len(row)), autopct='%1.1f%%', pctdistance = 1.0, startangle=30)
    ax.set_title(idx)  
    
fig.suptitle('2000 Census')      
fig.legend(ethnicity_list, ncol = len(ethnicity_list), loc = 'lower center')
plt.savefig('ethnicity_list_2000.png') 
```
<img class = "image fit" src ="{{ 'assets/images/ethnicity_list_2000.png' | relative_url }}">

Not surprisingly, having grown up in Chicago and having to travel from the westside, northside, and southside the race demographics were what I expected. For visitors, or those 
wanting to move to Chicago can quickly visualize the racial percents amongst the different communities. However, interestingly Belmont Cragin, Hermosa, Avondale, and Logan Square
all had about 20% habitants identifying as 'some other race alone' in 2000. But in 2010 that 20% most likely checked off as 'Hispanic or Latino' in 2010. This goes to show
the importance in how each race is defined as.