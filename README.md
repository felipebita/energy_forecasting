1 - Problem Description
The aim of this report is to analyze the energy production data sourced from ONS and Eneva, with the goal of uncovering their interrelationship, distinctive features and make forecast analysis.

Eneva
Eneva stands as a prominent integrated energy operator within Brazil, involved in various facets such as natural gas exploration and production (E&P), along with providing comprehensive energy solutions. Operating across multiple states including Amazonas, Maranhão, Mato Grosso do Sul, and Goiás, Eneva manages 12 natural gas fields situated in the Parnaíba and Amazonas basins. Notably, it holds the largest onshore exploration concession area in Brazil, spanning over 63,000 km². With an impressive generation capacity of 5.95 GW in its portfolio, Eneva plays a pivotal role in ensuring reliable and competitive energy supply for the Brazilian electrical grid. Moreover, Eneva actively participates in the Free Energy and Natural Gas Market, boasting a robust business platform and being listed on the B3's New Market since 2007.

ONS
The National System Operator (ONS) assumes responsibility for coordinating and overseeing the operation of electricity generation and transmission facilities within the National Interconnected System (SIN). Additionally, ONS is tasked with planning the operations of isolated systems across the country, operating under the supervision and regulation of the National Electric Energy Agency (ANEEL).

2 - Methodology
2.1 - ONS data
The data sourced from ONS was collected using an automated script to download files from the website: https://dados.ons.org.br/dataset/geracao-usina-2. These files were filtered to include specific power plants: Maranhão III, Maranhão IV, Maranhão V, Nova Venécia 2, Parnaíba IV, Parnaíba V, Jaguatirica II, Fortaleza, Porto de Sergipe I, Porto do Itaqui, and Porto do Pecém II. Subsequently, the files were concatenated, and preprocessing steps were applied, including the creation of columns for individual power plants, year, and month. The production data was then aggregated on a monthly basis. Following this, additional columns such as "ID", "group", and "quarter" were generated, and the file was saved as "ons_ts.csv". Additionally, a metadata file detailing information about the power plants was created.

2.2 - Eneva data
The Eneva data was acquired from the website: https://ri.eneva.com.br/informacoes-financeiras/planilhas-interativas/, specifically from the 'Dados Operacionais Trimestrais' tab. The available power plants include: Porto de Itaqui, Porto do Pecém II, Parnaíba I, Parnaíba II, Parnaíba III, Parnaíba IV, Parnaíba V, Jaguatirica II, Porto de Sergipe I, and Fortaleza. Initially, all data was consolidated into a single file, which underwent manual formatting in Google Sheets to ensure compatibility for download as a usable .csv file. The formatted file can be accessed in the "Final" tab of the following link: https://docs.google.com/spreadsheets/d/1z6C00vMYI1gy7xM-CIT6eu6nTKSSzVxaPdJ6IHTlvts/edit?usp=sharing. Subsequently, the data was loaded into a notebook for further processing, which included adding the "ID" and "group" columns, arranging the columns, and finally saving the processed file as "eneva_ts.csv".

2.3 - SQLite database
The SQL database was constructed using the three tables obtained previously: "ons_ts", "ons_metadata", and "eneva_ts". The database file was named "fs_challenge.db".

2.4 - Power BI
The tables in the SQLite database were accessed via ODBC. Subsequently, two new tables were generated: "ons_quart_ts" was created by aggregating the "ons_ts" table to quarterly data, and "ons_eneva_innerjoin" was formed by joining the quarterly datasets of ONS and Eneva on the columns "year", "quarter", and "ceg_label" (power plant), retaining only matched rows. For visualization purposes, the "ons_ts" table was utilized to generate a seasonal plot, incorporating month information. Meanwhile, the "ons_eneva_innerjoin" table was employed to craft a quarterly plot comparing ONS and Eneva data. Additionally, a third visualization was produced showcasing the quarterly production of the ONS dataset. Moreover, in all visualizations, the ONS dataset was converted from megawatt hours (MWmed) to gigawatt hours (GWh) by dividing the aggregation by 1000.

2.5 Trend analysis
To commence the comparison of the ONS and Eneva series in terms of correlation, the "ons_eneva_innerjoin" PowerBI exported dataframe was utilized. For trend analysis and forecasting, the ONS data spanning from Q1-2014 to Q4-2023 was employed. The power plants considered in the analysis were those shared between the two datasets: Parnaíba IV, Parnaíba V, Jaguatirica II, Fortaleza, Porto de Sergipe I, Porto do Itaqui, and Porto do Pecém II.

Descriptive statistics of the series were obtained. Additionally, decomposition, ACF/PACF analysis, and Augmented Dickey-Fuller testing were conducted. The forecasting experiment was conducted using the last four quarters (1 year) as the test set, and five scenarios were explored, considering orders for AR (AutoRegressive) and MA (Moving Average) up to 4: without seasonal effect and differencing; seasonal effect (period=4) with 1 order differencing; seasonal effect (period=2) with 2 order differencing; seasonal effect (period=4) with 2 order differencing; and seasonal effect (period=2) with 2 order differencing.

Four metrics were employed to select the best models: AIC (Akaike Information Criterion), SMAPE (Symmetric Mean Absolute Percentage Error), MSE (Mean Squared Error), and MAE (Mean Absolute Error).

3 - Results
3.1 - Data wrangling
The ONS data processing resulted in a dataframe comprising 1250 rows aggregated by month. It initially included data from 7 power plants in 2014 and expanded to cover 11 power plants by March 2024. Conversely, the Eneva data, upon processing, yielded a quarterly series containing 213 samples. It commenced in 2016 with data from three power plants and extended to encompass ten power plants by Q3/2023. It's evident that the ONS data exhibits a higher frequency of updates compared to the Eneva data.

In addition to these two dataframes, a metadata table for ONS power plants was created, comprising information for 11 samples. Subsequently, all three datasets were incorporated into the SQLite database.

3.2 - Power BI
With the data available in the Power BI, the initial visualization featured a seasonal plot showcasing ONS data. While the difference may not be stark, there is a discernible trend indicating higher production in the second half of the year compared to the first half (Fig. 1). The dashboard filters enable users to select specific power plants, years, and months within the series for further analysis and exploration.
