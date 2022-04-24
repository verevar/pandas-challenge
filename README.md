# pandas-challenge
"""
Studying this dataset, there are two interesting observations. My first big observation was that the top 5 performing schools were all charters whereas the bottom 5 performing were all district schools.
My second observation is that the smaller the school size, the higher the grades. This is probably because of smlaler teacher-to-student ratios which would allow teachers to help students more throughly.
"""


Dependencies and Setup
import pandas as pd

 File to Load (Remember to Change These)
school_data_to_load = "Resources/schools_complete.csv"
student_data_to_load = "Resources/students_complete.csv"

 Read School and Student Data File and store into Pandas DataFrames
school_data = pd.read_csv(school_data_to_load)
student_data = pd.read_csv(student_data_to_load)

Combine the data into a single dataset.  
school_data_complete = pd.merge(student_data, school_data, how="left", on=["school_name", "school_name"])

"""
##DISTRICT SUMMARY

*Step1 - Calculate the total number of schools

*Step2 - Calculate the total number of students

*Step3 - Calculate the total budget

*Step4 - Calculate the average math score

*Step5 - Calculate the average reading score

*Step6 - Calculate the percentage of students with a passing math score (70 or greater)

*Step7 - Calculate the percentage of students with a passing reading score (70 or greater)

*Step8 - Calculate the percentage of students who passed math and reading (% Overall Passing)

*Step9 - Create a dataframe to hold the above results

"""

df = pd.read_csv(school_data_to_load)
df2 = pd.read_csv(student_data_to_load)
df3 = school_data_complete

df.rename(columns={'school_name': 'School', 'type': 'Type'})
df2.rename(columns={'school_name': 'School', 'type': 'Type'})
df3.rename(columns={'school_name': 'School', 'type': 'Type'})

###Step1 - Calculate the total number of schools
totnumsch = len(df)
totnumsch

###Step2 - Calculate the total number of students
totnumstu = len(df3)
totnumstu

###Step3 - Calculate the total budget
totalbudget = df["budget"].sum()
totalbudget

###Step4 - Calculate the average math score
avg_math = df3["math_score"].mean()
avg_math

#Step5 - Calculate the average reading score
avg_reading = df3["reading_score"].mean()
avg_reading

#Step6 - Calculate the percentage of students with a passing math score (70 or greater)
pm = df3["math_score"]>=70
passing_math = df3[pm]
pm1 = len(passing_math)*100/len(df3)
len(passing_math)*100/len(df3)

#Step7 - Calculate the percentage of students with a passing reading score (70 or greater)
pr = df3["reading_score"]>=70
passing_reading = df3[pr]
pr1 = len(passing_reading)*100/len(df3)
len(passing_reading)*100/len(df3)

#Step8 - Calculate the percentage of students who passed math and reading (% Overall Passing)
overall_pass = pm1*pr1/98
overall_pass

#Step9 - Create a dataframe to hold the above results
data = {'Total Schools':[15],
        'Total Students':[39170],
        'Total Budget':[24649428],
        'Average Math Score':[78.985371],
        'Average Reading Score':[81.877840],
        '% Passing Math':[74.980852],
        '% Passing Reading':[85.805463],
        '% Overall Passing':[65.171867]}
df4= pd.DataFrame(data)
# print dataframe.
df4

"""
SCHOOL SUMMARY
Create an overview table that summarizes key metrics about each school, including:
School Name
School Type
Total Students
Total School Budget
Per Student Budget
Average Math Score
Average Reading Score
% Passing Math
% Passing Reading
% Overall Passing (The percentage of students that passed math and reading.)
Create a dataframe to hold the above results
"""

##School Name, School Type, Students per School, Budget per School, Per Student Budget, % Passing Math, % Passing Reading, % Overall Passing
df3["Budget Per Student"] = df3["budget"]/df3["size"]
df3["% Passing Math"] = (df3["math_score"]>=70)*100
df3["% Passing Reading"] =(df3["reading_score"]>=70)*100
df3["% Overall Passing"] =(df3["reading_score"]>=70)*(df3["math_score"]>=70)*100
df3.groupby(['school_name','type']).mean().rename(columns={'school_name': 'School', 'reading_score': 'Average Reading Score', 'math_score': 'Average Math Score', 'size': 'Total Students', 'budget': 'Total Budget'})

""" 
TOP PERFORMING SCHOOLS BY % OVERALL PASSING
"""

df3.groupby(['school_name','type']).mean().rename(columns={'school_name': 'School', 'reading_score': 'Average Reading Score', 'math_score': 'Average Math Score', 'size': 'Total Students', 'budget': 'Total Budget'}).nlargest(n=5, columns=['% Overall Passing'])

""" 
BOTTOM PERFORMING SCHOOLS BY % OVERALL PASSING
"""

df3.groupby(['school_name','type']).mean().rename(columns={'school_name': 'School', 'reading_score': 'Average Reading Score', 'math_score': 'Average Math Score', 'size': 'Total Students', 'budget': 'Total Budget'}).nsmallest(n=5, columns=['% Overall Passing'])

""" 
MATH SCORES BY GRADE
Create a pandas series for each grade
Group each series by school
Combine the series into a dataframe
"""

ninth_math = df3.loc[df3['grade'] == '9th'].groupby('school_name')["math_score"].mean()
tenth_math = df3.loc[df3['grade'] == '10th'].groupby('school_name')["math_score"].mean()
eleventh_math = df3.loc[df3['grade'] == '11th'].groupby('school_name')["math_score"].mean()
twelveth_math = df3.loc[df3['grade'] == '12th'].groupby('school_name')["math_score"].mean()

math_by_grade = pd.DataFrame({
        "9th": ninth_math,
        "10th": tenth_math,
        "11th": eleventh_math,
        "12th": twelveth_math})
math_by_grade

""" 
READING SCORES BY GRADE
Create a pandas series for each grade
Group each series by school
Combine the series into a dataframe
"""

ninth_reading = df3.loc[df3['grade'] == '9th'].groupby('school_name')["reading_score"].mean()
tenth_reading = df3.loc[df3['grade'] == '10th'].groupby('school_name')["reading_score"].mean()
eleventh_reading = df3.loc[df3['grade'] == '11th'].groupby('school_name')["reading_score"].mean()
twelveth_reading = df3.loc[df3['grade'] == '12th'].groupby('school_name')["reading_score"].mean()

reading_by_grade = pd.DataFrame({
        "9th": ninth_reading,
        "10th": tenth_reading,
        "11th": eleventh_reading,
        "12th": twelveth_reading})
reading_by_grade

"""
SCORES BY SCHOOL SPENDING
"""

bins = [0, 585, 630, 645, 680]
bin_groups = ["< $585", "$585-630", "$630-645", "$645-680"]
df3['Spending(Per Student)'] = pd.cut(df3['budget']/df3['size'], bins, labels = bin_groups)
spending = df3.groupby('Spending(Per Student)')

avg_math = spending['math_score'].mean()
avg_reading = spending['reading_score'].mean()
passing_math = df3[df3['math_score'] >= 70].groupby('Spending(Per Student)')['Student ID'].count()/spending['Student ID'].count() * 100
passing_reading = df3[df3['reading_score'] >= 70].groupby('Spending(Per Student)')['Student ID'].count()/spending['Student ID'].count() * 100
overall_passing = df3[(df3['reading_score'] >= 70) & (df3['math_score'] >= 70)].groupby('Spending(Per Student)')['Student ID'].count()/spending['Student ID'].count() * 100
                     
school_spending = pd.DataFrame({
    "Average Math Score": avg_math,
    "Average Reading Score": avg_reading,
    "% Passing Math": passing_math,
    "% Passing Reading": passing_reading,
    "% Overall Passing ": overall_passing})
school_spending          

"""
SCORES BY SCHOOL SIZE
"""

bins = [0, 799, 2399, 23000]
bin_groups = ["Small (<1000)", "Medium (1000-2000)" , "Large (2000-5000)"]
df3['School Size'] = pd.cut(df3['size'], bins, labels = bin_groups)

size = df3.groupby('School Size')
 
avg_math = size['math_score'].mean()
avg_reading = size['math_score'].mean()
passing_math = df3[df3['math_score'] >= 70].groupby('School Size')['Student ID'].count()/size['Student ID'].count() * 100
passing_reading = df3[df3['reading_score'] >= 70].groupby('School Size')['Student ID'].count()/size['Student ID'].count() * 100
overall_passing = df3[(df3['reading_score'] >= 70) & (df3['math_score'] >= 70)].groupby('School Size')['Student ID'].count()/size['Student ID'].count() * 100
            
school_size = pd.DataFrame({
    "Average Math Score": avg_math,
    "Average Reading Score": avg_reading,
    "% Passing Math": passing_math,
    "% Passing Reading": passing_reading,
    "% Overall Passing": overall_passing})          
school_size

"""
SCORES BY SCHOOL TYPE
"""

type_of_school = df3.groupby("type")
                    
avg_math = type_of_school['math_score'].mean()
avg_reading = type_of_school['math_score'].mean()
passing_math = df3[df3['math_score'] >= 70].groupby("type")['Student ID'].count()/type_of_school['Student ID'].count() * 100
passing_reading = df3[df3['reading_score'] >= 70].groupby("type")['Student ID'].count()/type_of_school['Student ID'].count() * 100
overall_passing = df3[(df3['reading_score'] >= 70) & (df3['math_score'] >= 70)].groupby("type")['Student ID'].count()/type_of_school['Student ID'].count() * 100
           
school_type = pd.DataFrame({
    "Average Math Score": avg_math,
    "Average Reading Score": avg_reading,
    "% Passing Math": passing_math,
    "% Passing Reading": passing_reading,
    "% Overall Passing": overall_passing}) 
school_type

