# Midbootcamp project | Analysing companies creation per country.


### 1. Motivation.

The main idea of this exercise is to analyse a dataset that contains the number of new LLC's created per country per year. The analysis covers the period from 2006 to 2020 and includes data for 163 countries. We would like to:
- See if we can relate this figures to some other variables.
- Obtain insights about the data and the possible relationships.


### 2. Sources.

- Dataset of new LLCs created per country per year. Worldbank. (https://www.worldbank.org/en/programs/entrepreneurship).
- Dataset of Economic freedom index. Heritage foundation. (https://www.heritage.org/index/explore?view=by-region-country-year).
- Dataset of Corporate tax. Tax Foundation. (https://taxfoundation.org/data/all/global/corporate-tax-rates-by-country-2022/).
- Dataset of Corporate tax. OECD. (https://stats.oecd.org/Index.aspx?DataSetCode=CTS_CIT#).
- Dataset of Global innovation index. Worldbank. (https://tcdata360.worldbank.org/indicators/3aa2eb70?country=BRA&indicator=40712&viz=line_chart&years=2013,2020).
- Dataset of median age of the population. Our world in data (obtained from UN). (https://ourworldindata.org/grapher/median-age).


### 3. Datasets preparation.

The files with the raw files and the code used for preparation are in the folder 'Datasets preparation'.

The process was done separately for each dataset, as the original files come from different sources and the format is different.
- The datasets are formatted so that each row represents the register of a year for an individual country.
- A column with the iso code of the country (alpha3) is introduced. We used pycountry library for this.
- An "id" column is introduced to uniquely identify each row. It is formed by the combination of the country code and the year of the register (for example: "ESP-2006").

As the datasets contain historical information and cover large lists of countries, all of the files contained missing data (represented with nan values). The majority of these values are not replaced nor this rows eliminated (we want to keep the most of the information from the sources, as it can help us during EDA or analysis). We do remove some other 'nan' values during the analysis, whenever we see it's necessary or useful to do so.

We find the largest amount of nan values in the following columns of the Economic freedom index dataset (this is due to the fact that these metrics were not computed until 2017):
- Judicial Effectiveness (and therefore the category to which this metric belongs, Rule of Law). Rule of Law category is added later in the file 'main' (we dind't have this column in the data from the source).
- Fiscal Health (and therefore the category to which this metric belongs, Government Size). Government size category is added later in the file 'main' (we dind't have this column in the data from the source). 

All of these tables are transferred to a MySQL Workbench schema (named nbc):

![nbc_schema](https://github.com/92CMDiego/mid-project-analysing-companies-creation/blob/main/images/nbc_schema.jpg)

Example of the table created for the dataset containing the number of new LLCs created per country per year:

![example_newllc_table](https://github.com/92CMDiego/mid-project-analysing-companies-creation/blob/main/images/example_newllc_table.jpg)


### 4. Main file.

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


### 5. Analysing.

The dataframe 'main' serves as a basis for the analysis.

'Analysing_correlations_hypothesis_and_overall_check' file. We first check the correlation between the economic freedom index and the new business density rate (we obtain a correlation above 0.6). We also used the two sample independent t-test (we assume both groups are independent):
- We divide the data into two groups (lower level of economic freedom scores, upper level of economic freedom scores).
- We apply the t-test for the mean of the new business density rate of the two groups.
- Our null hypothesis is that the mean for both groups is equal.
- For an alpha value of 0.05, we can reject the null hypothesis.

'Creating_combined_score' file. For the analysis, we decided to create a combined score of two metrics: new business density rate and economic freedom index overall score. We create a new dataframe that contains the following information:
- Average new business density rate per country.
- Average economic freedom index per country.
- Average new business density rate per country (scaled using MinMaxScaler).
- Average economic freedom index per country (scaled using MinMaxScaler).
- Combined score of the two metrics (average wieghted score of the two metrics). Both metrics are equally weighted.

We don't have the same amount of data available for every year. So we checked that the trends presented in the insights are similar for the whole dataset than the ones we get if we only use the years for which we have all of the information.

There are other files used for analysis and obtaining insights.


### 6. Insights.

The main insights are summarized in the presentation file (pr_midpr_v1).