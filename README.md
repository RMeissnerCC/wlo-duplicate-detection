# WLO Duplicate Detection

A utility to detect near duplicates in the WLO dataset.

The tool is based on the [MinHash](https://en.wikipedia.org/wiki/MinHash) algorithm. Parts of the implementation were taken from [https://github.com/chrisjmccormick/MinHash](https://github.com/chrisjmccormick/MinHash).

 
## Prerequisites

- Install [Docker](https://docker.com/).
- Build the Docker container.

```
sh build.sh
```
## Training (calculate hashes)

The `data` folder contains the dataset with one entry (document) per line. The first word of each line is the document's ID. 

- The following script calculates the hashes for each document and stores the interim data, which is necessary for prediction, also in the data folder.

```
sh runTraining.sh
```

## Prediction (find duplicates)

- To test the detection just query the system with an existing document's text.

```
sh runPrediction.sh "Bruchterme - gemeinsamer Nenner Bruchterme - gemeinsamer Nenner_1603916225648 Suche den gemeinsamen Nenner der beiden Bruchterme!   Mathematik Bruchterme Termumformungen"
```

The result is a list of tuples containing the relevant document IDs, similarity score (usually near 1.0 for duplicates) as well as the documents text. 

'''
['3ebe9c55-3405-4411-98f6-b5c581bb000e', 0.9999999999999998, ' Bruchterme - gemeinsamer Nenner Bruchterme - gemeinsamer Nenner_1603916225648 Suche den gemeinsamen Nenner der beiden Bruchterme!   Mathematik Bruchterme Termumformungen\n']
'''

(Only documents with an least similarity > 0.8 are returned. Might be set in the code.)

## Webservice

- To run the subject prediction tool as a simple REST based webservice, the following script can be used:

```
sh runService.sh
```

- The scripts deploys a CherryPy webservice in a docker container listening at `http://localhost:8080/duplicates`.

- To retrieve the recommendations, create a POST request and submit a json document with a text as for example: 

```
curl -d '{"text" : "Bruchterme - gemeinsamer Nenner Bruchterme - gemeinsamer Nenner_1603916225648 Suche den gemeinsamen Nenner der beiden Bruchterme!   Mathematik Bruchterme Termumformungen"}' -H "Content-Type: application/json" -X POST http://0.0.0.0:8080/duplicates
```	