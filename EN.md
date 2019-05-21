# CanLII REST API Documentation

CanLII maintains an API that allows authorized developers to programmatically access metadata about the CanLII collection.

The API is a read-only REST HTTP API. All request parameters are specified in the URLs used to make the calls. Information will be returned in the form of JSON structures.

To apply for an API key, please send a message with your contact information to the [feedback form](https://www.canlii.org/en/feedback/feedback.html).

## Common parameters

| Parameter | Description |
|--|--|
| language |  {en} or {fr}. This impacts the language of the output. When retrieving a list of legislative text using the legislationBrowse API, if using "en" as a parameter, the English name of statutes will be returned as output, and if using "fr", the French name will be returned. This functionality is restricted by what languages are available in the dataset.
| key |  Your API key.

## Getting  a list of courts and tribunals in the CanLII collection and their corresponding databaseId

### Call structure
    https://api.canlii.org/v1/caseBrowse/{language}/?api_key={key}

### Parameters

See "Common parameters" above.

### Example call

    https://api.canlii.org/v1/caseBrowse/en/?api_key={key}

This will return the following list:

    {
      "caseDatabases": [
        {
          "databaseId": "oncicb",
          "jurisdiction": "on",
          "name": "Criminal Injuries Compensation Board"
        },
        {
          "databaseId": "nlpc",
          "jurisdiction": "nl",
          "name": "Provincial Court of Newfoundland and Labrador"
        },
        {
          "databaseId": "nbls",
          "jurisdiction": "nb",
          "name": "Law Society of New Brunswick"
        },
        ... // other databases

## Getting a list of decisions from a specific caselaw database

### Call structure
    https://api.canlii.org/v1/caseBrowse/{language}/{databaseId}/?offset={offset}&resultCount={resultCount}

### Required parameters

| Parameter | Description |
|--|--|
| databaseId |   See above.
| offset | The record number from which to start returning results. Using 0 will return the records most recently added to that database, subject to optional parameters (see below).
| resultCount | The length of the list of results that will be returned. Max 10,000.

### Optional parameters

| Parameter | Description |
|--|--|
| [&publishedBefore=date] **or** [&publishedAfter=date] |The date when the decision was first published on CanLII. (Note: documents may be available only after this date. To get all current documents a two day lag is recommended.)
| [&modifiedBefore=date] **or** [&modifiedAfter=date] | The date when the content of the decision was last modified on CanLII.
| [&changedBefore=date] **or** [&changedAfter=date] | The date the metadata of the decision or its content was last modified on CanLII.
| [decisionDateBefore=date] **or** [&decisionDateAfter=date] | The date of the decision.

Notes:

 - "date" is a YYYY-MM-DD (ISO-8601) formatted date
 - The conditions are inclusive i.e. publishedAfter=2012-12-12 will return decisions published on 2012-12-12

### Example call

    https://api.canlii.org/v1/caseBrowse/en/onca/?offset=0&resultCount=3&publishedBefore=2015-01-01&api_key={key}

This will return:

    {
      "cases": [
        {
          "databaseId": "onca",
          "caseId": {
            "en": "2014onca925"
          },
          "title": "Ariston Realty Corp. v. Elcarim Inc.",
          "citation": "2014 ONCA 925 (CanLII)"
        },
        {
          "databaseId": "onca",
          "caseId": {
            "en": "2014onca922"
          },
          "title": "Kassburg v. Sun Life Assurance Company of Canada",
          "citation": "2014 ONCA 922 (CanLII)"
        },
        {
          "databaseId": "onca",
          "caseId": {
            "en": "2014onca921"
          },
          "title": "Gutowski v. Clayton",
          "citation": "2014 ONCA 921 (CanLII)"
        }
      ]
    }

## Getting metadata for a specific case
### Call structure
    https://api.canlii.org/v1/caseBrowse/{language}/{databaseId}/{caseId}/?api_key={key}

### Parameters

| Parameter | Description |
|--|--|
| databaseId |   See above.
| caseId | The case's unique identifier, as returned by the previous type of call. Generally corresponds to the CanLII citation.

### Example call
To fetch metadata for the Dunsmuir case (*Dunsmuir* v. *New Brunswick*, [2008] 1 SCR 190, 2008 SCC 9), you would use:

    https://api.canlii.org/v1/caseBrowse/en/csc-scc/2008scc9/?api_key={key}

This will return:

    {
      "databaseId": "csc-scc",
      "caseId": "2008scc9",
      "url": "http://canlii.ca/t/1vxsm",
      "title": "Dunsmuir v. New Brunswick",
      "citation": "[2008] 1 SCR 190, 2008 SCC 9 (CanLII)",
      "language": "en",
      "docketNumber": "31459",
      "decisionDate": "2008-03-07",
      "keywords": "adjudicator — review — reasonableness — administrative — procedural fairness",
      "concatenatedId": "2008csc-scc9"
    }

## Obtaining information about what a case cites and what cites it (citator)
### Call structure

      https://api.canlii.org/v1/caseCitator/{language}/{databaseId}/{caseId}/metadataType?api_key={key}

### Parameters

| Parameter | Description |
|--|--|
| databaseId |   See above.
| caseId | See above.|
| metadataType | citedCases **or** citingCases **or** citedLegislations|

### Example call
    https://api.canlii.org/v1/caseCitator/en/onca/1999canlii1527/citedCases?api_key={key}
    
This will return:

    {
      "citedCases": [
        {
          "databaseId": "onca",
          "caseId": {
            "en": "1998canlii2237"
          },
          "title": "Alper Development Inc. v. Harrowston Corp.",
          "citation": "1998 CanLII 2237 (ON CA)"
        },
        {
          "databaseId": "onca",
          "caseId": {
            "en": "1983canlii1820"
          },
          "title": "Berger v. Willowdale A.M.C. et al.",
          "citation": "1983 CanLII 1820 (ON CA)"
        }
        ... // other cases

## Fetching list of legislation and regulation databases

### Call structure

    https://api.canlii.org/v1/legislationBrowse/{language}/?api_key={key}

### Parameters
See "Common parameters" above.

### Example call

    https://api.canlii.org/v1/legislationBrowse/en/?api_key={key}

This will return the following list:

    {
      "legislationDatabases": [
        {
          "databaseId": "caa",
          "type": "ANNUAL_STATUTE",
          "jurisdiction": "ca",
          "name": "Annual Statutes of Canada"
        },
        {
          "databaseId": "ska",
          "type": "ANNUAL_STATUTE",
          "jurisdiction": "sk",
          "name": "Annual Statutes of Saskatchewan"
        },
        {
          "databaseId": "nur",
          "type": "REGULATION",
          "jurisdiction": "nu",
          "name": "Regulations of Nunavut"
            ... // other databases

## Getting list of legislation or regulation from a specific database

### Call structure

    https://api.canlii.org/v1/legislationBrowse/{language}/{databaseId}/?api_key={key}

### Parameter

| Parameter | Description |
|--|--|
| databaseId |   The code for the database for which you want a list. Generally, this will be the provincial two-letter code, followed by either "s" (for statutes), "r" (for regulations), or "a" (for annual statutes, when available). These can be obtained with the previous API call.


### Example call

    https://api.canlii.org/v1/legislationBrowse/en/ons/?api_key={key}

## Getting metadata for a specific legislation or regulation

### Call structure

    https://api.canlii.org/v1/{legislationBrowse}/{en}/{ons}/{legislationId}/?api_key={key}


### Parameters

| Parameter | Description |
|--|--|
| databaseId |   See above. |
| legislationId | Specific ID for the piece of legislation that is being queried. Obtained with the previous API call. |



### Example call

    https://api.canlii.org/v1/legislationBrowse/en/ons/rso-1990-c-a1/?api_key={key}

This will return:

    {
          "legislationId": "rso-1990-c-a1",
          "url": "http://canlii.ca/t/q",
          "title": "Abandoned Orchards Act",
          "citation": "RSO 1990, c A.1",
          "type": "STATUTE",
          "language": "en",
          "dateScheme": "ENTRY_INTO_FORCE",
          "startDate": "1990-12-31",
          "endDate": "1997-03-01",
          "repealed": "YES",
          "content": [
            {
              "partId": "1",
              "partName": "Main"
            }
          ]
        }

