# Variables

## How to modify a global variable from within a function in Python

Use the keyword <b>global</b> variable_name

```Python
def rl():
    global output_file # tells pythong to use the global variable output_file instead of creating a local variable
    output_file = generate_file_name("output")
    print(output_file)
    %run "./openpyxl-utils.ipynb"    
    return
```    
    
# Data acquisition

```Python
xls = pd.ExcelFile(input_file)
df = pd.read_excel(xls, 'All staff (daily refresh)', skiprows=7)
```

# EDA (exploratory data analysis)

# Sorting

## Sort with multiple keys

```Python
df.sort_values(['Area_Sort','Sub_Area','Name'],ascending=True,inplace=True)
```

# Pivot (groupby)

## Adding a total row

```Python
df.loc['Total']= df.sum(numeric_only=True, axis=0)
```

# Combining datasets

# Concat

```Python
dfn_pre=dfn.add_prefix("dfn_")
dfp_pre=dfp.add_prefix("dfp_")
dfn_pre.set_index(['dfn_id'],inplace=True) # setting the employee ID as index
dfp_pre.set_index(['dfp_id'],inplace=True) # setting the employee ID as index
df = pd.concat([dfp_pre,dfn_pre],axis=1) # combines the 2 DataFrames into 1 by combining columns from both using the index as key.
```

# Transformation and feature engineering

## List comprehension to rename columns with regular expression

Replace the hashtags by 'N' to make it easier to use the column names

```Python
df.columns=[re.sub('#', "N", col) for col in df.columns]
```

## Lambda functions to extract values from columns

### Split to retrieve the first element

note: split gives the flexibility to have an element of variable length such as "AD" or "MDSA"

```Python
dfn["ProposedRank"] = dfn.PromotionRequest.str.split().map(lambda x: x[0])
```

### Store a percentage value in a dataframe and prevent division by zero nan% errors

```Python
import math

df_rate['Perc_HC']=(df_rate.Nominated_/df_rate.dfp_Name).map(lambda num: '0.0%' if math.isnan(num) else '{0:.1f}%'.format(round(num * 100, 1)))
df_rate['Perc_Elig']=(df_rate.Nominated_/df_rate.dfp_Eligible).map(lambda num: '0.0%' if math.isnan(num) else '{0:.1f}%'.format(round(num * 100, 1)))
```



## Modifying values of filtered rows using .loc

Python requires the use of .loc to identify rows of a DataFrame which are modified.

NOTE: the use of <b>.loc[mask,"column_name"]</b>

```Python
# creates the new column containing a datetime delta
dfp['DaysSinceLastPromotion'] = (datetime.datetime.today() - dfp.LastPromotionDate) 
# converts the delta to a number of days
dfp.loc[~dfp.LastPromotionDate.isnull(),'DaysSinceLastPromotion'] = dfp.loc[~dfp.LastPromotionDate.isnull(),'DaysSinceLastPromotion'].dt.days 
# convers to a number of years (with decimals)
dfp['YearsSinceLastPromotion']= dfp.DaysSinceLastPromotion/365.0 
```

## Transforming boolean to int (1 for True and 0 for False)

```Python
dfp['Eligible']=dfp.PromotionEligibilityWithSec=='Meets all criteria'  # boolean value
dfp['Eligible_']=dfp.Eligible.astype(int) # int value
```
