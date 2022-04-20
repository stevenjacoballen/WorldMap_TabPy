# Tableau  + TabPy + Python
Inspiration: [Anya Prosvetova](https://www.youtube.com/watch?v=Xk67f45BuoA)

Unfortunately, Tableau Public does not allow workbooks that utilize analytic extension connections to be published, so the following is a brief write up about the workbook that I created.

## Overview

This project contains a dashboard that is comprised of the following three elements:

 1. A parameter (the selected country).
 2. A world map.
 3. A table of five calculated fields.

When the user clicks on a country, the table of calculated fields dynamically updates and displays information about the selected country, such as the capital, population, area (size), continent, and subregion. Image below:

!['usa_selected](https://github.com/stevenjacoballen/WorldMap_TabPy/blob/main/usa_selected.png)

## TabPy

The information displayed in these calculated fields is not sourced locally, however. This data is retrieved from an external API (REST Countries) via Python scripts in each calculated field. When a country is selected on the world map, the following process occurs:

 1. Each calculated field sends a call to the REST Countries API, passing the selected country as an argument.
 2. JSON responses are returned.
 3. JSON responses are parsed for the relevant data, i.e.- population.

The ability to execute Python scripts in Tableau in this manner is made possible by TabPy, the Tableau Python Server. This project implemented a local instance of TabPy, but TabPy can also be deployed remotely (Heroku, etc.)


## Python Scripts
Included below are two of the five Python scripts used in the calculated fields: Continent and Population. The primary difference between the two is that Continent returns a string, whereas Population returns a number, as denoted by `SCRIPT_STR` and `SCRIPT_REAL`  at the top of each script respectively. The other scripts behave in an identical manner, differing only in what values they return.

### Continent
```
SCRIPT_STR('

#import libraries that we will use
import requests

#create a new variable for the value from the parameter (field called Selected Country)
country = _arg1

#build the request URL
url = f"https://restcountries.com/v3/name/{country}"

#send the request and get the response in JSON format

response = requests.get(url)
response = response.json()

#parse JSON response to get the value for the continent
country_info = response[0]
continent = country_info["continents"][0]

#return the continent value
return continent
', ATTR([Selected country])
```
<br>

### Population
```
SCRIPT_REAL('

#import libraries that we will use
import requests

#create a new variable for the value from the parameter (field called Selected Country)
country = _arg1

#build the request URL
url = f"https://restcountries.com/v3/name/{country}"

#send the request and get the response in JSON format

response = requests.get(url)
response = response.json()

#parse JSON response to get the value for the country population
country_info = response[0]
population = country_info["population"]

#return the population value
return population
', ATTR([Selected country])
```

<br>

### Calculated Fields Table
Once the calculated fields have been scripted, they are placed into a table prior to being incorporated into the final dashboard, as shown below.

!['Calc Table'](https://github.com/stevenjacoballen/WorldMap_TabPy/blob/main/Calc_table.png)

Thank you!
