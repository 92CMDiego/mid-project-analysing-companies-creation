# Midbootcamp project | Analysing companies creation per country.
##### 1. Motivation.

The main idea of this exercise is to analyse a dataset that contains the number of new LLC's created per country per year. The analysis covers the period from 2006 to 2020 and includes data for 163 countries. We would like to:
- See if we can relate this figures to some other variables.
- Obtain insights about the data and the possible relationships.

##### 2. Sources.

- Dataset of new LLCs created per country per year. Worldbank. (https://www.worldbank.org/en/programs/entrepreneurship).
- Dataset of Economic freedom index. Heritage foundation. (https://www.heritage.org/index/explore?view=by-region-country-year).
- Dataset of Corporate tax. Tax Foundation. (https://taxfoundation.org/data/all/global/corporate-tax-rates-by-country-2022/).
- Dataset of Corporate tax. OECD. (https://stats.oecd.org/Index.aspx?DataSetCode=CTS_CIT#).
- Dataset of Global innovation index. Worldbank. (https://tcdata360.worldbank.org/indicators/3aa2eb70?country=BRA&indicator=40712&viz=line_chart&years=2013,2020).
- Dataset of median age of the population. Our world in data (obtained from UN). (https://ourworldindata.org/grapher/median-age).
##### 3. Datasets preparation.

The files with the raw files and the code used for preparation are in the folder 'Datasets preparation'.

The process was done separately for each dataset, as the original files come from different sources and the format is different.
- The datasets are formatted so that each row represents the register of a year for an individual country.
- A column with the iso code of the country (alpha3) is introduced. We used pycountry library for this.
- An "id" column is introduced to uniquely identify each row. It is formed by the combination of the country code and the year of the register (for example: "ESP-2006").

As the datasets contain historical information and cover large lists of countries, all of the files contained missing data (represented with nan values). The majority of these values are not replaced nor this rows eliminated (we keep most of the information of the source). We do remove some other 'nan' values during the analysis.

We find the largest amount of nan values in the following columns of the Economic freedom index dataset (this is due to the fact that these metrics were not computed until 2017):
- Judicial Effectiveness (and therefore the category to which this metric belongs, Rule of Law). Rule of Law category is added later in the file 'main' (we dind't have this column in the data from the source).
- Fiscal Health (and therefore the category to which this metric belongs, Government Size). Government size category is added later in the file 'main' (we dind't have this column in the data from the source). 

All of these tables are transferred to a MySQL Workbench schema (named nbc).
##### 4. Main file.

We created a dataframe that would serve as a basis for the analysis. We start with:
- Dataframe of new LLCs created per country per year.

Throughout the process, we added the information that we think could potentially be valuable for the analysis:
- Economic freedom index.
- Corporate tax (source: Tax foundation).
- Corporate tax (source: OECD).
- Global innovation index.
- Mean age of the population.

**Columns of the dataframe 'main'**:
- id.
- ISO_code_alpha3.
- Country.
- Adult population. Working age population (15-64) of the country.
- Year.
- Number of new Limited Liablity Companies.
- New business density rate. The number of companies created in the country for that year per 1,000 inhabitants.
- Economic freedom index. Economic freedom index overall score.
- Columns 9-20. 12 subcategories of the economic freedom index.
- Columns 21-24. 4 main categories (formed with the 12 subcategories) of the economic freedom index.
- Corporate tax (Tax Foundation).
- Corporate tax (OECD).
- Global innovation index. Global innovation index score.
- Median age. Median age of the population in the country for each year.

##### 5. Analysing.

The dataframe 'main' serves as a basis for the analysis.

'Analysis_correlation_and_overall_check' file. Our null hypothesis is that the correlation between the new business density rate and the economic freedom index overall score is smaller tha 0.5. For this step, we do not need to deal with the nan values, as .corr() function is not affected by this.
- We find a correlation larger than 0.6.

'Creating_combined_score'. For the analysis, we decided to create a combined score of two metrics: new business density rate and economic freedom index overall score. We create a new dataframe that contains the following information:
- Average new business density rate per country.
- Average economic freedom index per country.
- Average new business density rate per country (scaled using MinMaxScaler).
- Average economic freedom index per country (scaled using MinMaxScaler).
- Combined score of the two metrics (average wieghted score of the two metrics). Both metrics are equally weighted.

There are other files used for analysis and obtaining insights.