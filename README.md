
# Amazon Laptops and Reviewers Scraper

<p align="center">
  <img src="http://static1.businessinsider.com/image/539f3ffbecad044276726c01-960/amazon-com-logo.jpg" width="200"><br><br>
</p>

## Features:
-   **Scrape Amazon laptops from the search results**
-   **Scrape laptop specs from the product page**
-   **Scrape laptop top reviews**
-   **Scrape reviewers profile**
-   **Command Line Interface for filter based search**
-   **New! write filtered results directly to the database

## Requirements:
- BeautifulSoup4
- lxml parser
- pandas
- Selenium with correspodning webdriver
- Requests
- SQLite3

## Installation:
  ```
  git clone https://github.com/thewizardofozz/amazon_scraper.git
  pip3 install -r requirements.txt
  ```
  
 ## Run:
 ```
 run main.py
 ```
**We have programmed a task manager who executes the main.py once a day at 8am to update our db.**

 ## Current Status:
 - Stable: version 2.0
    - Scrapes laptop specs and reviewers details.
    - Scrapes based on user's filter choice
 ## Issues:
 - Amazon server occasionally blocks the ip address. 
 
 We try:
 
     - random sleep between the requests
     - adding a random proxy from a list of proxies for each request
     - adding a column 'valid' to check the validity of the scraping so we can retry scraping for the records that are not valids.

 
 ## Scraping
 `scraper_class.py`
 
 - Class Scraper: (parent)
    - param: url
    - function: get_soup() --> return the content of a web page after BeautifulSoup.
 
 - Class SearchPage: (child)
     - param: url
     - function: get_data() --> return a list of object oriented Laptop from the search page of Amazon.
  
 - Class Parameters: (child)
     - param: url
     - function: get_param() --> return an object oriented Features from the page of the laptop description.
     
 - Class Reviews: (child)
     - param: url
     - function: get_reviews() --> return a list of object oriented Review for a specific laptop.
     
 - Class ProfileScrapper:
      - param: url
      - function: user_profile() --> return an object oriented Profile for each user


## Features Engineering

We use the aylien api to enrich our database: 
`https://aylien-text.p.rapidapi.com/sentiment`

The api gives us the sentiment analysis of the content of the reviews.

We end with four new columns:
- Polarity (positive/negative)
- Subjectivity (objective/subjective)
- Polarity confidence (number between 0 to 1)
- Subjectivity confidence (number between 0 to 1)

We choose this api because it allows us to make 1000 requests per day for free, whereas the others were limited to 500 (or 1000) in total.

 ## Command Line Interface
 `cli_main.py`
 - cli_tool:
      - params: argprase parameters
      - Interacts with argparse --> returns a search query
      - Multiple functions which refine the search and build the query
      - Saves search results into the DB
 
 ## Run:
  ```
 run main_cli.py
 ```     
<p align="center">
  <img src="https://github.com/thewizardofozz/amazon_scraper/blob/master/images_readme/filtering.png" width="600"><br><br>
</p>

## Object Oriented:
   `laptop_class.py`
    `laptop_features_class.py`
    `reviews_class.py`
    `profile_class.py`
    

<p align="center">
  <img src="https://github.com/thewizardofozz/amazon_scraper/blob/master/images_readme/Class.PNG" width="600"><br><br>
</p>

## Functions explanation:

- add_to_db(): add all the arguments to the corresponding table in the database.
- if_exist(): Check if a record already exists in the table.
- update_db(): update an existing record in the corresponding table of the database.
- get_arg_db(): request information from the corresponding table in the database.
- get_arg(): get information from the attributes of the class.

## Database Structure
`Createdb.py`

<p align="center">
  <img src="https://raw.githubusercontent.com/thewizardofozz/amazon_scraper/master/images_readme/ERD.png" width="600"><br><br>
</p>

- The table laptop and profile need to be updated frequently because they contains attributes that are constantly changing.
- The table laptop_features is updated when a record is not valid or when a new laptop is added to amazon.
- The table reviews is updated when a new review is added.

## Log
`Logging.py`
`logs.log`
- INFO: 
   - When a record is added, updated to a table.
- WARNING: 
   - When Amazon return a blank page. (Because it blocks us, or because it's a laptop accessories and not a laptop so the source page is different)
- ERROR: 
   - When using python functions. (Because we did not meet yet all the possibly errors we use a general handle errors that blocks all the errors and gives us the description of the error in the log.)


## Configuration
`config.py`

