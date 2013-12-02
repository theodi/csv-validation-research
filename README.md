This repo contains some sample files that show how different tools and data formats can be used to support the validation and description of CSV files.

Currently the repository contains a single example CSV file and several "schemas" in different formats. The intention is to show how the tools and formats vary in terms of their expressivity, feature set, etc.

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

* `schema.ini` -- Ini file format supported by Microsoft Jet, ODBC drivers, etc. See this [MSDN article](http://msdn.microsoft.com/en-us/library/ms709353(v=vs.85).aspx ) for reference
* `chkcsv.fmt` -- Format descriptor supported by [chkcsv.py](http://pythonhosted.org/chkcsv/)
* `csv-validator-schema.txt` -- Custom text format supported by [csv-validator tool and library](https://github.com/digital-preservation/csv-validator)
* `datapackage.json` -- JSON [Data Package descriptor](http://dataprotocols.org/data-packages/) that follows principles of [Simple Data Format](http://dataprotocols.org/simple-data-format/). CSV file is described using [JSON Table Schema](http://dataprotocols.org/json-table-schema/) and [CSV Dialect Description Format](http://dataprotocols.org/csv-dialect/).

## Misc Notes

* The Land Registry data uses a date format that isn't supported by either chkcsv.py or csv-validator. This may be down to a bug in the former, but the latter only uses XML Schema data types
* csv-validator doesn't support column names with spaces or other characters, requiring use of column positions
* Only csv-validator and chkcsv.py are able to fully validate the files, largely by use of regular expressions. None of the others support other validation rules.


