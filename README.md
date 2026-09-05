# LinkedBikes---Semantic-Web-Bike-Station-Project
This project was developed by Master Students (Thet Kyi Htun,Htet Myet Aung,Khin Nadi Kyaw,Nan Hsu Wai) from University Of Computer Studies,Yangon (UCSY) in 2025-2026 Academic Year.

An end-to-end Semantic Web application that collects bike station data for multiple cities/countries, converts it into RDF triples, loads it into a triplestore, and exposes it through a web application that queries the triplestore using SPARQL.

---------------------------------------------------------------
TECHNOLOGIES
----------------------------------------------------------------

Backend (Data Import / RDF Generation)
  - Java 8 (JDK 1.8)
  - Apache Maven (build tool)
  - Apache Jena libraries (v3.13.0)   - RDF / SPARQL / RDFConnection
  - Apache Jena Fuseki               - RDF triplestore (port 3030)
  - JSON.simple (v1.1.1)              - JSON parsing
  - SLF4J Simple (v1.7.36)            - logging

Frontend / Web Application
  - PHP                                - server-side rendering
    + Fuseki SPARQL queries (via HTTP GET/POST)
    + Turtle generation and upload on city submission
  - HTML5 / CSS3 / JavaScript (vanilla + jQuery)
  - Bootstrap 4                        - UI framework
  - Font Awesome                       - icons

Data Formats
  - CSV    - manually added files in /Manually-added-files (*.txt)
  - Turtle - generated RDF output in /turtle-files
  - XML    - parser available for remote API feeds

Project Layout
  - src/main/java/
      Main/Main.java          - entry point, orchestrates the import
      Constants/Constants.java- triplestore URL config
      Models/                 - City and BikeStation POJOs
      FileReader/             - CSV file reader / parser
      JSONCityParser/         - JSON dataset parsers
      XMLParser/              - XML feed parser
      RDFGenerator/           - builds RDF (Turtle) from parsed data
  - Manually-added-files/     - CSV data for additional cities
  - turtle-files/             - generated .ttl output
  - semantic-web-project-web-app/
      index.php  - home page (browse + submit city)
      city.php   - city station view
      info.php   - about page
      upload.php - uploads new city Turtle into the triplestore
      js/rdf.js  - SPARQL query helpers
      js/parsers.js - response parsing for station data
      css/, img/, vendor/ - styles, images, bundled libraries

----------------------------------------------------------------
REQUIREMENTS
----------------------------------------------------------------

Software
  - JDK 8 or higher
  - Apache Maven 3.x
  - Apache Jena Fuseki (compatible with Jena 3.13.x)
  - PHP 7+ (with json and file_get_contents enabled)
  - A web server (e.g. Apache) configured with PHP
  - A modern web browser

Running Services
  - Fuseki triplestore must be running on http://localhost:3030
    with a dataset named:  bike_station_db
  - The web application expects the Fuseki endpoint at:
        http://localhost:3030/bike_station_db

----------------------------------------------------------------
RUNNING PROCESS
----------------------------------------------------------------

1) START THE TRIPLESTORE
   Start Apache Jena Fuseki with a dataset named bike_station_db:
       ./fuseki-server --port=3030 --update --mem /bike_station_db
   (for persistent storage use a TDB location instead of --mem)
   Verify it is up:  http://localhost:3030

2) BUILD THE JAVA IMPORT PROJECT
   From the project root, resolve dependencies and compile:
       mvn clean compile

3) RUN THE DATA IMPORT (loads RDF into Fuseki)
   Run the main class:
       mvn exec:java -Dexec.mainClass="Main.Main"
   OR (if the exec plugin is not configured):
       mvn package
       java -cp target/semweb-1.0-SNAPSHOT.jar Main.Main
   This performs:
     [1] Clears existing triples in the triplestore
     [2] Parses CSV files in /Manually-added-files and inserts RDF

4) VERIFY DATA
   Open the Fuseki UI at http://localhost:3030 and run a sample
   SPARQL query, e.g.:
       PREFIX dbo: <http://dbpedia.org/ontology/>
       SELECT ?city WHERE { ?city a dbo:city . }
   You should see cities from the imported datasets.

5) RUN THE WEB APPLICATION
   Point your web server document root at:
       semantic-web-project-web-app/
   Example (Apache virtual host or alias) so the app is served via
   PHP. Then open in the browser:
       http://<your-server>/index.php
   Features:
     - Browse existing bike station data by country and city
     - Submit a new city (upload.php) - generates Turtle from the
       form input and posts it into the Fuseki triplestore

----------------------------------------------------------------
NOTES
----------------------------------------------------------------
  - The triplestore URL is configured in:
        src/main/java/Constants/Constants.java
    and in the PHP files (city.php, upload.php, js/rdf.js).
    Change these if your Fuseki instance runs elsewhere.
  - The Main.java clears the triplestore on every run, so re-running
    the import replaces the dataset with fresh data.

----------------------------------------------------------------
References
----------------------------------------------------------------
  1. Berners-Lee, T., Hendler, J., and Lassila, O. (2001). The Semantic Web. Scientific
  American, 284(5), 34–43.
  2. Gruber, T. R. (1993). A translation approach to portable ontology specifications.
  Knowledge Acquisition, 5(2), 199–220.
  3. Antoniou, G., and van Harmelen, F. (2004). A Semantic Web Primer. MIT Press.
  4. W3C (2014). RDF 1.1 Concepts and Abstract Syntax. W3C Recommendation.
  https://www.w3.org/TR/rdf11-concepts/
  5. W3C (2014). RDF Schema 1.1. W3C Recommendation.
  https://www.w3.org/TR/rdf-schema/
  6. W3C (2012). RDF 1.1 Turtle. W3C Recommendation.
  https://www.w3.org/TR/turtle/
  7. W3C (2013). SPARQL 1.1 Query Language. W3C Recommendation.
  https://www.w3.org/TR/sparql11-query/
  8. W3C (2013). SPARQL 1.1 Update. W3C Recommendation.
  https://www.w3.org/TR/sparql11-update/
  9. Apache Jena. Apache Jena — A free and open source Java framework for
  building Semantic Web and Linked Data applications. https://jena.apache.org/
  10. DBpedia. DBpedia Ontology. https://www.dbpedia.org/
  11. Breadboard/W3C. WGS84 Geo Positioning: an RDF vocabulary.
  http://www.w3.org/2003/01/geo/
  12. https://github.com/Anass-ELGHAOUI/semantic-web-project
