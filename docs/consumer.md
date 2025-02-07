# Consumer of Soil Data

In each of the articles we aim to describe the producer and consumer aspect of a topic. However in many cases we mainly describe the producer aspect. 
On this page we provide some specific consumer guidance and reference a number of articles which have explicit sections on the consumer aspect of discovering, accessing and assimilating Soil Data.

Consuming and assimilating soil data has various challenges. 

- Discovery of data; not much data is advertised and on various platforms. In many cases the search engine is the only option to find something. In other cases there's too much similar results, which prevent you from easily finding the gems between the others. 
- Soil data is quite complex by nature; samples representing a soil horizon at a location, being analysed in some laboratory. These observations are used to derive a region sharing a common value, being vizualised on a map. Capturing this complexity in a standardised model has been quite a challenge. It resulted in [iso28258](https://www.iso.org/standard/44595.html) and derived models such as GLOSIS and the INSPIRE model. The process of adopting the standard requires expert skills, consider that working with this type of models is challenging, especcially in generic GIS tools such as QGIS/ArcGIS. It resulted in the fact that many organisations haven't fully adopted these models and provide the data in a local model or that the harmonised data is a subset of the original dataset. As a consumer it is hard to assess the level of implementation of the standard. 
- INSPIRE identified WFS as a relevant exchange mechanism for rich data such as soil data. However the industry has not moved with those efforts and hardly any tooling exists which is able to use WFS to explore the richness of WFS's providing rich (soil) data.
- As part of the harmonisation it is important to adopt common code lists, or, for unique concepts, extend existing code lists in a proper way. Many implementations have failed to follow this principle, resulting in a multitude of code list references which are redundant or under documented. In many cases a consumer will need to harmonize code list values as part of using the data.

Below some guidance on how to face some of these challenges:

## Discovery of data

Some guidance on locating soil data sources.

- Datasets of soil institutes of EU memberstates are typically found on the [INSPIRE GeoPortal](https://inspire-geoportal.ec.europa.eu/overview.html?view=themeOverview&theme=so), filter by theme 'soil'.
- ESDAC hosts a series of [pan European datasets](https://esdac.jrc.ec.europa.eu/resource-type/datasets), including the [Lucas database](https://esdac.jrc.ec.europa.eu/projects/lucas).
- Datasets of academic projects can best be located at [OpenAire Explore](https://explore.openaire.eu/search/find?instancetypename=%22Dataset%22&active=result&fv0=soil&f0=q&page=1)
- ISRIC - World soil information hosts a discovery service including some 200 datasets at [https://data.isric.org](https://data.isric.org), and a separate collection of [external soil resources](https://www.isric.org/index.php/explore/soil-geographic-databases) 
- Global Soil Partnership provides a number of [global soil maps](https://www.fao.org/global-soil-partnership/areas-of-work/soil-information-and-data/en/)
- FAO provides a number of legecay soil maps and databases in the [Soils portal](https://www.fao.org/soils-portal/data-hub/soil-maps-and-databases/en/)
- Google provides a [dedicated dataset search](https://datasetsearch.research.google.com/search?query=soil) option, operating on [structured data](https://developers.google.com/search/docs/appearance/structured-data/dataset) of websites


## Multiple source data models

INSPIRE provides guidance on how to [indicate in metadata the level of harmonisation of the service](https://github.com/INSPIRE-MIF/technical-guidelines/blob/2022.2/metadata/metadata-iso19139/metadata-iso19139.adoc#4332-category-of-the-spatial-data-service). Unfortunately this feature is not broadly adopted yet, many data providers provide non harmonised data without indicating it as such.

In case data is provided using the INSPIRE Soil Model you can use [Hale Studio to import gml](tools/hale-studio-consume-gml.md) to an alternative datamodel.

Because INSPIRE Soil is based on the [Observations and Measurements](https://www.ogc.org/standards/om) model, it is possible to publish soil data using a [SOS](https://www.ogc.org/standards/sos) or [SensorThings API](https://www.ogc.org/standards/sensorthings). At this moment there are no known implementations of SOS or STA to provide Soil data. I'm not aware of procedures to download full datasets from a sensor service and transform them, but the use case may not be relevant to sensor data. A multitude of sensor clients is available to interact directly with Sensor Services, providing the options to filter the results and extract only the relevant data for a use case.

## Importing WFS data

Because a single INSPIRE Soil dataset consists of multiple featuretypes (plots, profiles, horizons, observations, ...) requesting the full dataset is not possible using a single WFS GetFeature request. Instead a client should consult the service capabilities to evaluate which featuretypes are available and iterate over each of them.

INSPIRE mandates the availability of a stored query on any WFS which is able to download the full dataset in a single request. This is by far the easiest option to download a dataset. However consider that this option is not provided by some providers.

The [GDAL utility](utils/gdal.md) supports [GMLAS](https://gdal.org/drivers/vector/gmlas.html) (GML Application Schema) and is able to import gml from WFS and store it in for example a database. See the [GDAL recipe](utils/gdal.md) for an example. Hale studio is also able to import data from a WFS.

!!! note

    If you open a rich INSPIRE WFS service in QGIS, you will get unexpected results. In some cases QGIS will be able to extract a geometry and display the layer, but in other cases it will open each featuretype as a table (without geometry). Links between layers and tables are not imported. A [GMLAS plugin](https://plugins.qgis.org/plugins/gml_application_schema_toolbox/) (based on the GDAL GMLAS functionality) has been developed in 2016, but adoption has been low and the project seems currently unmaintained.

## Code list mappings

Common codelists are stored on the INSPIRE registry, or common registries such as Agrovoc or Gemet. If a value needs to be registered which is not in those registries, organisations have the option to publish an extended version of the code list locally. Code list values are typically provided as a URI referencing the concept in a registry. 

The soil model has various codelists. Important codelists are the (WRB) soil clasification, the observed soil property and the methods used in the laboratory. It is important to understand if the method used impacts the measured value. For example there are various methods to measure pH, which each give a specific value. Pedo transfer functions exist to recalculate the value based on the method used. Prior to applying a pedo transfer function, you have to map the codelist from the remote dataset to the code list used locally. Some cases can exist:
 
- Both datasets reference the same value from a common code list
- The dataset references a remote concept, but it matches with a concept used locally
- The dataset references a remote concept, but no local representation exists for the concept 
- No information exists on the concept used

Option 1 is optimal, option 2 requires a mapping table (a suggestion can be made to include the concept on the inspire registry), option 3 and 4 are problematic, these cases should be decided on case by case. Hale studio includes an option to provide mapping tables to [map codelist values](). 