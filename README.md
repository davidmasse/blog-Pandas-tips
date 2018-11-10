## Helpful Pandas Patterns

Some basic Python imports:

```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```


### Duplicates

If your sense is that your data probably has no duplicates but just want to make sure, here's a quick way. Returns False if duplicate rows (same in specified columns, or all columns if none specified) are found, and True if no duplicates.

```
df.equals(pd.DataFrame.drop_duplicates(df, subset = ['A', 'B', 'C']))
```


### Apply and Assign

Here's the best way I've found to add derived columns to a dataframe. It's nice to see the lambda function separately. Adding if-else logic is often useful:

```
# Here 'A' and 'B' are columns in df:
fn = lambda row: row['A'] / row['B'] if row['B'] != 0 else 0
df_expanded = df.assign(derived_column = df.apply(fn, axis = 1).values
```

### Side-by-Side Bar Chart (Seaborn)

Here's a quick side-by-side bar chart to show more than one vertical value (all on the same scale) for each category on the horizontal axis - in case you would like to see various versions of a metric for each group or point in time.

```
# nonsense example data:
d = {'Month': ['March', 'March', 'April', 'April', 'May', 'May'],
                       'Measurement': [4, 3, 3, 4, 5, 6],
                       'Type of Measurement': ['A', 'B', 'A', 'B', 'A', 'B']}
to_chart = pd.DataFrame(d)
# the line below is convenient to adjust depending on scale and look of plot
sns.set(font_scale = 1.5)
fig, ax = plt.subplots(figsize = (10, 7))
# hue is the argument to know, indicates the two versions of each month's data to show
sns.barplot(x = 'Month', y = 'Measurement', hue = 'Type of Measurement', data = to_chart)
plt.show()
```

![sbs_bar](sbs_bar.png)

### Iterating through `.groupby` Objects

### Binary Target Histograms

If you have a binary target variable, it's often illustrative to plot a histogram of each feature showing two populations: the feature's values where the target is positive, and its values where the target is negative.  Other conditions may be placed on membership in either group to exclude outliers if desired.

```
yes = df[(df['A'] == 1]) & (df['B'] < 100)]
no = df[(df['A'] == 0]) & (df['B'] < 100)]
# The below assumes that the target is in the final column, but if the dataframe only contains the features, omit the -1:
for name in df.columns[:-1]:
    a = yes[name].values
    b = no[name].values
    sns.distplot(a, label = 'positive', kde = False, rug = False)
    sns.distplot(b, label = 'negative', kde = False, rug = False)
    plt.legend()
    plt.show()
```
