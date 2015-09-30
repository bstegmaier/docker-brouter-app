# docker-brouter-app

## What is brouter?

There's a lot of routing software on the market, both free and commercial, both online and offline, both OSM and proprietary maps. However, when it comes to bike routing, it's hard to find something that's really useful. There's far less money and engineering power in the field compared to car navigation.

What do we expect from a bike routing software? It should be able to calculate more or less the routes that match your experience in the regions you are familiar with, thus giving you confidence that it yields the same quality in unknown regions. Scanning the market with that expectation gives little to no result.

Here's what makes BRouter unique:

* It uses freely configurable routing profiles
* It works fully offline on any Android phone and is interfaced to some of the most popular Android map tools
* It uses a sophisticated routing-algorithm with elevation consideration
* It offers alternative route calculations
* It supports via and nogo-points
* It can consider long distance cycle routes
* Routing data is available worldwide with automatic weekly updates

For more information see http://brouter.de/brouter/

## How to use this image

This image contains the pre-build brouter server listening on TCP port 17777, all you have to do is to provide routing profiles and map data.
Any application supporting the brouter api (brouter-web, qlandkartegt, ...) can then be used to calculate routes.

### Run as standalone server

Assuming you have three folders:

* *profiles* containing routing profiles (*.brf) and the _lookups.dat_ file
* *segments* containg the map data (*.rd5)
* custom profiles uploaded to the server will be stored in *customprofiles* (can be left empty)

For downloading those file you can also use the script from the docker-compose-brouter repository mentioned below.

You can then start a container using:

	docker run -p 17777:17777 -v /path/to/segments:/data/segments -v /path/to/profiles:/data/profiles -v /path/to/customprofiles:/data/customprofiles bstegmaier/brouter-app

### Run docker-compose with brouter-web

Using [docker-compose-brouter](http://github.com/bstegmaier/docker-compose-brouter) you will have [brouter-web](https://github.com/bstegmaier/docker-brouter-web) and brouter-app up and running in a breeze:

* Clone https://github.com/bstegmaier/docker-compose-brouter
* Download all necessary files (wget required)
	./download_profiles.sh
	./download_country.sh Germany
* Start the containers
	docker-compose up
* Navigate to http://localhost in your browser and enjoy

### Configuration

There are three environment variables (see Dockerfile for defaults), which can be used to tweak brouter:

* *REQUEST_TIMEOUT* specifies the time limit for calculating an individual route
* *JAVA_OPTS* may be used to alter Java specific options
* *MAX_THREADS*

Example: In order to calculate a route halfway across Europe, you might want to increase the timelimit to half an hour and the maximum heap size to 256MB.

	docker run -e 'REQUEST_TIMEOUT=1800' -e 'JAVA_OPTS="-Xmx256M -Xms128M -Xmn8M -XX:+PrintCommandLineFlags"' -p 17777:17777 -v /path/to/segments:/data/segments -v /path/to/profiles:/data/profiles -v /path/to/customprofiles:/data/customprofiles bstegmaier/brouter-app

