
# Working with missing data - Total number of U.S. teachers by state
---

This dataset comes from the [National Center for Education Statistics](https://nces.ed.gov/surveys/sass/tables/sass1112_2013314_t1s_001.asp), and it includes the total number of teachers by state.  [Download the dataset here](state_teachers_2011-12).  This data was collected during the 2011-2012 School and Staffing Survey.

We can see that some states are missing data:

```R
# Import the libraries
suppressMessages(library(dplyr))
# Import the data
df_states = read.csv('state_teachers_2011-12.csv', header=TRUE)
cat('Total Rows:', nrow(df_states))
head(df_states, 3)
# Show the rows missing data
cat('States with missing data:')
df_states[is.na(df_states$TEACHERS),]
```
<div class="output">
<pre>
Total Rows: 50
</pre>
</div>

<table>
<thead><tr><th>STATE</th><th>TEACHERS</th></tr></thead>
<tbody>
	<tr><td>Alabama</td><td>45000  </td></tr>
	<tr><td>Alaska </td><td> 7500  </td></tr>
	<tr><td>Arizona</td><td>61700  </td></tr>
</tbody>
</table>

<div class="output">
<pre>
States with missing data:
</pre>
</div>

<table>
<thead><tr><th></th><th>STATE</th><th>TEACHERS</th></tr></thead>
<tbody>
	<tr><th>9</th><td>Florida     </td><td>NA          </td></tr>
	<tr><th>11</th><td>Hawaii      </td><td>NA          </td></tr>
	<tr><th>20</th><td>Maryland    </td><td>NA          </td></tr>
	<tr><th>39</th><td>Rhode Island</td><td>NA          </td></tr>
</tbody>
</table>

Four states are missing data.  What can we do?

1. Find the missing data from other data sources
2. Drop the data points with missing data
3. [Impute](https://en.wikipedia.org/wiki/Imputation_%28statistics%29) the missing data

The best option would be to find the missing data from other sources, but we must be sure to use reliable sources for the data.  Luckily enough, I found reliable sources for two of the missing states.

Rhode Island has up to date teacher counts on their [InfoWorks!](http://infoworks.ride.ri.gov/state/ri) website.  We can see that there are 15,988 registered teachers working in Rhode Island.  This number is likely inflated slightly relative to the 2011-2012 school year, so let's use an even 15,900 teachers for Rhode Island.

```R
# Set the value for Rhode Island
df_states$TEACHERS = ifelse(df_states$STATE == 'Rhode Island', 15900,
                            df_states$TEACHERS)
head(df_states, 3)
df_states[df_states$STATE == 'Rhode Island',]
```

<table>
<thead><tr><th>STATE</th><th>TEACHERS</th></tr></thead>
<tbody>
	<tr><td>Alabama</td><td>45000  </td></tr>
	<tr><td>Alaska </td><td> 7500  </td></tr>
	<tr><td>Arizona</td><td>61700  </td></tr>
</tbody>
</table>

<table>
<thead><tr><th></th><th>STATE</th><th>TEACHERS</th></tr></thead>
<tbody>
	<tr><th>39</th><td>Rhode Island</td><td>15900       </td></tr>
</tbody>
</table>

While I could not find the total teacher count for Florida on their government website, but I found the average class size across school years on the [Florida Dept. of Education](http://fldoe.org/finance/budget/class-size/class-size-reduction-averages.stml) website.  For the year 2011-2012, the average class sizes were:

Grades|Class Size
-|-
PK-3|15.61
4-8|18.16
9-12|20.08

Let's compute the average of the three to determine the state average.

```R
# Calculate the average class size across all grades
fl_str = (15.61 + 18.16 + 20.08) / 3
cat('Florida average class size:', fl_str)
```
<div class="output">
<pre>
Florida average class size: 17.95
</pre>
</div>

From Kaggle's [U.S. Education Datasets: Unification Project](https://www.kaggle.com/noriuk/us-education-datasets-unification-project), we can get the total number of students enrolled in Florida in 2011-2012.  That number is 2,658,559.  Using this number and the average class size, we can estimate the total number of teachers.

```R
# Calculate the total number of teachers in Florida in 2011-2012
fl_teachers = round(2658559 / fl_str, -3)
cat('Florida teachers in 2011-2012:', fl_teachers)
```

<div class="output">
<pre>
Florida teachers in 2011-2012: 148000
</pre>
</div>

There were approximately 148,000 teachers in Florida during the 2011-2012 school year.  Let's add them to our data table.

```R
# Set the value for Florida
df_states$TEACHERS = ifelse(df_states$STATE == 'Florida', fl_teachers, df_states$TEACHERS)
# Show Florida state
df_states[df_states$STATE == 'Florida',]
```
<table>
<thead><tr><th></th><th>STATE</th><th>TEACHERS</th></tr></thead>
<tbody>
	<tr><th>9</th><td>Florida</td><td>148000 </td></tr>
</tbody>
</table>

I was unable to find teacher counts from reliable government sources for either Hawaii or Maryland.  That leaves us with two choices.  We can drop the data or impute it.  In statistics you use the existing data to estimate the values of the missing data. Let's try to impute the data using regression, but first we will need to combine this data set with another that contains additional information.

## Imputation

In one of my other exploratory data analyses, I looked at the relationship between average student national exam scores and average spending per student.  You can check it out [here](/eda/us_state_education).

From that analysis, I have a [cleaned dataset](state_2015_with_enroll.csv) and we can import it here.

```R
df_enex = read.csv('state_2015_with_enroll.csv', header=TRUE)
head(df_enex, 3)
```

<table>
<thead><tr><th>STATE</th><th>ENROLL</th><th>AVG_SCORE</th><th>AVG_TOT_EXP</th></tr></thead>
<tbody>
	<tr><td>ALABAMA  </td><td>734974   </td><td>245.9005 </td><td>10.206890</td></tr>
	<tr><td>ALASKA   </td><td>130755   </td><td>247.0921 </td><td>22.701549</td></tr>
	<tr><td>ARIZONA  </td><td>944978   </td><td>249.0399 </td><td> 8.362734</td></tr>
</tbody>
</table>

Let's merge it with our existing dataset, but the first thing to notice is that the state names do not match.

```R
# Show the df_states names
as.character(df_states$STATE[grepl(' ', df_states$STATE)])
# Show the enroll and expenditure names
as.character(df_enex$STATE[grepl('_', df_enex$STATE)])
```

<div class="output">
<pre>
'New Hampshire' 'New Jersey' 'New Mexico' 'New York' 'North Carolina'
'North Dakota' 'Rhode Island' 'South Carolina' 'South Dakota'
'West Virginia'

'DISTRICT_OF_COLUMBIA' 'NEW_HAMPSHIRE' 'NEW_JERSEY' 'NEW_MEXICO' 'NEW_YORK'
'NORTH_CAROLINA' 'NORTH_DAKOTA' 'RHODE_ISLAND' 'SOUTH_CAROLINA'
'SOUTH_DAKOTA' 'WEST_VIRGINIA'
</pre>
</div>

One dataset uses both upper and lower case, and the other uses only upper case.  Also, one dataset uses spaces to separate words and the other uses underscores.  We need both datasets to match.  So let's change the first dataset to use upper case letters and the second data set to use spaces between the words.

You will also notice that District of Columbia is in the second dataset, but that will be dropped as soon as we inner merge the two datasets.  So we don't need to worry about it.

```R
# Convert the state names to capital letters and strings for merging
df_states$STATE = as.character(toupper(df_states$STATE))
df_enex$STATE = as.character(df_enex$STATE)
# Convert underscores to spaces
df_enex$STATE = sub('_', ' ', df_enex$STATE)

# Show the df_states names
df_states$STATE[grepl(' ', df_states$STATE)]
# Show the enroll and expenditure names
df_enex$STATE[grepl(' ', df_enex$STATE)]
```

<div class="output">
<pre>
'NEW HAMPSHIRE' 'NEW JERSEY' 'NEW MEXICO' 'NEW YORK' 'NORTH CAROLINA'
'NORTH DAKOTA' 'RHODE ISLAND' 'SOUTH CAROLINA' 'SOUTH DAKOTA'
'WEST VIRGINIA'

'DISTRICT OF_COLUMBIA' 'NEW HAMPSHIRE' 'NEW JERSEY' 'NEW MEXICO' 'NEW YORK'
'NORTH CAROLINA' 'NORTH DAKOTA' 'RHODE ISLAND' 'SOUTH CAROLINA'
'SOUTH DAKOTA' 'WEST VIRGINIA'
</pre>
</div>

With the STATES columns matching, we are ready to inner merge them into a single dataset.

```R
# Merge both the state teachers and state score, expenditure datasets
df_edu = left_join(df_states, df_enex, by='STATE')
# Show the merged dataset
cat('Total Rows:', nrow(df_edu))
head(df_edu, 3)
# Show the rows missing data
cat('States with missing data:')
df_edu[is.na(df_edu$TEACHERS),]
```

<div class="output">
<pre>
Total Rows: 50
</pre>
</div>

<table>
<thead><tr><th>STATE</th><th>TEACHERS</th><th>ENROLL</th><th>AVG_SCORE</th><th>AVG_TOT_EXP</th></tr></thead>
<tbody>
	<tr><td>ALABAMA  </td><td>45000    </td><td>734974   </td><td>245.9005 </td><td>10.206890</td></tr>
	<tr><td>ALASKA   </td><td> 7500    </td><td>130755   </td><td>247.0921 </td><td>22.701549</td></tr>
	<tr><td>ARIZONA  </td><td>61700    </td><td>944978   </td><td>249.0399 </td><td> 8.362734</td></tr>
</tbody>
</table>

<div class="output">
<pre>
States with missing data:
</pre>
</div>

<table>
<thead><tr><th></th><th>STATE</th><th>TEACHERS</th><th>ENROLL</th><th>AVG_SCORE</th><th>AVG_TOT_EXP</th></tr></thead>
<tbody>
	<tr><th>11</th><td>HAWAII  </td><td>NA      </td><td>182384  </td><td>248.9358</td><td>13.82251</td></tr>
	<tr><th>20</th><td>MARYLAND</td><td>NA      </td><td>874108  </td><td>250.2656</td><td>15.88227</td></tr>
</tbody>
</table>

Now we are ready to impute the missing teacher values for the dataset.  We have at least three options to impute the missing values:

1. Using the mean of the TEACHERS variable to impute the values.
2. Using regression to impute the values.
3. Using regression with perturbation to impute the values.

We will use the third option, as it tends to produce better results overall.

### Imputing via regression with perturbation

To impute via regression, we must first create a regression model to estimate the teachers from the other data points.

```R
# Create the regression model
mod_impreg = lm(TEACHERS ~ ENROLL+AVG_SCORE+AVG_TOT_EXP, df_edu)
summary(mod_impreg)
```
<div class="output">
<pre>
Call:
lm(formula = TEACHERS ~ ENROLL + AVG_SCORE + AVG_TOT_EXP, data = df_edu)

Residuals:
   Min     1Q Median     3Q    Max
-83873  -8247  -1064   8153  58338

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept) -2.625e+05  1.967e+05  -1.334   0.1890    
ENROLL       5.929e-02  2.664e-03  22.255   <2e-16 ***
AVG_SCORE    9.939e+02  7.907e+02   1.257   0.2154    
AVG_TOT_EXP  1.554e+03  7.760e+02   2.003   0.0514 .  
---
Signif. codes:  0 ‘ *** ’ 0.001 ‘ ** ’ 0.01 ‘ * ’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 20890 on 44 degrees of freedom
  (2 observations deleted due to missingness)
Multiple R-squared:  0.9199,	Adjusted R-squared:  0.9145
F-statistic: 168.5 on 3 and 44 DF,  p-value: < 2.2e-16
</pre>
</div>

We can see that ENROLL is the best predictor of the number of teachers, which makes complete sense given that more students require more teachers.  All predictors have positive coefficients indicating that an increase in any predictor will increase the estimated TEACHERS value.

We will use the model to compute the predicted values and append them to the data frame.

```R
# Compute the predicted values from the regression model
df_edu$TEACH_PRED = round(predict(mod_impreg, df_edu), -2)
# Show the rows missing data
cat('States with missing data:')
df_edu[is.na(df_edu$TEACHERS),]
```
<div class="output">
<pre>
States with missing data:
</pre>
</div>

<table>
<thead><tr><th></th><th>STATE</th><th>TEACHERS</th><th>ENROLL</th><th>AVG_SCORE</th><th>AVG_TOT_EXP</th><th>TEACH_PRED</th></tr></thead>
<tbody>
	<tr><th>11</th><td>HAWAII  </td><td>NA      </td><td>182384  </td><td>248.9358</td><td>13.82251</td><td>17200   </td></tr>
	<tr><th>20</th><td>MARYLAND</td><td>NA      </td><td>874108  </td><td>250.2656</td><td>15.88227</td><td>62800   </td></tr>
</tbody>
</table>

Finally we will perturb the predictions following a normal distribution.  A standard deviation of 1000 teachers will give us an appropriately sized perturbation.

```R
# Add normally destributed perturbations
set.seed(1234)
df_edu$TEACH_PTB <- round(df_edu$TEACH_PRED + rnorm(nrow(df_edu), mean=0,
                                              sd=1000), -2)
# Show the rows missing data
cat('States with missing data:')
df_edu[is.na(df_edu$TEACHERS),]
```

<div class="output">
<pre>
States with missing data:
</pre>
</div>

<table>
<thead><tr><th></th><th>STATE</th><th>TEACHERS</th><th>ENROLL</th><th>AVG_SCORE</th><th>AVG_TOT_EXP</th><th>TEACH_PRED</th><th>TEACH_PTB</th></tr></thead>
<tbody>
	<tr><th>11</th><td>HAWAII  </td><td>NA      </td><td>182384  </td><td>248.9358</td><td>13.82251</td><td>17200   </td><td>16700   </td></tr>
	<tr><th>20</th><td>MARYLAND</td><td>NA      </td><td>874108  </td><td>250.2656</td><td>15.88227</td><td>62800   </td><td>65200   </td></tr>
</tbody>
</table>

Let's substituted the imputed regression with perturbation values into the NA values of the TEACHERS variable.

```R
# Set the NaN values to the perturbed values
df_imputed = df_edu
df_imputed$TEACHERS = ifelse(is.na(df_imputed$TEACHERS), df_imputed$TEACH_PTB,
                            df_imputed$TEACHERS)
# Show the rows missing data
cat('States with imputed data:')
df_imputed[df_imputed$STATE == 'HAWAII' |  df_imputed$STATE == 'MARYLAND',]
```
<div class="output">
<pre>
States with imputed data:
</pre>
</div>

<table>
<thead><tr><th></th><th>STATE</th><th>TEACHERS</th><th>ENROLL</th><th>AVG_SCORE</th><th>AVG_TOT_EXP</th><th>TEACH_PRED</th><th>TEACH_PTB</th></tr></thead>
<tbody>
	<tr><th>11</th><td>HAWAII  </td><td>16700   </td><td>182384  </td><td>248.9358</td><td>13.82251</td><td>17200   </td><td>16700   </td></tr>
	<tr><th>20</th><td>MARYLAND</td><td>65200   </td><td>874108  </td><td>250.2656</td><td>15.88227</td><td>62800   </td><td>65200   </td></tr>
</tbody>
</table>

We can now drop the extra columns that we created to impute the data.

```R
# Drop the imputation columns
df_cleaned = df_imputed[ ,!(names(df_imputed) %in%
                             c('TEACH_PRED','TEACH_PTB'))]
# Show the first rows
head(df_cleaned, 3)
```

<table>
<thead><tr><th>STATE</th><th>TEACHERS</th><th>ENROLL</th><th>AVG_SCORE</th><th>AVG_TOT_EXP</th></tr></thead>
<tbody>
	<tr><td>ALABAMA  </td><td>45000    </td><td>734974   </td><td>245.9005 </td><td>10.206890</td></tr>
	<tr><td>ALASKA   </td><td> 7500    </td><td>130755   </td><td>247.0921 </td><td>22.701549</td></tr>
	<tr><td>ARIZONA  </td><td>61700    </td><td>944978   </td><td>249.0399 </td><td> 8.362734</td></tr>
</tbody>
</table>

Finally, we will create an interaction term for class size that will be used in other analyses.

```R
# Calculate the class size
df_cleaned$CLASS_SIZE = round(df_cleaned$ENROLL / df_cleaned$TEACHERS, 2)
# Show the first rows
head(df_cleaned, 3)
```

<table>
<thead><tr><th>STATE</th><th>TEACHERS</th><th>ENROLL</th><th>AVG_SCORE</th><th>AVG_TOT_EXP</th><th>CLASS_SIZE</th></tr></thead>
<tbody>
	<tr><td>ALABAMA  </td><td>45000    </td><td>734974   </td><td>245.9005 </td><td>10.206890</td><td>16.33    </td></tr>
	<tr><td>ALASKA   </td><td> 7500    </td><td>130755   </td><td>247.0921 </td><td>22.701549</td><td>17.43    </td></tr>
	<tr><td>ARIZONA  </td><td>61700    </td><td>944978   </td><td>249.0399 </td><td> 8.362734</td><td>15.32    </td></tr>
</tbody>
</table>

We have now successfully updated all of the missing TEACHERS values within the dataset.  [Download it here](states_imputed.csv).

Let's continue with creating and [evaluating a multiple linear regression model of the states to estimate the average student national examination scores.](/modeling/us_state_education_multi)
