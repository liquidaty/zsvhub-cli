# Demo: COVID-19 data

## Overview
We demonstrate how to use the ZSV Playground (https://zsvhub.com/playground/) to analyze publicly available COVID data.
 
## Topics covered
 
* quick description / summary of a CSV** table of data
* joining data
* auto-generating a stratification report

** The input data does not need to be CSV; the steps demonstrated herein could
equally apply to tabular data in XLSX, TSV, JSON or other input formats that
can be converted to CSV

## The data
Data used in this demo includes:
* govex/COVID-19 data (https://github.com/govex/COVID-19)
* Country/continent data (https://pkgstore.datahub.io/JohnSnowLabs/country-and-continent-codes-list/)

## Getting the "vaccine" data set
```
curl -LO https://raw.githubusercontent.com/liquidaty/zsvhub-cli/main/demos/covid_vaccine/vaccine_data_global.csv
```

### Quick summary

There are several ways to quickly assess the shape of your data and explore it in more detail. A couple of easy ways to start are to generate:
- a basic overview of table columns
- for each column in your data, a breakdown of its distinct or of its ranges of values

#### Basic overview of columns and basic data shape
Use the `desc+` command to quickly see the "shape" of our data:
```
zsv desc+ vaccine_data_global.csv
```

which will display something like the below (if using the CLI, you can pipe into `pretty` and adjust the width of the `zsv pretty` output with the `--width` option
e.g. `zsv pretty --width 150` to adjust the width to 150 characters):

|#|Column name|Min Length|Max Length|Example 1|Example 2|Example 3|Example 4|Example 5|
|--|--|--|--|--|--|--|--|--|
|1|Province_State|3|44|Autonomous City of Buenos Aires|Buenos Aires|Catamarca|Chaco|Chubut|
|2|Country_Region|2|32|Afghanistan|Albania|Algeria|Andorra|Angola|
|3|Date|10|10|2021-08-14|2021-08-14|2021-08-14|2021-08-14|2021-08-14|
|4|Doses_admin|1|10|1809517|1280239|4146091|86320|1695588|
|5|People_partially_vaccinated|5|10|770302|720232|3421279|48445|972978|
|6|People_fully_vaccinated|1|10|219159|560007|724812|33904|722610|
|7|Report_Date_String|10|10|2021/08/14|2021/08/14|2021/08/14|2021/08/14|2021/08/14|
|8|UID|1|6|4|8|12|20|24|

(you can also use `pretty` to output in markdown format by adding a `--markdown` option as in `zsv pretty --markdown`)


#### More detailed overview of columns and basic data shape

We can get additional information using the `--all` option:
```
zsv desc+ --all vaccine_data_global.csv | zsv pretty
```

which will display:
|#|Column name|Min Length|Max Length|Unique|Unique (case-insensitive)|Blank|Date|Bool|Int|Float|Stdev|Example 1|Example 2|Example 3|Example 4|Example 5|
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|1|Province_State|3|44|FALSE|FALSE|29.68|0|0|0|0||Autonomous City of Buenos Aires|Buenos Aires|Catamarca|Chaco|Chubut|
|2|Country_Region|2|32|FALSE|FALSE|0|0|0|0|0||Afghanistan|Albania|Algeria|Andorra|Angola|
|3|Date|10|10|FALSE|FALSE|0|100|0|0|0||2021-08-14|2021-08-14|2021-08-14|2021-08-14|2021-08-14|
|4|Doses_admin|1|10|FALSE|FALSE|0|0.36|1.44|100|100|214203231.42|1809517|1280239|4146091|86320|1695588|
|5|People_partially_vaccinated|5|10|FALSE|FALSE|71.04|0.62|0|100|100|197019743.78|770302|720232|3421279|48445|972978|
|6|People_fully_vaccinated|1|10|FALSE|FALSE|71.04|0|1.24|100|100|155324753.36|219159|560007|724812|33904|722610|
|7|Report_Date_String|10|10|FALSE|FALSE|0|100|0|0|0||2021/08/14|2021/08/14|2021/08/14|2021/08/14|2021/08/14|
|8|UID|1|6|FALSE|FALSE|0.18|0|0|100|100|102983.14|4|8|12|20|24|

For even more detail, use `--all` together with `--json`

#### JSON output
The `--json` option specifies JSON output. The maximum level of data shape detail
is output when `--json` is used in conjunction with the `--all` option

```
zsv desc+ --json --all vaccine_data_global.csv
```

#### Sampling
`zsv` is designed to be reasonably fast, but sampling is often useful when
working with large files. `zsv` offers several methods for sampling:
- every nth row: `zsv select --sample-every 10` will return rows 1, 11, 21, ...
- random selection based on a specified chance: `zsv select --sample-pct 10 vaccine_data_global.csv` will give each row a 10% chance of being selected
- formulaic condition evaluated on a per-row basis: `zsv transform -w 'env("rand")>0.15 and *.[Province_State] != null' vaccine_data_global.csv` will give each row a 15% chance of being selected, but only if the "population" column in that row is greater than zero
- with a sql query: `zsv sql "select * from data where abs(random() % 100) < 20" vaccine_data_global.csv` will give each row a 20% change of being selected

#### Stratification of each data column

To create a simple stratification of each column of data, run:
```
zsv desc+ --strat vaccine_data_global.csv
```
(alternatively, you can produce this as a formatted XLSX file named 'simple_strat.xlsx' by running `zsv desc+ --strat simple_strat.xlsx vaccine_data_global.csv`)

This creates a report with one table for each column in your data and with statistical information including count and various sums:

![image](https://user-images.githubusercontent.com/26302468/129617496-7c6b9e16-fa42-4f7d-bdcc-390920f6e21d.png)


##### Expanding a table

You'll notice that the first two tables only show very broad "buckets" for our columms "Province_State" and "Country_Region". The reason for this is that,
when `zsv` determines how to "bucket" a table filled with non-numeric and non-date text values, it only creates a table based on distinct values if the
number of distinct values in the table does not exceed a threshold, which defaults to 100.

To force those tables to show distinct values, we can use --distinct:
```
zsv desc+ --strat --distinct Province_State --distinct Country_Region vaccine_data_global.csv | zsv pretty
```

which makes our first two tables look like:
![image](https://user-images.githubusercontent.com/26302468/129617781-6803c00b-8077-45a6-85c6-3f581a5be285.png)

which, as you can see, display the top ten distinct values (by count). If we wanted to, we could can expand that limit by setting the `--strat-distinct-rows` option to a higher value, e.g.:
```
zsv desc+ --strat --distinct Province_State --distinct Country_Region --strat-distinct-rows 100 vaccine_data_global.csv | zsv pretty
```

##### Joining other data

Let's say we want to add a column to this strat, which will show the % of people fully vaccinated and % of people partially vaccinated, and we want to be able to see a table by continent.

First, we need to augment our data with, for each country, the total population and the continent to which it belogs.

We can get the total population from the dataset's "world_pop_by_country.csv" file:
```
curl -LO https://raw.githubusercontent.com/liquidaty/zsvhub-cli/main/demos/covid_vaccine/world_pop_by_country.csv
```

and the related content from "country-and-continent-codes-list.csv":
```
curl -LO https://raw.githubusercontent.com/liquidaty/zsvhub-cli/main/demos/covid_vaccine/country-and-continent-codes-list.csv
```

Next, we need to incorporate this into our data.

A quick look using `zsv desc+ world_pop_by_country.csv` and `zsv desc+ vaccine_data_global.csv` suggests that
we can do so by joining the tables on Country Name and Country_Region, respectively, and extracting
the '2018' column. Similarly, we can get the continent data by joining on Country_Number and UID.

There are several ways to do this with `zsv`-- using an sql command, a lookup, or an sql-base lookup. 
This is a simple join and the joined table is small, which makes it a good fit for a simple `sql` join
([to do: other join methods are discussed in further detail in later sections of this tutorial]).

The syntax for this command is: `zsv select source1.csv source2.csv <sql>`, where the sql statement
references the data sources as tables named "data" and "data2" (`zsv sql` also can be run with more than 2
sources, or with a single source).

In addition, since we are now bringing in country-level data, we will want to make sure we are only working with country-level data.

Viewing with `zsv pretty vaccine_data_global.csv`, we can see we only want data with a blank Province_State--
otherwise, the People_xxx columns seem to all be blank:

|Province_State|Country_Region|Date|Doses_admin|People_partially_vaccinated|People_fully_vaccinated|Report_Date_String|UID|
|--|--|--|--|--|--|--|--|
||Afghanistan|2021-08-16|1809517|770302|219159|2021/08/16|4|
||Albania|2021-08-16|1309995|742540|567455|2021/08/16|8|
||...|...|...|...|...|...|...|
||Argentina|2021-08-16|36851592|26609472|10032222|2021/08/16|32|
|Autonomous City of Buenos Aires|Argentina|2021-08-16|3159173|||2021/08/16|3201|
|Buenos Aires|Argentina|2021-08-16|13829963|||2021/08/16|3202|
|Catamarca|Argentina|2021-08-16|331182|||2021/08/16|3203|
|Chaco|Argentina|2021-08-16|816996|||2021/08/16|3204|

So, we will add a condition to our sql query. Finally, we seem to have some null Country_Number values in our country/continent list,
so we will also make sure not to include join results based on empty strings. 

Putting all this together, and saving the result in vaccine_data_global_with_population.csv:

```
zsv sql vaccine_data_global.csv world_pop_by_country.csv country-and-continent-codes-list.csv "select data.*, data2.[2018] as population, data3.Continent_Name from data left join data2 on data.Country_Region = data2.[Country Name] left join data3 on data.UID = data3.Country_Number where coalesce(data3.Country_Number,'') != '' and coalesce(data.Province_State,'') = ''" -o vaccine_data_global_with_population.csv
```

Let's take a look at our strat now:
```
zsv desc+ --strat vaccine_data_global_with_population.csv
```

Looking better-- our last table, in particular, is looking close to what we want:
|Continent_Name|Count|Sum of Doses_admin|Sum of People_partially_vaccinated|Sum of People_fully_vaccinated|Sum of UID|Sum of population|
|--|--|--|--|--|--|--|
|Europe|49|799,330,913|437,687,908|363,438,306|20,274|862,816,059|
|Asia|44|3,110,839,713|1,469,835,062|1,145,731,544|17,552|4,510,095,324|
|Africa|34|77,178,526|51,749,371|27,252,784|16,957|936,940,597|
|North America|22|869,145,406|496,761,906|398,990,393|8,608|555,508,032|
|South America|12|300,333,347|206,943,884|101,120,897|4,708|423,398,995|
|Oceania|8|18,378,248|11,713,503|6,174,937|3,272|40,096,846|
|Total|169|5,175,206,153|2,674,691,634|2,042,708,861|71,371|7,328,855,853|

##### Adding a column weight

At this point, we don't yet reached our goal of showing the % of people fully and partly vaccinated by continent,
but we're getting much closer. We could just save our report definition and tweak the rest by hand, but
we'll do one more thing first: designate the "weight" column.

The "weight" column will be the column that we *weight* our data by. For example, if we wanted to analyze our credit
card spend by category, then we would analyze a list of transactions and weight them by dollars.

In this case, we want to weight our data by population, and `zsv` can help us do that with the `--weight` option. When
this option is used for statistics, `zsv` will calculate correlations for each column vs the weight column, and
mwhen used for stratification reporting, `zsv` will generate weighted average column statistics with the given weight.

Let's try it. If we're printing to console, we'll need to extend the `zsv pretty` default width as well with `--width`:
```
zsv desc+ --strat --weight population vaccine_data_global_with_population.csv
# to save as xlsx:
zsv desc+ --strat mystrat.xlsx --weight population vaccine_data_global_with_population.csv
```
Now, our last table looks like:

|Continent_Name|Count|Sum of population|population (%)|Weighted Avg Doses_admin / population|Sum of Doses_admin|Weighted Avg People_partially_vaccinated / popula…|Sum of People_partially_vaccinated|Weighted Avg People_fully_vaccinated / population|Sum of People_fully_vaccinated|Weighted Avg UID|
|--|--|--|--|--|--|--|--|--|--|--|
|Asia|44|4,510,095,324|61.538873|68.832479|3,110,839,713|32.892768|1,469,835,062|25.636254|1,145,731,544|336|
|Africa|34|936,940,597|12.784268|8.232642|77,178,526|5.519893|51,749,371|2.907386|27,252,784|500|
|Europe|49|862,816,059|11.772862|92.6421|799,330,913|50.727835|437,687,908|42.122339|363,438,306|529|
|North America|22|555,508,032|7.579737|92.584818|869,145,406|53.822809|496,761,906|41.553294|398,990,393|653|
|South America|12|423,398,995|5.77715|70.933883|300,333,347|48.876801|206,943,884|23.883122|101,120,897|201|
|Oceania|8|40,096,846|0.547109|45.68789|18,378,248|29.268738|11,713,503|15.429425|6,174,937|226|
|Total|169|7,328,855,853|100|65.683447|5,175,206,153|33.990866|2,674,691,634|25.721472|2,042,708,861|395|

That looks better! Now, we just need to get rid of the other stuff we don't want.

##### Editing a stratification report manually

Stratification report definitions can be saved in JSON format and edited with any text editor. The `zsv sum` command
can then be used to generate a report using the saved definition, as applied to your data.

Let's save the last iteration of our report definition to "generated-strat-definition.json":
```
zsv desc+ --weight population --strat-definition generated-strat-definition.json vaccine_data_global_with_population.csv
```

The schema for this definition can be found at
https://github.com/liquidaty/zsvhub-cli/main/schemas/stratification-definition.json

The properties we care about right now are `columns` and `tables`, which define the report's columns and tables, respectively.

Let's remove all table entries other than Country_Region and Continent_Name,
remove all columns other than the first three,
the weighted average percentages and the fully vaccinated sum,
and tidy up the column titles a bit.

Lastly, let's move the Country_Region to be the second table, add the same `sort` to it as our Continent table,
and change it to show distinct values (by changing its value formula to simply `*.[Country_Region]`):
```
  "tables": [
    {
      "name": "Continent_Name",
      ...
    },
    {
      "name": "Country_Region",
      "data_type": "S",
      "value": "*.[Country_Region]",
      "sort": [
        {
          "by": "[Sum of population]",
          "desc": 1,
          "nulldesc": 1
        }
      ]
    }
  ],
```

(we also could have achived the above step by using `--distinct` and `--strat-distinct-rows`
when the report definition was generated)

Finally, let's save this report definition as "final-strat-definition.json"
(if you don't want to edit the json yourself, you can get it from
https://raw.githubusercontent.com/liquidaty/zsvhub-cli/main/demos/covid_vaccine/final-strat-definition.json)
and create the output file 'final_strat.xlsx' by running:
```
zsv sum -m final-strat-definition.json vaccine_data_global_with_population.csv -o final_strat.xlsx
```

Now it's shaping up!

![image](https://user-images.githubusercontent.com/26302468/129627193-51d23dac-25c9-4faf-85b2-358006da95ac.png)

If we were doing this for "real", we wouldn't be done just yet-- for example,
you can see that there are a few countries that appear more than once in our dataset as indicated by having a Count greater than 1.

<!--- 
Hop over to https://github.com/liquidaty/zsvhub-cli/main/demos/validation
to see how to use `zsv` to handle that and other common validation tasks
--->

