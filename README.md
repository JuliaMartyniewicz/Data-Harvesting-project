# Data-Harvesting-project
Final project for Data Harvesting subject - Master in Computational Social Science UC3M

#### Julia Martyniewicz, Alba García Vega

## Scottish Local Elections 2022 Scraper

This project scrapes and processes election results from Dundee, Edinburgh, and Perth, organizing the data into a structured format for analysis.

### Prerequisites

Before running the scraper, ensure you have the following R libraries installed:

    install.packages(c("rvest", "httr", "xml2", "stringr", "tidyverse", "ggplot2",     "tidyr"))

Use the library() function to load the needed libraries:

    library(rvest)
    library(httr)
    library(xml2)
    library(stringr)
    library(tidyverse)

### How to Run the Scraper

1st step: clone this repository by using the following information:

    git clone https://github.com/JuliaMartyniewicz/Data-Harvesting-project.git
    cd Data-Harvesting-project

2nd step: open R or RStudio and run the script. We recommend to use the R Markdown file (final project.Rmd). Nevertheless, if you prefer another approach, download and open final project.Rmd in RStudio. In both cases, just run the script from top to ottom!

### What does the scraper do?
In general, the scraper extracts election results from three cities:
* Dundee: Extracts tables from the Dundee City Council website.
* Edinburgh: Parses structured election data from the Edinburgh City Council page.
* Perth: Scrapes election results and voter turnout from the Perth website.

The results from all cities are merged into a single dataframe (combined_table).
Visualizations are generated using ggplot2 to show the number of elected candidates per party.

### City by city
#### Dundee
We have used the official website of the Dundee City Coundil to obtain the results. The first thing that does our scraper is to extract the titles, and then it eliminates the first two titles, because they do not match any table and, in general, do not provide valuable information. 
After obtaining the titles, the scraper extracts the tables associated to the titles and eliminates the first one, which is not important. In these tables we obtain information about the politicians that ran the elections: surname, firts name, party, number of first preference votes, and at which stage were they elected at (if they were, of course :)!)
Once we have the titles and the tables, the scraper obtains information about the Wards in Dundee by using the xpath to //strong parts of the nodes; these are the regions in which Dundee is divided, and in each of them they choose different politicians and a different number of them. For example, some of them choose 3 candidates, others choose 4. In this information we include: name of the ward, number of the ward (from 1 to 8), the electorate number (how many people vote at each ward), the percentage poll and how many candidates they choose. 
From this information, we want to extract two important aspects: electorate number and percentage poll. The next thing that does the scraper is to extract these pieces of information and clean them. 
With all the information, we create a table called election_final, which contains the ward name and number, how many candidates each of them chooses, the names of the candidates, their parties, at which stage were they elected, and the extracted and cleaned information about electorate numbers and percentage polls. 

The next step is to clean the table. First of all, the scraper filters out the rows that contain empty strings or "-". Having cleaned the table, we separate the coukn "ward" in two columns: ward_number and ward_name. The scraper also joins the "Surname" and "Other names" (first name) columns in only one column, Full_Name, which will contain the full name of the candidates. It changes the name of the party column to "Party", up until now it has been called "Description". Finally, it deletes all the "ward" words from the ward_number column, so we only have numbers in it; and it changes the order of the columns so that the Full_Name one is the fourth one, after the two ward columns and the number of candidates one.
With this, Dundee is ready! 

#### Edinburgh
Again, we use the official website of the Edinburgh city council to obtain the election´s data. The scraper starts by extracting the different ward sections, which in this case are <h3> elements instead of //strong. Then, it uses a function designed to extract data from each of the wards in Edinburgh: ward´s name and number and list of elected candidates in each ward, first of all. Then, it creates a dataframe with the candidates´ information. Having obtained the dataframe, it goes on to get information about votes cast, electorate numbers and turnout in the wards. The scraper then will print this information in order to debug it.
It continues extracting electorate and voting information and numbers by using regular expressions. Then, it was needed an improved turnout extraction so that both formats of the turnouts were to be handled. As you may note in the official website, some of the turnouts were informed as this: Turnout: 51.1%. Nevertheless, other wards informed their turnouts like this: Turnout: % 50.8. The percentage symbol is sometimes before and sometimes after the number, so the scraper has both models in consideration when extracting the information.
After obtaining all the values, the scraper replicates the ward data for each of the candidates, adding the ward number and the ward name. Finally, all the information of ward_data and their candidates is combined in ward_data.
The function is, again, applied to each of the wards´ header. Once we have all the information from the wards, from both the headers and general text, the scrappeer combines all of it in one single data frame. It cleans it up, changes the order od the words in the "Candidate" column and eliminates the capital letters in their surnames... And, finally, prints the final result! That is for Edinburgh! Only one to go :)

#### Perth
Again, we will use the official Perth council website to get valid information.  To obtain the election results, the scraper will start by targetting the tables with the class "borchestergenericgeneral". The html_nodes() function identifies these elements, and html_table(fill = TRUE) converts them into data frames. Each table will corresponde to a ward's election results.
The scraper then extracts the names of the electoral wards from <h3> elements. These elements contain information about the wards to be found in Perth. Then, the scraper gets the turnout percentages of each of the ward´s information. In this case, it is easier than in the Edinburg one: the scraper searches for patterns that match "Percentage turnout", followed by a numerical value. Nevertheless, we have learned from the Edinburgh experiences that these turnout formats may vary, so we make sure that the scraper also removes unnecessary text and converts the extracted values into numeric format. Any NA values in the turnout vector are filtered out, so we get clean data. With all this information, the scraper initializes an empty list called "results" that contains the election data for each ward. It then loops through all extracted tables, processing them one by one and getting all the information we need out of them. In this loop, the scraper starts by cleaning each of the tables, filtering out rows where the first column contains the words "Candidate" or "Party." This is a way of making sure that only relevant candidate information is kept.
Next, the scraper also filters out only the elected candidates by checking if the "Elected at Stage Number" column contains valid values; this is, no NAs or "-". We want to keep only the candidates that have been elected, not the ones that have ran the elections. 
The scraper then assigns the corresponding ward name to each of the elected candidates and matches the turnout percentage for that ward. We must keep in mind that the turnout values may be fewer than the number of wards, so the scraper also checks if an index exists before assigning a value.
Finally, the scraper prepares the final dataset. In order to do so, it makes sure that the extracted data has a standardized structure and format by assigning the appropriate column names and attaching the cleaned ward data to the results list.
After it has processed all the tables, the scraper combines the individual ward results into a single dataframe.

### Final output
Finally, we have tables with information of the three cities! The scraper must ready the data, so it can join all of the tables in one. First of all, it adds a new column to each of the tables: the column "City", that will have only one value for each of the tables: Dundee, Edinburgh or Perth. 
Having done that, the scraper renames the tables´ columns, so that their columns´ names match the ones in the other two tables and avoid problems when joinning them. Before joinning, it ensures that all is in order and, finally, it starts joinning! It uses the function bind_rows() to do so. And, finally, it cleans the Party column, so that the words in the parties´ names are correctly separtaded. Finally, we have the final table (combined_table), with all the information!
You may also see the plots that can be made with the obtained information, raphical representations of the party distributions in each of the cities. 


### Notes

* The scraper uses XPath and CSS selectors to extract the relevant data.
* The script includes cleaning steps such as removing missing values and standardizing party names.
* If website structures change, minor modifications to the extraction logic may be required.
