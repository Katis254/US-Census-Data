# US-Census-Data
This is an analysis of US Census Data according to their Racial Composition by state

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

import glob

files = glob.glob("C:\\Users\\KATIS\\Desktop\\Coding Projects\\Cleaning US Census Data\\*.csv")

print(files)

states_list = []
for filename in files:
    data=pd.read_csv(filename)
    states_list.append(data)
    
us_census = pd.concat(states_list)

#printing the columns and data types

print(us_census.columns)
print(us_census.dtypes)

#looking at the first five rows of the data
print(us_census.head())

#Changing Income Column to float64
for index in range(0,len(us_census["Income"])):
    string = str(us_census['Income'].iat[index])
    replace_dol = string.replace('$', '')
    replace_com = replace_dol.replace(',', '')
    us_census['Income'].iat[index] = replace_com

us_census["Income"] = pd.to_numeric(us_census['Income'])
us_census["Income"]

#Seperate the Gender Column to Men and Women

us_census['GenderPop'].head()

Men = []
Women = []
for index in range(0,len(us_census["GenderPop"])):
    string = str(us_census['GenderPop'].iat[index])
    replace = string.split('_')
    Men.append(replace[0])
    Women.append(replace[1])

us_census['Men'] = Men
us_census['Women'] = Women

us_census.head()

#Converting both Columns to numerical datatypes

for index in range(0,len(us_census["Men"])):
    string = str(us_census['Men'].iat[index])
    replace = string.replace('M', '')
    us_census['Men'].iat[index] = replace
    
for index in range(0,len(us_census["Women"])):
    string = str(us_census['Women'].iat[index])
    replace = string.replace('F', '')
    us_census['Women'].iat[index] = replace
    
us_census['Men'] = pd.to_numeric(us_census['Men'])
us_census['Women'] = pd.to_numeric(us_census['Women'])

us_census.head()

#Creating a Scatter Plot for Income Vs Number of Women per State
plt.scatter(us_census['Women'], us_census['Income'])
plt.title("Scatter Plot of Income vs. Number of Women per State")
plt.xlabel("Population of Women per State")
plt.ylabel("Income (in US Dollars)")
plt.show()
plt.clf()

#Checking the Nan Values
print(us_census['Women'])

us_census['Women'] = us_census['Women'].fillna(us_census['TotalPop'] - us_census['Men'])
print(us_census['Women'])

#check for duplicates
us_census.duplicated(subset = us_census.columns[1:])

#drop duplicates
census = us_census.drop_duplicates(subset = us_census.columns[1:])
census

#Make the Scatter plot again
plt.scatter(census['Women'], census['Income'])
plt.title("Scatter Plot of Income vs. Number of Women per State")
plt.xlabel("Population of Women per State")
plt.ylabel("Income (in US Dollars)")
plt.show()
plt.clf()

#look at the columns
census.columns

#Make histograms for each column
for race in ['Hispanic', 'White', 'Black', 'Native', 'Asian', 'Pacific']:
    for index in range(0,len(us_census)):    
        string = str(us_census[race].iat[index])
        replace = string.replace('%', '')
        if (replace == "nan"):
            replace = ""
        us_census[race].iat[index] = replace
    us_census[race] = pd.to_numeric(us_census[race])
    
us_census['Pacific'] = us_census['Pacific'].fillna(100 - us_census['Hispanic'] - us_census['White'] - us_census['Black'] - us_census['Native'] - us_census['Asian'])

census = us_census.drop_duplicates(subset = us_census.columns[1:])
census

for race in ['Hispanic', 'White', 'Black', 'Native', 'Asian', 'Pacific']:
    plt.hist(census[race])
    plt.title("Histogram of the Percentage of {} People per State".format(race))
    plt.xlabel("Percentage")
    plt.ylabel("Frequency")
    plt.show()
    plt.clf()
    
#Make BoxPlot
for race in ['Hispanic', 'White', 'Black', 'Native', 'Asian', 'Pacific']:
    plt.boxplot(census[race])
    plt.title("Box Plot of the Percentage of {} People per State".format(race))
    plt.ylabel("Percentage")
    plt.show()

#Make Bar Plots
for race in ['Hispanic', 'White', 'Black', 'Native', 'Asian', 'Pacific']:
    plt.bar(census['State'], census[race])
    plt.title("Percentage of {} People per State".format(race))
    plt.xlabel("State")
    plt.ylabel("Percentage")
    plt.xticks(rotation=90)  # Rotate state labels for readability
    plt.show()

#Stacked Bar plots

census_race = census[['Hispanic', 'White', 'Black', 'Native', 'Asian', 'Pacific']]
census_race.plot(kind='bar', stacked=True, figsize=(10, 6))
plt.title("Population Composition by Race per State")
plt.xlabel("State")
plt.ylabel("Percentage")
plt.legend(title="Race", loc='upper right', labels=census_race.columns)
plt.xticks(range(len(census)), census['State'], rotation=90)
plt.show()

#HeatMap
import seaborn as sns

census_race_corr = census[['Hispanic', 'White', 'Black', 'Native', 'Asian', 'Pacific']].corr()
sns.heatmap(census_race_corr, annot=True, cmap='coolwarm')
plt.title("Correlation Heatmap of Racial Composition by State")
plt.show()

# Violin Plot Visualization for Racial Composition
def visualize_violinplot(df):
    race_columns = ['Hispanic', 'White', 'Black', 'Native', 'Asian', 'Pacific']
    plt.figure(figsize=(12, 6))
    sns.violinplot(data=df[race_columns])
    plt.title("Violin Plot of Racial Composition by State")
    plt.ylabel("Percentage")
    plt.show()

visualize_violinplot(us_census)

