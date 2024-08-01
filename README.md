# Postcode Distance Calculator
This project uses the ONS National Statistics Postcode Lookup (NSPL) dataset to calculate the distance in meters between two postcodes in the UK.

# Table of Contents
* Introduction
* Setup
* Usage
* Functions
* Example
* License

# Introduction
The Postcode Distance Calculator is a Python tool that calculates the distance in meters between two postcodes using their northing and easting coordinates from the NSPL dataset. The distance is computed using the Pythagorean theorem.

# Setup
Prerequisites
* Python 3.6 or higher
* pandas
* math


# Installation
1. Clone the repository:

```
git clone https://github.com/barrychocolate/NSPL-Postcode-Distance.git
```

2. Change to the project directory:
```
cd postcode-distance-calculator
```

3. Install the required packages:
```
pip install pandas
```

# Usage
1 Download the NSPL dataset from the [ONS Geography Portal](https://geoportal.statistics.gov.uk/) website and place it in the data directory.
2. Update the path to the NSPL file in the script.
3. Run the script to calculate distances between postcodes.

# Script Overview
Import libraries and read data:
```
import pandas as pd
import sqlite3
import itertools
import math

nspl_path = 'data/NSPL21_MAY_2024_UK.csv'
columns_to_load = ['pcds', 'oseast1m', 'osnrth1m']
nspl_df = pd.read_csv(nspl_path, usecols=columns_to_load)
```

Functions to format postcodes, get coordinates, and calculate distances:
```
def format_postcode(postcode):
    cleaned_postcode = postcode.replace(" ", "").upper()
    formatted_postcode = cleaned_postcode[:-3] + ' ' + cleaned_postcode[-3:]
    return formatted_postcode

def get_postcode_coordinates(postcode, df):
    formatted_postcode = format_postcode(postcode)
    result = df[df['pcds'] == formatted_postcode]
    if result.empty:
        return None, None
    northing = result['osnrth1m'].values[0]
    easting = result['oseast1m'].values[0]
    return northing, easting

def calculate_distance(start_pcode, end_pcode):
    northing1, easting1 = get_postcode_coordinates(start_pcode, nspl_df)
    northing2, easting2 = get_postcode_coordinates(end_pcode, nspl_df)
    if northing1 is None or northing2 is None or easting1 is None or easting2 is None:
        return None
    delta_easting = easting2 - easting1
    delta_northing = northing2 - northing1
    distance = math.sqrt(delta_easting**2 + delta_northing**2)
    return distance

```
Example usage:
```
data = {
    'id': [1, 2, 3, 4, 5],
    'start_postcode': ['AB1 0AA', 'AB2 0BB', 'AB3 0CC', 'AB4 0DD', 'AB5 0EE'],
    'end_postcode': ['AB1 1AA', 'AB2 1BB', 'AB3 1CC', 'AB4 1DD', 'AB5 1EE']
}
df = pd.DataFrame(data)
df['distance'] = df.apply(lambda row: calculate_distance(row['start_postcode'], row['end_postcode']), axis=1)
print(df)
```
# Functions
## format_postcode(postcode)
Formats a postcode by removing spaces, converting to uppercase, and inserting a space before the last three characters.

## get_postcode_coordinates(postcode, df)
Retrieves the northing and easting coordinates for a given postcode from the NSPL DataFrame.

## calculate_distance(start_pcode, end_pcode)
Calculates the distance in meters between two postcodes using their coordinates.

# Example
Here's an example of how to use the script to calculate the distance between two postcodes:
```
start_postcode = 'AB1 0AA'
end_postcode = 'AB1 1AA'
distance = calculate_distance(start_postcode, end_postcode)
print(f"The distance between {start_postcode} and {end_postcode} is {distance} meters.")
```
