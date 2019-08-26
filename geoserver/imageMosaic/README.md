# Publish ImageMosaic in Geoserver by REST

## Requirements

Make sure you have the following libraries/softwares installed:

- [`postgresql`](https://www.postgresql.org/download/)
- [`postgis`](https://postgis.net/)
- [`Geoserver`](http://geoserver.org/)

or:
- [`Docker`](https://docs.docker.com/install/overview/)
- [`Docker Compose`](https://docs.docker.com/compose/)

## Preparing the environment 
1. Start DB and Geoserver
    ```
    docker-compose up -d
    ```

2. Create workspace in Geoserver
    - access geoserver web app (localhost:8080/geoserver)
    - make login (user: admin/ pwd: geoserver)
    - click in workspace then 'add new workspace'
    - fill in the workspace name and url (E.g. BDC / localhost) and click in 'send'

3. Create Database in postgresql and add POSTGIS extension
    E.g. In PSQL:
    - 'create database bdc;'
    - '\c bdc;'
    - 'create extension POSTGIS;'

4. Save the tiff files in the 'files' folder
    - for each ImageMosaic:
        - create a folder with the name of the mosaic in the 'files' folder
        - move the geoTiffs of this mosaic to the folder
        - the name of each tiff should follow the structure: MosaicName_yyyy-mm-dd_OthersParameters.tif (E.g. Cerrado_2018-05-01_nir.tif)

## Publishing Mosaic
To publish Cerrado mosaic, must:

1. edit and move three files (from the 'helpers' folder) to the mosaic folder;
    - indexer.properties (file with the columns of the table in the database)
    - datastore.properties (file with database connection information)
    - timeregex.properties (file with regex to extract date)

2. create coverage store (if not exists) and publish layer/mosaic
    - replace 'bdc' by workspace name
    - replace 'brazil' by coveragestore name
    - replace 'Cerrado' by coverage(mosaic) name
    - curl -v -u admin:geoserver -XPUT -H "Content-Type: text/plain" -d '/home/data/Cerrado' http://localhost:8080/geoserver/rest/workspaces/bdc/coveragestores/brazil/external.imagemosaic?configure=all&coverageName=Cerrado

    *obs*:
    - if you want to pass other information such as description, projection ... call the request with the URL below:
    - edit file mosaic.xml (in 'helpers' folder) with new mosaic information and click submit: 
    - curl -v -u admin:geoserver -XPUT -H "Content-Type: text/xml" -d @helpers/mosaic.xml http://localhost:8080/geoserver/rest/workspaces/bdc/coveragestores/brazil/coverages/Cerrado

    **extras, if you need it:**
    - you can post just a few tiffs
    - extract other parameters from file names (E.g. Bands)
    
    *always changing existing regexes in the .properties files*


## View Result
1. open layer with openlayers (viewer default in geoserver)
2. click in '...' button
3. in CQL input, take the select/filter (E.g. ingestion AFTER 2019-06-01T00:00:00Z AND ingestion BEFORE 2019-08-01T00:00:00Z)