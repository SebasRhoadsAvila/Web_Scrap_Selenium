# Web-Scraping Exercise for Mexican States Databases

## Project Overview

This is a web-scraping exercise for a project that involves filling three columns from various databases of many Mexican states. The exercise found in this repository focuses on the state of Zacatecas and serves as an example of automatic scraping for pages made with JavaScript.

## Repository Contents

- **`WebScrapingZacatecas.ipynb`**: The main Python script that performs the web scraping.
- **`ESTADOS.xlsx`**: The processed dataset.
- **`README.md`**: This file, providing an overview and instructions.

## Database Structure

The database contains many columns, from which we only cared about the last four. One column had, for every entry, different links for different pages with the same structure. The other columns were empty, and were supposed to be filled with the information from each respective link. The relevant columns were:

1. **`Dirección del anuncio`**: Contains links to web pages with the required information.
2. **`Descripción CUCoP+`**: Product description.
3. **`Canidad `**: Quantity of the product bought.
4. **`Unidad de medida`**: Unit of Measure.

## Web Page

**CompraNet**: 

This is an example from the first link in the column
    
    https://upcp-compranet.hacienda.gob.mx/sitiopublico/#/sitiopublico/detalle/0f6117fcc2bd4c15b9ccf4b5c69fef30/procedimiento

Happy scraping!
