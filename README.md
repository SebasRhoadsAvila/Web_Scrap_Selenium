# Web-Scraping Exercise for Mexican States Databases

## Project Overview

This is a web-scraping exercise for a project that involves filling three columns from various databases of many Mexican states. The exercise found in this repository focuses on the state of Zacatecas and serves as an example of automatic scraping for pages made with JavaScript.

## Repository Contents

- **`WebScrapingZacatecas.py`**: The main Python script that performs the web scraping.
- **`data/`**: Directory containing the raw and processed data files.
- **`README.md`**: This file, providing an overview and instructions.

## Database Structure

The database contains many columns, from which we only cared about the last four. One column had, for every entry, different links for different pages with the same structure. The other columns were empty, and were supposed to be filled with the information from each respective link:

1. **`Dirección del anuncio`**: Contains links to web pages with the required information.
2. **`column_2`**: Initially empty, to be filled with data scraped from the links.
3. **`column_3`**: Initially empty, to be filled with data scraped from the links.
4. **`column_4`**: Initially empty, to be filled with data scraped from the links.

## How to Use

1. **Clone the repository**:
    ```sh
    git clone https://github.com/yourusername/repository-name.git
    cd repository-name
    ```
Happy scraping!
