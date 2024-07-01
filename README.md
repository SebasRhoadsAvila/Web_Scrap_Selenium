# Web-Scraping Exercise for Mexican States Databases

## Project Overview

This is a web-scraping exercise for a project that involves filling three columns from various databases of many Mexican states. The exercise found in this repository focuses on the state of Zacatecas and serves as an example of automatic scraping for pages made with JavaScript.

## Repository Relevant Contents

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

This is an example from the first link in the column:
    
    https://upcp-compranet.hacienda.gob.mx/sitiopublico/#/sitiopublico/detalle/0f6117fcc2bd4c15b9ccf4b5c69fef30/procedimiento

![Example](WebScrapping/webscrp1.png)

At the bottom, we can find a 1-row table. From there, I extracted the information from columns 4, 6, and 7. This process is repeated for each link in the **`ESTADOS.xlsx`** dataset. However, there is a catch: some links contain a table with up to 50 rows that the dataset wasn't aware of.

For example:
    
    https://upcp-compranet.hacienda.gob.mx/sitiopublico/#/sitiopublico/detalle/0f6117fcc2bd4c15b9ccf4b5c69fef30/procedimiento

![Example](WebScrapping/webscrp2.png)

For these, the algorithm iterates over every row and adds them to the final dataset. If the initial observations were 65, the final results could have a thousand or more, depending on how many rows were contained in the relevant table of each link.

## Code Explanation

---
This part of the script is responsible for setting up the necessary libraries and loading the Excel file:

```python
# 1
import pandas as pd
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

# 2
excel_path = 'C:/Users/Acer/Downloads/ESTADOS.xlsx'
df = pd.read_excel(excel_path, sheet_name='ZACATECAS 2023')

# 3
def setup_driver():
    options = webdriver.ChromeOptions()
    options.add_argument('--headless')
    driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=options)
    return driver
```
1. **Importing Libraries:**

- **pandas**: A powerful library for data manipulation and analysis.
- **selenium**: A tool for automating web browsers. It is used here for web scraping.
- **webdriver_manager.chrome**: A helper to manage ChromeDriver binaries.
- The **By**, **Service**, **WebDriverWait**, and **expected_conditions** classes from **selenium** are used for locating elements, managing the ChromeDriver service, waiting for elements to load, and defining conditions for waiting, respectively.

2. **Loading the Excel Files:**

- The `pd.read_excel()` function loads the Excel file located at the specified path (`excel_path`).
- `sheet_name='ZACATECAS 2023'` specifies the sheet to be read from the Excel file.
- The data from this sheet is stored in a DataFrame called `df`.

3. **Setting Up Selenium WebDriver:**

- The `setup_driver()` function configures Selenium to use a headless Chrome browser. Headless mode means the browser runs without a GUI, which is useful for automated scripts.
- `options.add_argument('--headless')`: Adds the argument to run Chrome in headless mode.
- `webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=options)`: Initializes the Chrome WebDriver using the ChromeDriverManager to handle driver installation automatically.
- The function returns the configured WebDriver instance, `driver`.



---

This code is responsible for iterating through URLs, extracting data from web pages, and saving the data into an Excel file. Here's a detailed explanation:

```python
# 1
data = []

# 2
for index, url in enumerate(df['Dirección del anuncio'], start=1):
    driver = setup_driver()
    driver.get(url)
    driver.implicitly_wait(30)  # Esperar a que la página cargue completamente
    print(f"Procesando URL {index} de {len(df)}: {url}")

    # 3
    base_selector = "#p-tabpanel-5 > div > app-sitiopublico-detalle-economicos-pc > div > div > app-sitiopublico-detalle-economicos-list > div.p-col-12.p-sm-12.p-md-12.p-lg-12.ng-star-inserted > p-table > div > div > div > div.p-datatable-scrollable-body.ng-star-inserted > table > tbody > tr"
    rows = driver.find_elements(By.CSS_SELECTOR, base_selector)
    if not rows:
        base_selector = "#p-tabpanel-5 > div > app-sitiopublico-detalle-economicos-pc > div > div > app-sitiopublico-detalle-economicos-list > div.p-col-12.p-sm-12.p-md-12.p-lg-12.ng-star-inserted > p-table > div > div > table > tbody > tr"
        rows = driver.find_elements(By.CSS_SELECTOR, base_selector)
    # 4
    for row in rows:
        description = row.find_element(By.CSS_SELECTOR, 'td:nth-child(4)').text
        unit = row.find_element(By.CSS_SELECTOR, 'td:nth-child(6)').text
        try:
            quantity = row.find_element(By.CSS_SELECTOR, 'td:nth-child(7)').text
        except Exception:
            quantity = "N/A"  # Usa "N/A" o cualquier otro valor que represente un dato no disponible
        # 5
        data.append({'Dirección del anuncio': url, 'Descripción CUCoP+': description, 'Cantidad': quantity, 'Unidad de medida': unit})
        print(f"Extraído: Descripción - {description}, Unidad - {unit}, Cantidad - {quantity}")
    # 6
    driver.quit()

# 7
new_df = pd.DataFrame(data)
new_df.to_excel('C:/Users/Acer/Desktop/PythonNotes/Python/DataSetsPython/ZACATECAS_RESULTS.xlsx', index=False)
print("Proceso completado. El archivo Excel ha sido actualizado.")
```

1. **Initialize Data Storage**:
   - `data = []`: An empty list to store the extracted data.

2. **Iterate Over URLs**:
   - `for index, url in enumerate(df['Dirección del anuncio'], start=1)`: Loops through each URL in the 'Dirección del anuncio' column of the DataFrame.
   - `driver = setup_driver()`: Calls the `setup_driver()` function to initialize the web driver.
   - `driver.get(url)`: Opens the web page for the current URL.
   - `driver.implicitly_wait(30)`: Waits up to 30 seconds for the page to load completely.
   - `print(f"Procesando URL {index} de {len(df)}: {url}")`: Prints the progress of URL processing.

3. **Extract Table Data**:
   - `base_selector = "... > table > tbody > tr"`: Defines the CSS selector for locating table rows.
   - `rows = driver.find_elements(By.CSS_SELECTOR, base_selector)`: Finds all rows in the table using the defined CSS selector.
   - If no rows are found, it tries an alternative CSS selector to locate the table rows.

4. **Extract Data from Each Row**:
   - `for row in rows`: Loops through each row in the table.
   - `description = row.find_element(By.CSS_SELECTOR, 'td:nth-child(4)').text`: Extracts text from the 4th column (description).
   - `unit = row.find_element(By.CSS_SELECTOR, 'td:nth-child(6)').text`: Extracts text from the 6th column (unit).
   - `quantity = row.find_element(By.CSS_SELECTOR, 'td:nth-child(7)').text`: Extracts text from the 7th column (quantity). If this fails, it assigns "N/A" as the quantity.

5. **Store Extracted Data**:
   - `data.append({'Dirección del anuncio': url, 'Descripción CUCoP+': description, 'Cantidad': quantity, 'Unidad de medida': unit})`: Appends the extracted data to the list.
   - `print(f"Extraído: Descripción - {description}, Unidad - {unit}, Cantidad - {quantity}")`: Prints the extracted information.

6. **Close the Web Driver**:
   - `driver.quit()`: Closes the web driver after processing each URL.



7. **Save Extracted Data to Excel**:
   - `new_df = pd.DataFrame(data)`: Converts the list of extracted data into a DataFrame.
   - `new_df.to_excel('C:/Users/Acer/Desktop/PythonNotes/Python/DataSetsPython/ZACATECAS_RESULTS.xlsx', index=False)`: Saves the DataFrame to a new Excel file at the specified path.
   - `print("Proceso completado. El archivo Excel ha sido actualizado.")`: Prints a message indicating that the process is complete and the Excel file has been updated.


---

The code systematically goes through each URL, scrapes the required data from the web pages, and compiles it into a structured Excel file for further analysis.

Happy scraping!
