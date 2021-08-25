# Google Earth Engine - Cyclone Idai Flooding

## Methdology

This exercise consisted of the following methodology, adapted from the advice from [UN-SPYDER](https://un-spider.org/advisory-support/recommended-practices/recommended-practice-google-earth-engine-flood-mapping/step-by-step#Step%202:%20Time%20frame%20and%20sensor%20parameters%20selection):

#### Preparation:
- Brief research to determine initial methodology for modelling flood mapping
- Evaluate data sources; choose on appropriateness and accessibility

#### SAR flood analysis
- Select area of interest.  In this case a country: Mozambique
- Import Coperincus Sentinel-1
- Filter SAR data using area of interest and image metadata: (e.g polarization of VV, , usual for flood mapping)
- Using date range, create mosaics of SAR for both before and after flood events based on date.  Reduce based on median values.
- Speckle removal of mosaics using mean result from circular kernel
- Create difference image from before and after mosaics.
- Create binary image from difference image using suggested threshold of 1.25 (this could be experimented with further)
- Convert binary image to mask by masking itself
- Filter binary flood mask on high slope to remove unlikely inundations
- Filter binary flood mask on areas of low connectivity – probably noise.

#### Determine affected population:
- Import a population count raster: WorldPop
- Create a mosaic from the ImageCollection after sorting by most recent year.
- Convert flood mask to vector
- Mask population mosaic with flood vector
- Conduct summary statistics on masked population mosaic
- Print total affected population

#### Visualise results
- Set up appropriate map in Jupyter Notebook: in this case Folium
- Choose colour palettes for each layer.
- Give layers human friendly name
- Add each layer to the interactive map


## Overview and future work

The methodology used centres around a change detection in Sentinel-1 SAR data and is adapted from this advice from [UN-SPYDER](https://un-spider.org/advisory-support/recommended-practices/recommended-practice-google-earth-engine-flood-mapping/step-by-step#Step%202:%20Time%20frame%20and%20sensor%20parameters%20selection).  Given the low values SAR response values from water, the idea is that by selecting images from an appropriate date range, change detection will yield significant difference between the month prior and during the floods of Cyclone Idai.  The dates were chosen to be as close as possible to before and during the flood events.  

Several methods were employed to remove noise from the images.  The first was pre-change detection.  This involved a smoothing operation using a circular kernel.  Post change detection, connectivity was used to filter out isolated pixels from the binary flood image.  DEM was also used to remove areas of high slope which were unlikely to have standing water post flood surge.   

One challenges faced was the Sentinel-1 coverage of Mozambique.  Using the ‘ascending’ orbit direction did not yield full coverage for the entire country.  Though not investigated in detail, it is surprising given a full three weeks within the window.  By exploring other parameters and changing the orbit direction to ‘descending’ I was able to find full coverage.   

Another challenge I experienced was working with strings in Google Earth Engine.  Having GEE string objects as server side, it took a bit of experimenting and reading the documentation to understand the best way of parsing them.  Most of the documentation seems to assume the code will be run in the GEE console, so understanding the server side objects vs client side was crucial.  

For future work there are several things I would like to explore.  Firstly, I would like to investigate alternative means beyond change detection.  Thresholding a simple image is possible using a histogram to determine the threshold per image.  Given the scope of the project I did not have enough time to determine how to do this on a per image basis withing the SAR Image Collection, though I am confident it would not be too much work.  It would be incredibly interesting to compare the results of these two methodologies.  

In regards with datasets, and the appropriateness, one thing I would like to explore is using an alternative WorldPop dataset.  The WorldPop dataset used here was unconstrained, however WorldPop also publishes a dataset with population counts constrained to buildings and other signs of habitation.  I believe this may yield more accurate results of the affected population especially at riparian and rural areas.  

Finally, I think it is important to note that inundation mapping with remote sensing data can be misleading to interpreters.  Given the six day pass of the Sentinel-1 data, the chances of capturing the peak flood heights are low.  How can I convey this uncertainty? At one extreme, an option would be to explore combining flood risk modelling from products such as HEC-GeoRAS; however, this would obviously be outside Google Earth Engine.  Another option is to explore incorporating other change detection from multispectral which could pick up changes such as sediment deposits or other significant change.   

