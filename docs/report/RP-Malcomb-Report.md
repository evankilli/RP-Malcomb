---
layout: page
title: RP- Vulnerability modeling for sub-Saharan Africa
---


**Replication of**
# Vulnerability modeling for sub-Saharan Africa

Original study *by* Malcomb, D. W., E. A. Weaver, and A. R. Krakowka. 2014. Vulnerability modeling for sub-Saharan Africa: An operationalized approach in Malawi. *Applied Geography* 48:17–30. DOI:[10.1016/j.apgeog.2014.01.004](https://doi.org/10.1016/j.apgeog.2014.01.004)

Replication Authors:
Evan Killion, Joseph Holler, Kufre Udoh, Open Source GIScience students of fall 2019 and Spring 2021

Replication Materials Available at: [github repository name](github repository link)

Created: `DD Month YYYY`
Revised: `DD Month YYYY`

## Abstract

The original study is a multi-criteria analysis of vulnerability to Climate Change in Malawi, and is one of the earliest sub-national geographic models of climate change vulnerability for an African country. The study aims to be replicable, and had 40 citations in Google Scholar as of April 8, 2021.

## Original Study Information

The study region is the country of Malawi. The spatial support of input data includes DHS survey points, Traditional Authority boundaries, and raster grids of flood risk (0.833 degree resolution) and drought exposure (0.416 degree resolution).

The original study was published without data or code, but has detailed narrative description of the methodology. The methods used are feasible for undergraduate students to implement following completion of one introductory GIS course. The study states that its data is available for replication in 23 African countries.


### Data Description and Variables

Briefly, the data used for this analysis and the original study came from three sources: USAID's Demographic and Health Survey (DHS), the Famine Early Warning Network (FEWSNET), and the UN Environmental Program (UNEP). All data points were transformed in the original study and our replication to 1-5 scores using the formula (percentage rank) * 4 + 1, with the percentage rank descending or ascending based on . Additionally, a geopackage of traditional authorities - an administrative division in Malawi in between the village level and the high-level, low-resolution district level. The original authors chose this as the level of analysis due to their perception of it as the most useful level for analysis - districts were much to large, there being only 28 for the entire country, whereas villages were less equipped than traditional authorities or districts for later planning to mitigate climate change effects.

The DHS was the largest source of data, providing household level information related to a number of health and socio-economic indicators, including number of children in households, numbers of various kinds of livestock, and access to electricity. This data broadly allowed for the quantifying of the adaptive capacity of households and traditional authorities. This data was broken up into two major sets, "Assets" and "Access." Assets were those indicators, like number of livestock, which quantified the basic economic wellbeing of households, while Access was those indicators quantifying access to resources like electricity, water, etc.. Within the Access set, the authors also included the sex of the head of each household, which may be used as a proxy for access to social capital and other similar resources. This data was then aggregated within the boundaries of each traditional authority and an adaptive capacity score was calculated based on the 1-5 scores created for the DHS data. This was multiplied by 20 in the reproduction to make the range of values produced match the scale of the range of values in the original study. The DHS data was also used without aggregating to the traditional authority level for use in creating the final resilience score alongside the other data, including the scaling to scores of 1-5 and weighting, but without the multiplication by 20.

The FEWSNET data provided a large set of statistics related to livelihood sensitivity. This was calculated at a fairly high level, with Malawi broken up into 19 zones. These zones were created by FEWSNET based on areas that share common farming practices, labor patterns, and environmental coping strategies. This data did need to be pre-processed for analysis, working - in collaboration with [Maddie Tango]("MADDIES WEBSITE"), [Steven Montilla]("STEVENS WEBSITE"), [Jackson Mumper]("JACKSONS WEBSITE"), [ARIELLE LANDAU]("ARIELLES WEBSITE"), and [Sanjana Roy]("SANJANAS WEBSITE") - to classify what figures were drawn upon to calculate the percentage of households' food that came from their own farms, percent of income from wage labor, percentage of labor vulnerable to market shocks, and "ecological destruction associated with livelihood coping strategies," and eventually to manually calculate all four of these figures for each livelihood zone.

Finally, UNEP's data provided two rasters of 1) flood risk and 2) drought risk. No pre-processing needed to be done to this; it was used in its weighted quintile form and added to rasterized versions of the corresponding weighted FEWSNET and DHS data (created during the course of analysis) to create the final vulnerability map.

- sources of each data layer and
- the variable(s) used from each data source
- transformations applied to the variables (e.g. rescaling variables, calculating derived variables, aggregating to different geographic units, etc.)

### Analytical Specification

The original study was conducted using ArcGIS and STATA, but does not state which versions of these software were used.
The replication study will use R.

## Materials and Procedure

The first procedure below was created without accessing the data for this study first. The second was created after investigating the data and its sources.

### Data
2004 - 2010 DHS w/ GPS
Demographic and health survey (assets & access)
UNEP/grid Europe (biophysical exposure)
Famine early warning network (livelihood)

### Procedure 1
#### Step 1: Preprocessing of Geographic Boundaries
2004-2010 DHS data points (for each village surveyed): District → ***disaggregated*** → villages → ***disaggregated*** → traditional authorities

DHS Households table (1 row/house) → ***field calc*** → conversion to 0-5 scale → weighted A/C score → ***join by attribute*** w/ DHS data points (village level) → ***spatial join AND group*** w/ traditional authorities (GADM adm_2) → traditional authorities w/ Capacity Score → ***Raster***

Livelihood zones → ***copy #s from spreadsheet*** →***rescale 0-5*** →  ***Rasterize*** → ***Raster Calc*** (w/ Drought Exposure and Flood Risk)

Drought exposure → ***rescale 0-5***

Flood risk → ***rescale 0-5***

#### Step 2: Weighting
Data Input: UNEP/grid Europe, Famine early warning network → ***Raster*** → ***Weight values***: All vulnerability measures were weighted (table 2) and normalized between 0 & 5 (RStudio)

#### Step 3: Creating the Model of Vulnerability
***Calculate***: Household resilience = adaptive capacity + livelihood sensitivity - biophysical exposure

### Procedure 2
1. Data Preprocessing:
  1. Download traditional authorities: MWI_adm2.shp
1. Adding TA and LZ ids to DHS clusters
1. Removing HH entries with invalid or unknown values
1. Aggregating HH data to DHA clusters, and then joining to traditional authorities to get: ta_capacity_2010
1. Removing index and livestock values that were NA
1. Sum of Livestock by HH
1. Scale adaptive capacity fields (from DHS data) on scale of 1 - 5 to match Malcomb et al.
1. Weight capacity based on table 2 in Malcomb et al.
  1. Calculate capacity by summing all weighted capacity fields
1. Summarize capacity from households to traditional authorities
1. Joining mean capacities to TA polygon layer
1. Making capacity score resemble Malcomb et al's work (scores on range of 0-20) by multiplying capacity score by 20
1. Categorizing capacities using natural jenks methods
1. Creating blank raster and setting extent of Malawi - CRS: 4326
1. Reproject, clip and resampling flood risk and drought exposure rasters to new extent and cell size
  1. Uses bilinear resampling for drought to average continuous population exposure values
  1. Uses nearest neighbor resampling for flood risk to preserve integer values
  1. Removing factors and recasting them as integers
  1. Clipping TAs with LZs to remove lake
  1. Rasterizing final TA capacity layer
1. Masking flood and drought layers
1. Reclassify drought raster into quantiles
1. Add all RASTERs together to calculate final output:  final = (40 - geo) * 0.40 + drought * 0.20 + flood * 0.20 + livelihood sensitivity * 20
1. Using zonal statistics to aggregate raster to TA geometry for final calculation of vulnerability in each traditional authority


## Replication Results

*UNDERCOUNTING RESILIENCE*

*UNDERCOUNTING VULNERABILITY*

For each output from the original study (mainly figure 4 and figure 5), present separately the results of the replication attempt.

2.	State whether the original study was or was not supported by the replication
3.	State whether any hypothesis linked to a planned deviation from the original study was supported. Provide key statistics and related reasoning.

Figures to Include:
- map of resilience by traditional authority in 2010, analagous to figure 4 of the original study
- map of vulnerability in Malawi, analagous to figure 5 of the original study
- map of difference between your figure 4 and the original figure 4
- map of difference between your figure 5 and the original figure 5

## Unplanned Deviations from the Protocol

*Summarize changes and uncertainties between*
- your interpretation and plan for the workflow based on reading the paper
- your final workflow after accessing the data and code and completing the code

- rescaling 0-5 then 1-5 --> unclear (quintiles, but also 0-5, but thats 6 classes)
- multiplying capacity by 20 to match original map scale --> why tho (note this was given initially)
- livelihood senitivity --> had to decide what data went into calculating 4 scores, paper wasnt clear


## Discussion

Provide a summary and interpretation of the key findings of the replication *vis-a-vis* the original study results. If the attempt was a failure, discuss possible causes of the failure. In this replication, any failure is probably due to practical causes, which may include:
- lack of data
- lack of code
- lack of details in the original analysis
- uncertainties due to manner in which data has been used




## Conclusion

Restate the key findings and discuss their broader societal implications or contributions to theory.
Do the research findings suggest a need for any future research?

## References

Include any referenced studies or materials in the [AAG Style of author-date referencing](https://www.tandf.co.uk//journals/authors/style/reference/tf_USChicagoB.pdf).

####  Report Template References & License

This template was developed by Peter Kedron and Joseph Holler with funding support from HEGS-2049837. This template is an adaptation of the ReScience Article Template Developed by N.P Rougier, released under a GPL version 3 license and available here: https://github.com/ReScience/template. Copyright © Nicolas Rougier and coauthors. It also draws inspiration from the pre-registration protocol of the Open Science Framework and the replication studies of Camerer et al. (2016, 2018). See https://osf.io/pfdyw/ and https://osf.io/bzm54/

Camerer, C. F., A. Dreber, E. Forsell, T.-H. Ho, J. Huber, M. Johannesson, M. Kirchler, J. Almenberg, A. Altmejd, T. Chan, E. Heikensten, F. Holzmeister, T. Imai, S. Isaksson, G. Nave, T. Pfeiffer, M. Razen, and H. Wu. 2016. Evaluating replicability of laboratory experiments in economics. Science 351 (6280):1433–1436. https://www.sciencemag.org/lookup/doi/10.1126/science.aaf0918.

Camerer, C. F., A. Dreber, F. Holzmeister, T.-H. Ho, J. Huber, M. Johannesson, M. Kirchler, G. Nave, B. A. Nosek, T. Pfeiffer, A. Altmejd, N. Buttrick, T. Chan, Y. Chen, E. Forsell, A. Gampa, E. Heikensten, L. Hummer, T. Imai, S. Isaksson, D. Manfredi, J. Rose, E.-J. Wagenmakers, and H. Wu. 2018. Evaluating the replicability of social science experiments in Nature and Science between 2010 and 2015. Nature Human Behaviour 2 (9):637–644. http://www.nature.com/articles/s41562-018-0399-z.
