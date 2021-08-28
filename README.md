# ETL with Covid-19 Infection and Vaccination Data

  Project to extract, transform, and load (ETL) Covid-19 infection and vaccination data. The project provides insight into the pandemic for the State of Michigan and each United States (US) county from January 2020 to the present. Data is extracted from two sources using Pandas read CSV and an API. Multiple techniques and functions were used to transform the data with Python Pandas into a more structured and organized format. It is exported into seven SQL database tables. All tables are linked using primary keys to easily navigate and compare data.

[Project Presentation](https://docs.google.com/presentation/d/1E2xyrMzGghwNk5w36eGZ_Ov4rRsHmBbnMWVS6YPyYD0/edit?usp=sharing)

## Extract

### Johns Hopkins University Center for Systems Science & Engineering (JHU CSSE)

- Covid-19 infection rates extracted from [Johns Hopkins University Center for Systems Science & Engineering (JHU CSSE) GitHub](https://github.com/CSSEGISandData/COVID-19/blob/master/csse_covid_19_data/csse_covid_19_time_series/time_series_covid19_confirmed_US.csv) in csv format with Pandas read csv.
  - time_series_covid19_confirmed_US.csv
  - Confirmed cases in the US only.
  - Updated daily around 23:59 (UTC).

### COVID ACT NOW : Realtime US Covid-19 Map & Vaccine Tracker

- [Covid Act NOW API](https://covidactnow.org/data-api)
  - Vaccination counts at state and county levels extracted though an API and converted into JSON format.
  - Confirmed cases in the US only.
  - Data is updated daily around noon (EST).

## Transform

### Pandas functions to transform dataset

1. pandas.melt function to unpivot data in a DataFrame
2. Unpivot: transpose columns into rows, changing from wide to long format
   1. When to use: when there are two data types in one column (column header data type differs from the column value data) or to better organize a DataFrame to save space
3. .melt() parameters:
   1. id_vars = Unpivoted by county name
   2. var_name = column header name for the columns to be unpivoted
   3. value_name = column header name for the values

### Functions to get the largest daily changes (in case counts and percent change) for Wayne County, Michigan

   1. .nlargest() find county with largest case counts overall
   2. .loc() on that county
   3. .diff() find largest daily change in case numbers
   4. .pct_change() % change in those case numbers

### Additional fuctions

.iterrows()

1. Did a str.split() to split the date column into Month, Day, & Year columns
2. Used iterrows to convert “4” to “April”
3. Then merged to get “Month YYYY”

.apply()

1. Apply a function along an axis of the DataFrame
2. (axis = 0 : apply function to columns, axis = 1 : apply function to each rows)

pd.agg()

1. The aggregate function is used for one or more operations, it was used here in order to get the min and max vaccination percent completed in the state.

Pandas.profileReport: (from pandas_profiling import ProfileReport)

   1. Pandas profile report generates multiple statistical results based on a given dataset. (vaccination_df)
   2. Each variable shows high correlation (Pearson’s r)

## Load

### Connection with SQLAlchemy

1. Connected Jupyter notebook with Postgresql by creating an engine to load data into SQL.
2. Create four tables in SQL to hold data that will be loading into Postgresql.
   1. [Table Names](schema_and_join.sql)
      1. Death_infections
      2. Test_case_ratios
      3. April_2020_2021
      4. Vaccination
      5. Cases by County
3. Export to SQL database
   1. Function - vaccination_df.to_sql(name='vaccination', con=engine, if_exists='replace', index=False)
4. Verified data was loaded by running SQL query in Jupyter notebook
   1. Function - pd.read_sql_query('select * from vaccination', con=engine).head()
5. Ran a join on 'County' in vaccination table and 'April_2020_2021' to compare April 2021 Coronavirus cases to total current cases.

   `SELECT va."County", va."Total_Current_Cases", ap."Cases"`\
   `FROM vaccination as va`\
   `JOIN april_2020_2021 as ap`\
   `ON (ap."County" = va."County");`

## Data Sources

- [Johns Hopkins University (JHU CSSE)](https://github.com/CSSEGISandData/COVID-19/blob/master/csse_covid_19_data/)
- [COVID ACT NOW](https://covidactnow.org)
  - The Covid Act NOW API provides access to comprehensive Covid-19 data (current and historical).
  - The data is available for all US states, counties and metros, and is aggregated from a number of official sources, quality assured and updated daily.

## Authors

- **David W. Mueller**
  - [LinkedIn Profile](https://www.linkedin.com/in/davidwaltermueller/)
  - davemuelle@gmail.com
- **Chloe Lee**
  - [LinkedIn Profile](https://www.linkedin.com/in/chloe-lee-858b34b8/)
- **Amanda Pesch**
  - [LinkedIn Profile](https://www.linkedin.com/in/amanda-pesch-6a45a155/)
- **Rna Babikar**
- **John Burke**

## Acknowledgments

- Michigan State University Data Analytics Bootcamp; June 15, 2021.

## References

"COVID-19 Data Repository by the Center for Systems Science and Engineering (CSSE) at Johns Hopkins University" or "JHU CSSE COVID-19 Data." This data set is licensed under the Creative Commons Attribution 4.0 International (CC BY 4.0) by the Johns Hopkins University on behalf of its Center for Systems Science in Engineering. Copyright Johns Hopkins University 2020. <https://github.com/CSSEGISandData/COVID-19/blob/master/csse_covid_19_data/csse_covid_19_time_series/time_series_covid19_confirmed_US.csv>

Covid Act Now. (2021). Vaccination Tracker. <https://covidactnow.org>
