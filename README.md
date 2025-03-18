# Data-Harvesting-project
Final project for Data Harvesting subject - Master in Computational Social Science UC3M

#### Julia Martyniewicz, Alba Vega

## Scottish Local Elections 2022 Scraper

This project scrapes and processes election results from Dundee, Edinburgh, and Perth, organizing the data into a structured format for analysis.

### Prerequisites

Before running the scraper, ensure you have the following R libraries installed:

    install.packages(c("rvest", "httr", "xml2", "stringr", "tidyverse", "ggplot2", "tidyr"))

### How to Run the Scraper

Clone the repository:

    git clone https://github.com/JuliaMartyniewicz/Data-Harvesting-project.git
    cd Data-Harvesting-project

Open R or RStudio and run the script.

Use the R Markdown file (final project.Rmd)
If you prefer another approach, download and open final project.Rmd in RStudio.

The scraper extracts election results from three cities:
* Dundee: Extracts tables from the Dundee City Council website.
* Edinburgh: Parses structured election data from the Edinburgh City Council page.
* Perth: Scrapes election results and voter turnout from the Perth website.

The results from all cities are merged into a single dataframe (combined_table).
Visualizations are generated using ggplot2 to show the number of elected candidates per party.

Example Output

* Raw Extracted Tables
* Cleaned & Merged Election Results
* Graphical Representation of Party Distribution

Notes

* The scraper uses XPath and CSS selectors to extract the relevant data.
* The script includes cleaning steps such as removing missing values and standardizing party names.
* If website structures change, minor modifications to the extraction logic may be required.
