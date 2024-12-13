# Sealing Monitor

[CORRECTIV](https://correctiv.org/) and [Vertical52](https://vertical52.org/en) conducted the most detailed satellite-based investigation of soil sealing in German cities to date. Using computer vision and satellite imagery from Sentinel-2 and Planet satellites, we achieved unprecedented accuracy with a resolution down to 3 meters per pixel - a level of detail previously unavailable for such analyses. We also examined how Hamburg, Leipzig, and Stuttgart have developed between 2018 and 2024 regarding sealed surfaces. We analyzed both the extent of sealed surfaces and their correlation with urban heat islands, revealing that heavily sealed districts can be up to seven degrees warmer than other areas.

The investigation showed concerning trends: Despite official commitments to climate adaptation, sealed surfaces increased in all three cities. Hamburg saw the most dramatic change with 14 square kilometers of new sealed surfaces - an area five times the size of the St. Pauli district. Leipzig lost eight square kilometers of green space, equivalent to 1,120 football fields, while Stuttgart showed mixed results with both sealing and unsealing effort. The full results can be read in our October 2024 article [Zwischen Asphalt und Beton](https://correctiv.org/aktuelles/2024/10/01/zwischen-asphalt-und-beton-versiegelung-deutscher-staedte-nimmt-zu/) (DE) or in the Vertical52 [report](https://docs.google.com/document/d/1aqqOnTrgCGjkXMI6rsIpteRZq0KeZCHmUZQ-O5kykA8/), which also includes the full methodology for the classification process.

## Methodology

### Sealing

The basis of this investigation are images from Sentinel 2 (10 meter resolution) in 2024 and 2018 as well as from Planet Labs (3 meter resolution) in 2024. Vertical52 then classified these images with a 90% accuracy into the following classes:

|Classification|Meaning|
|--------------|-------|
|0|Water|
|1|Impervious (sealed)|
|2|Barren|
|3|Green|

Using the classified output as a basis, the total area and percent of sealed land per district and overall was then calculated using the R `terra` package. Since bodies of water have a large impact on both the temperature and infiltration, we included land classified as water in the total area while calculating the percent imperviousness.

The classified satellite imagery is available for download as GeoTIFF files at this [link](https://cloud.correctiv.net/index.php/s/ed5r282wDp4K2Pe).

### Heat stress

To better illustrate the impact of soil sealing on heat stress, we also calculated the relative surface temperature of each district using data from the U.S. Geological Survey. With resampling, this data had a resolution of 30 meters.

In discussion with Professor [Tobias Krüger](https://www.ioer.de/institut/beschaeftigte/krueger) from the Leibniz-Institut für ökologische Raumentwicklung, we decided on the following selection criteria for choosing satellite images from the [Landsat Collection 2 Level 2](https://www.usgs.gov/landsat-missions/landsat-collection-2-level-2-science-products) dataset:

- Images were taken between May and September (Summer months plus 1 month buffer on each end)
- Low cloud cover (filter for < 30% cloud cover in Earth Explorer and then manually inspect for clouds within city borders)
- A temperature of 25 Celsius (defined as a „Sommertag“ by the Deutsche Wetter Dienst) or higher on the day a photo was taken or the day before

We considered the temperature on the day before an image was taken because heat is often stored in the ground overnight, even more so when the ground is sealed. Ideally we would have limited satellite images to just those taken on „Hitzetage“ (30 Celsius or more), but there would not have been enough images to build a sample selection of at least three. Similarly, we also included images from 2023 in order to have a sample size of at least three images for each city. A documentation of the images used to calculate relative surface temperature and the dates on which they were taken can be found in [landsat_dates.csv](landsat_dates.csv).

In order to calculate the average relative surface temperature, we first calculated the average surface temperature of each district using the `terra` package in R. We then calculated the average surface temperature for the entire city and subtracted that from the district temperatures. This left us with the average relative temperature, which signals which areas tend to be hotter or cooler.

It's important to note that the Landsat Level 2 data is enriched with auxiliary data sources. Some of these sources were missing for parts of Stuttgart. Affected districts have an NA value for the relative surface temperature.

### Data dictionary

The result is the dataset [sealing_by_city_district.csv](sealing_by_city_district.csv), which includes the following information:

|Variable|Definition|
|--------|----------|
|city|City (Stadt)|
|district|District (Stadtteil)|
|area_km|Area of district in km2|
|sentinel_2018_perc|Percent sealing 2018 based on Sentinel (10m)|
|sentinel_2024_perc|Percent sealing 2024 based on Sentinel (10m)|
|sentinel_net_change_perc|Net change in sealing based on Sentinel (10m)|
|planet_2024_perc|Percent sealing 2024 based on Planet (3m)|
|method_diff_perc|Difference in sealing between Sentinel and Planet|
|sentinel_2018_area|Sealed area (km2) 2018 based on Sentinel (10m)|
|sentinel_2024_area|Sealed area (km2) 2024 based on Sentinel (10m)|
|sentinel_net_change_area|Net change in sealed area (km2) based on Sentinel (10m)|
|planet_2024_area|Sealed area (km2) 2024 based on Planet (3m)|
|method_diff_area|Difference in sealed area (km2) between Sentinel and Planet|
|mean_relative_lst_2023_2024|Average relative temperature in Celsius in 2023 and 2024|

## Attribution

This data can be used for further research. When using the data, cite CORRECTIV and Vertical52 as the data sources and link to this GitHub page.

We obtained vector data for the district boundaries from the following local authorities:

|City|Data Source|
|----|-----------|
|Hamburg|[Landesbetrieb Geoinformation und Vermessung (LGV) Hamburg](https://api.hamburg.de/datasets/v1/verwaltungsgrenzen/collections/stadtteile)|
|Leipzig|[Stadt Leipzig](https://www.leipzig.de/buergerservice-und-verwaltung/unsere-stadt/gebietsgliederung-und-strassennamen/kommunale-gebietsgliederung#c123748)|
|Stuttgart|[Stadtmessungsamt Stuttgart](https://opendata.stuttgart.de/dataset/kleinraumige-gliederung)|
