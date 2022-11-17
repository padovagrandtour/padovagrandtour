# Padova Grand Tour
Main mono repository for the Database 2 project "Padova Grand Tour".
Luca FABBIAN, Loic DUPUY-KERANGUEVEN, Jean LE CHEVALIER

**TL;DR:**

## Project motivation
As a group mostly composed of 2 Erasmus students and one italian student, our idea was to create an ontology related to tourism, specifically in Padova.
The main goal was to create and query some "Tours" regarding the artworks/cultural site they would contain.
In order to do so, we scrapped data from https://padovamusei.it/ and wikipedia. The scrapper returned a big Json file that required data processing in order to get csv files that would be easier to use for the rdf database creation. 


## Getting the data
Most of the official websites we refer to do not provide any kind of machine-friendly API. Thus, the only solution is to extract the information from the website itself. We have written some node+puppeteer data scrapers to accomplish these tasks. The resulting json files are stored into the `scraper/results` folder. Intermediate files are named with a `-tmp` suffix. If you want to generate those files again from scratch, follow the instructions below.

- Install `Node.js` version >=18.

- (optional) to use the copy of chrome/chromium/edge already installed in your computer, find the path with `whereis google-chrome` and setup:
```bash 
export PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true
export PUPPETEER_EXECUTABLE_PATH=/usr/bin/google-chrome #replace with your own path

# Remember: if you choose to use your already installed browser, you MUST export these var EVERY TIME you want to use the scraper.
```
- install node dependencies:
```bash
cd data-scraper
npm install
```

### Retrieving data from [padovamusei.it](https://padovamusei.it)

```bash
cd scraper
node padovamuseicivici.js
```
This will open a chrome window and will start browsing the [padovamusei.it](https://padovamusei.it). Do not interfer with the window while the program is running! It will take around 15mins to generate the json file.


### Retrieving data from [wikipedia.it](https://padovamusei.it)

```bash
cd scraper
node wikipedia-categories.js
```
This will open a chrome window and will start crawling the [Portale:Padova](https://it.wikipedia.org/wiki/Portale:Padova) page on wikipedia. Do not interfer with the window while the program is running! It will take around 15mins to generate the json file.


## Ingesting the data

In this step, the data taken from `scraper/results` is merged with the one taken from the enpoind, processed and used to populate the `data` folder.

```bash
pip3 install pandas rdflib roman
```


## Setting up GraphDB
This is done auto-magically via Docker. Just run `docker compose up`. This will create a new GraphDB repo from `data/graphdb-repo.ttl`, ingest all ttl files in `data/ttlData` and start a GraphDB server on <http://localhost:7210>.

Keep in mind that every time you shut down the docker compose service, everything is recreated and populated again from scratch. This is done on purpose, since during the development phase the data changes frequently, so it's important.


## Building the website and the entity website
We provide two websites, one is a webapp built on top of our database, the other is a static html file showing details on our schema.

You DO NOT NEED to build them manually. It is done every time you `git push` thanks to actions stored in `.github/workflows`


- Install `Node.js` version >=18.

```bash
npm run build    # or npm run dev
```

