# Download and process satellite Image Data

In this repo you will find a couple of scripts to download and process satellite images to train GANs.

These scripts were used to create [MarsGAN](https://medium.com/@ivalengy/synthetic-images-of-mars-using-the-stylegan-e4144901bdde).

You can follow for example [this tutorial](https://ivanlen.github.io/how-to-generate-synthetic-mars-images/) to train your own MarsGAN-stylegan with HiRISE data.

Also some of these scripts were used to create the dataset of [Neural Planets](https://neuralplanets.com).

## Download Data
### Download HiRISE data
>The High Resolution Imaging Experiment is known as HIRISE. The big and powerful HIRISE camera takes pictures that cover vast areas of Martian terrain while being able to see features as small as a kitchen table.

To scrap HiRISE images you can use the notebook [download_hirise_images.ipynb](./download_hirise_images.ipynb).

In this notebook you scan the ftp server of HiRISE and download the images which filename that include the pattern `_RGB.NOMAP.browse.jpg`. These are the images that we are insterested in.

### Download Landsat 8 data
>Landsat 8 orbits the the Earth in a sun-synchronous, near-polar orbit, at an altitude of 705 km (438 mi), inclined at 98.2 degrees, and completes one Earth orbit every 99 minutes.  The satellite has a 16-day repeat cycle with an equatorial crossing time: 10:00 a.m. +/- 15 minutes.  
>
>Landsat 8 acquires about 740 scenes a day on the Worldwide Reference System-2 (WRS-2) path/row system, with a swath overlap (or sidelap) varying from 7 percent at the equator to a maximum of approximately 85 percent at extreme latitudes. A Landsat 8 scene size is 185 km x 180 km (114 mi x 112 mi).

To download images from Landsat 8 data there are a couple of steps that you should follow.

###### Use BigQuery to download a csv
Modify `long` and `lats` to target a specific area. Use `cloud_cover <= 60` to avoid images covered with clouds.

```SELECT
sensor_id, wrs_path, wrs_row, scene_id, product_id,
sensor_id, north_lat, south_lat, east_lon, west_lon,
base_url, cloud_cover, date_acquired
FROM `bigquery-public-data.cloud_storage_geo_index.landsat_index`
WHERE sensor_id='OLI_TIRS'
AND spacecraft_id='LANDSAT_8'
AND west_lon >= -58.3
AND east_lon < -18.25
AND south_lat >= 9.5
AND north_lat < 45.8
AND date_acquired >= DATE('2018-01-01')
AND date_acquired <= DATE('2020-01-01')
AND cloud_cover <= 60
LIMIT 1000
````

#### Process the csv
To process the csv files and download the images use the notebook [proc_images_from_earth_engine_read_csv](./proc_images_from_earth_engine_read_csv.ipynb)


## Process & augment images.

Once the images are downloaded use the notebook [crop_images_and_augment.ipynb](./crop_images_and_augment.ipynb) to crop and augment the images.

Once to crop the images they are ready to be used for the training! yeaaiihh! :)
