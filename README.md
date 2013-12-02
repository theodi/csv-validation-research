This repository contains some sample files that show how different tools and data formats can be used to support the validation and description of CSV files.

Currently the repository contains a single example CSV file and several "schemas" in different formats. The intention is to show how the tools and formats vary in terms of their expressivity, feature set, etc.

The work builds on some previous research:

* [CSV Validation and Description - a Review](https://docs.google.com/document/d/1TDMWM8VGq3UdZ9gNSg_E7lLrFdD4mAOD803CaUR5KTA/edit?usp=sharing) -- summarises some research into approaches for describing and validating CSV files. The review looks at: how metadata is associated with files and what type of metadata is provided.
* [CSV Validation Survey](https://docs.google.com/spreadsheet/ccc?key=0AiswT8ko8hb4dEtOR0x1WkJ3LS1LSm1HQm1YQzZuSHc&usp=sharing) -- provides a comparison of the features of various tools and formats for describing CSV files. The feature chart uses the categories from the review document.

Creating sample schemas for some of the tools provides a little more insight into how they fare at describing some real-world data.

## The Data

The example CSV file is called `lr-pp-nov-2013.csv`. 

It's an extract of the first 10 lines from the [Land Registry Price Paid Data](http://www.landregistry.gov.uk/market-trend-data/public-data/price-paid-data/download) for November 2013. 

The file was downloaded, same data was extracted using `head` and then the file was manually edited to add a header row using [the definitions given here](http://www.landregistry.gov.uk/market-trend-data/public-data/price-paid-faq#m18).

The CSV file consists of 10 records described using 15 columns. All fields are quoted.

* `ID` -- unique record identifier. This is a GUID wrapped in curly braces
* `Price` -- the price paid for the property expressed as an integer
* `Date of Transfer` -- a date time, e.g. `"2013-10-04 00:00"` 
* `Postcode` -- a UK postcode
* `Property Type` -- single letter code for property type. (S, D, F, T)
* `Old/New` -- single letter code indicating whether this is an old or new property (Y, N)
* `Duration` -- single letter code indicating length of tenure. (F, L)
* `PAON` -- this and the following fields capture the address, many of these can be empty
* `SAON`
* `Street`
* `Locality`
* `Town/City`
* `Local Authority`
* `County`
* `Record Status` -- single letter code indicating status of the record. (A, C, D)

The data provides a reasonable starting point for exploring validation as it includes a mixture of text, number and date types. Several of the text types have different validation rules.

## The Schemas

### chkcsv.fmt
 
An ini file format, this time supported by the Python [chkcsv.py](https://pypi.python.org/pypi/chkcsv) library. Read the [package documentation](http://pythonhosted.org/chkcsv/) for notes on the file format.

To install and use the tool, download and unzip the `chkcsv.py` package then:

    sudo python setup.py install
    
To use the installed tool:

    chkcsv.py -f chkcsv.fmt lr-pp-nov-2013.csv

The schema allows description of:

* required fields, e.g. the identifier, price
* formats of specific fields, e.g. structure of the GUID, postcodes and the single letter codes
* number validation

However it couldn't deal with the date format, so this is validated with a regex.

### schema.ini

Another ini file format, `schema.ini` can be used by the Microsoft Jet database, ODBC drivers, etc. See this [MSDN article](http://msdn.microsoft.com/en-us/library/ms709353(v=vs.85).aspx ) for reference.

This schema only includes:

* Basic type information
* Checking of length of columns
* Mapping of dates using custom format

### csv-validator-schema

`csv-validator-schema.txt` is a custom text format supported by the [csv-validator tool and library](https://github.com/digital-preservation/csv-validator) produced by the UK National Archives.

To install the tool (make sure you have Java and Maven installed):

    git clone https://github.com/digital-preservation/csv-validator
    cd csv-validator
    mvn package    
    #create symlink to script
    ln -s `pwd`/csv-validator-cmd/target/csv-validator-cmd-1.0-SNAPSHOT-application/csv-validator-cmd-1.0-SNAPSHOT/bin/validate ~/bin/csv-validator

Then from this project directory:

    csv-validator lr-pp-nov-2013.csv csv-validator-schema.txt

The schema is able to:

* validate that `ID` is unique
* validate formats of the majority of fields using regexs

However as the tool only uses XML Schema date types, it can't deal with the custom date format. The schema also has to use numeric column references in some cases as it doesn't support field names with spaces and slashes.

### datapackage.json

The [Simple Data Format](http://dataprotocols.org/simple-data-format/) specification describes the use of a [Data Package descriptor](http://dataprotocols.org/data-packages/) to describe one or more CSV files using a combination ofthe [CSV Dialect Description Format](http://dataprotocols.org/csv-dialect/) and [JSON Table Schema](http://dataprotocols.org/json-table-schema/).

`datapackage.json` is an example of using those formats to describe the CSV file in this project. The schema is able to:

* capture basic types of columns, e.g. text, number, date
* describe format of date (although formatting is under-specified: its not clear what format strings are legal)
* provide some additional documentation about each field


