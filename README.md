# RD80 Scripts

## Description

This repository contains a set of tools developed for processing RD80 disdrometer data collected in different field projects and observational campaigns. The code provides routines for data organization, preprocessing, and generation of standardized outputs, supporting precipitation microphysics studies and validation activities of satellite-based rainfall estimates.

In particular, the repository includes scripts to generate **netCDF** files from data in `.trf` format, as well as scripts to generate **figures and graphical products** from the processed netCDF files.

The available commands to execute the scripts are described below.

---

## Authors

The main authors and contributors of this software are:

1. **Alan James Peixoto Calheiros** – Project conception, scientific supervision, and overall coordination of the software development.  
2. **Thomaz A. Pougy** – Lead software developer.  
3. **Helvécio Bezerra Leal Neto** – Code refactoring, development of new functionalities, and documentation preparation.  
4. **Aline Andrade do Nascimento** – Support in implementation and deployment of the software in different processing environments.

Additional contributions may automatically appear in the GitHub contributors list.

---

# Installation

## Requirements

- Python 3.11 or higher  
- git  
- pip  

or  

- Anaconda

## Installation

Via pip:

```bash
git clone https://github.com/SInApSE-INPE/RD80-Processing.git
cd RD80-Processing
pip install -r requirements.txt
```

Via conda:

```bash
git clone https://github.com/SInApSE-INPE/RD80-Processing.git
cd RD80-Processing
conda env create -f environment.yml
```

---

# Gen netCDF

## Run for all `.trf` files in the directory `JOSS/input/data`

Generates one netCDF file for each day that contains data.

```bash
cd JOSS
```

```bash
python JOSS_gen_netCDF.py -s
```

---

## Run for all files in a specific directory

```bash
python JOSS_gen_netCDF.py -s -i /path/to/directory
```

---

## Run for a specific date

Generates a netCDF file only with the data from the date specified in the command.

```bash
python JOSS_gen_netCDF.py -s -d 31/01/2000
```

---

## Run for files listed in `files.txt`

Generates one netCDF file for each day containing data from the files whose names are included in the `files.txt` file in the `input/JOSS` directory.

```bash
python JOSS_gen_netCDF.py -l
```

---

## Run for a specific date from files listed in `files.txt`

Generates a netCDF file only with the data from the specified date for the files listed in `files.txt`.

```bash
python JOSS_gen_netCDF.py -l -d 31/01/2000
```

---

# Gen Figures

## Run for all netCDF files in the directory `input/JOSS/data_figures`

Generates all figures in PNG and HTML formats.

```bash
python JOSS_gen_figures.py -s
```

---

## Run for all netCDF files in PNG format

Generates only figures in PNG format.

```bash
python JOSS_gen_figures.py -s -p
```

---

## Run for files listed in `files_figures.txt`

Generates all figures for the files whose names are included in `files_figures.txt` in the `input/JOSS` directory.

```bash
python JOSS_gen_figures.py -l
```

---

## Run for files listed in `files_figures.txt` in PNG format

Generates only the figures in PNG format.

```bash
python JOSS_gen_figures.py -l -p
```

---

# Directory Structure

- `input/JOSS/data/` : Contains `.trf` files used to generate netCDF  
- `input/JOSS/data_figures/` : Contains `.nc` files used to generate figures  
- `input/JOSS/files.txt` : List of `.trf` files to be processed  
- `input/JOSS/files_figures.txt` : List of `.nc` files to be processed  

---

# PATTERN MODE EXPLANATION

## Example command

```bash
python JOSS_gen_netCDF.py -p "%Y%m%d" -d "01/08/2022"
```

---

## What it does

Receives the parameter `-d` and interprets it as a date. We then have:

datetime(year=2022,month=8,day=1)

Generates three temporary variables:

```
d0 = datetime(year=2022,month=8,day=1)
d1 = datetime(year=2022,month=8,day=1) + day
d2 = datetime(year=2022,month=8,day=1) - day
```

Uses the value received by parameter `-p` as a datetime formatter. It then formats `d0`, `d1` and `d2` as auxiliary strings:

```
d0 = "20220801"
d1 = "20220802"
d2 = "20220731"
```

Searches all files in the input directory, but returns in the list `files` only those files whose name satisfies the criteria:

1. The filename ends with the extension specified in `variables_info.json`
2. The filename contains any of the strings `d0`, `d1` or `d2`.

The list `files` is then used to read the files. Therefore, only the files of interest are processed.

---

# Important Notes

## A. Parameter `-p`

The format must correspond to how the date appears in the filenames located in the input directory.

For example, for the files:

```
RD-20220731-215400.txt
RD-20220801-215400.txt
RD-20220802-215400.txt
```

In:

```
RD-20220801-215400.txt
           ^^^^^^^^
        The date is here
```

The date format above is `YYYYMMDD`.

However, it must be provided according to the Python `strftime` conventions.

Details:  
https://strftime.org/

Therefore, the value passed to parameter `-p` should be:

```
%Y%m%d
```

---

## B. Parameter `-d`

Determines the reference day for which one day before and one day after will also be considered.

The parameter `-d` must always be provided whenever parameter `-p` is used.
