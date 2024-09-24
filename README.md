# Classi**FIRE**
_Samuel Alter's BrainStation 2023 Data Science Capstone Project, Spring 2023_

## Motivation
- Wildfires, or the uncontrolled burning of vegetation, cause enormous damage and loss of life worldwide every year.[^1]
- **33 people died** and **>4.3 million acres burned** in California in 2020 alone. This is **equivalent to the total combined area of Puerto Rico and Rhode Island**.
- Although they are a natural component of some forest ecosystems, **wildfire incidents are projected to increase** in our warming world.[^2]
- While society transitions to a greener future, **there is a clear need to predict where wildfires are likely to occur** so that communities can protect themselves and evacuate when necessary.

## Introduction
- For this project, I collected a combination of **satellite imagery**, **topographic data** (aspect, elevation, and slope), and **historical fire boundaries** to train models that would predict wildfire risk (**Figure 1**). 
- I first used [QGIS](https://qgis.org/en/site/), an open-source mapping application, to setup the relationships between the datasets.
- Then used **Python** to process and **model the data**.
- I fed the processed data into machine learning and deep learning models to make my predictions.
- I chose to focus on the **Santa Monica Mountains in Ventura and Los Angeles Counties**, for its relatively manageable **size**, **proximity to populated areas**, and its **east-west trend**, which makes the topographic analysis easier.

<img width="696" alt="Figure 1: Data ingestion pipeline" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/03704211-5734-49b8-bb13-8cc88e2a8d65">

## Datasets Used
- **Satellite imagery** and **topographic data** from USGS’ EarthExplorer[^3]
- Historical records of **wildfire burn boundaries** from the National Interagency Fire Center[^4]
- File formats used: .tif and .jpg (imagery); .geojson and .csv (historical wildfire data).

## Mapping, Data Cleaning, and Preprocessing
- In order to train a model that integrates spatial information with historical fire boundaries, I had to find a way to quantify the continuous spatial information. I settled on **building an evenly-spaced points layer** that would have the topographic and fire boundary data from under that point appended to the file (**Figure 2**).
- Since the satellite photos were from 2018, I removed all fire polygons from after 2018 and **merged the rest to one shape**.
- Using the point layer, **an overlapping function could determine if a part of the landscape experienced a fire (“fire” area) or not (“nofire” area)**. The resultant file would serve as the “topographic” data in the project.
- For the geographic data, I focused on **aspect** (i.e. what direction the land is facing), **elevation** (meters above sea level), and **slope** (degrees). It is a straightforward operation to extract these data from an elevation raster and append them to the point layer.
- I **tiled the satellite imagery into 128x128 pixels** that would be fed into a **Tensorflow image model**, and saved them into two folders (i.e., fire and nofire).
- At the end of the data collection step, I had a **table of geographic data** and **two image folders** (fire areas and non-fire areas).

<img width="735" alt="Figure 2: Mapping flow from QGIS to modeling" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/1c198858-da48-47c0-9860-ea417e3471bd">

## Modeling, Results, and Insights
- Since data is in two forms (point-based topographic information and satellite image tiles), I trained two models. After finding an optimal model, I created a metamodel, which used the predictions from the topographic and image analysis models as factors for a new logistic regression (**Figure 3**).

<img width="704" alt="Figure 3: Modeling pipelines using statsmodels, sklearn, Tensorflow (VGG19), and finally a metamodel with sklearn's logistic regression." src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/504c806a-4aeb-42bf-b566-9c3253ee4bba">

  - _Topographic Data_  
    - The **areas affected by fire** were predominantly **located in mountainous regions with higher mean elevation** compared to fire-free areas (**Table 1**).
    - Correlations were observed between different topographic factors, such as categorical aspect with continuous aspect, and categorical slope and elevation with continuous elevation and slope.
    - `LogisticRegression` **was chosen as the modeling approach** for classifying fire incidence based on topographic data, revealing the relative importance of different features.
    - Various models were tested, including `naive_bayes`, `Bernoulli` and `Gauss`, `XGBClassifier`, `RandomForestClassifier`, `AdaBoost`, and `GradientBoostingClassifier`, with `GradientBoostingClassifier` **achieving the highest accuracy**.
    - Grid search was employed to optimize the GradientBoostingClassifier model, but the best accuracy remained the same with specific parameter values.

<img width="696" alt="Table 1: Summary statistics by fire/nofire areas" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/918a9941-a27b-4c09-8536-cb4b0ddac3e4">

  -  _Satellite Imagery_  
     -  BigEarthNet[^5] suggested the **pre-trained VGG19 model** to be sufficient, so I used that with **Tensorflow-Keras** and **added a final dense layer with two output nodes** to represent the fire/nofire categories required by this project.
     -  With **20,025,410 total parameters**, I deemed the model more than sufficient for the project’s needs.
     -  After training, **the model achieved an accuracy of 95.6%** on classifying all the images.
     -  **Figure 4** shows typical images in the set as well as the locations of the two areas (fire and non-fire) that I used to feed the models.

<img width="623" alt="Figure 4: examples of fire and nofire satellite images" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/ed8122e2-a2be-4545-bee6-56b5a3ba570a">
<img width="1303" alt="Figure 3: Map of the sections within the study area used to feed the models" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/d1121556-5d5f-4fbf-a64e-065752f546f7">

  -  _Metamodel_
     -  To construct the metamodel, I **extracted the predictions from the topographic and imagery datasets, used them as features**, and ran the two through a **scikit-learn `LogisticRegression`, which achieved over 99% accuracy**. This concluded the modeling portion of the project.

## Findings and Conclusions
- The statsmodels `Logit` model reveals that **higher elevations and west- and north-facing hillslopes correlate with wildfires, potentially due to dominant wind directions in the area**.
- The project serves as a **proof-of-concept**, demonstrating the capabilities of a machine-learning model using remotely-sensed data for predicting wildfire risk. **Future iterations can expand by incorporating weather and time series analysis** and **testing the model on different landscapes**.
- Despite limitations, such as lack of high-resolution weather data, **the project shows promise for developing a robust wildfire risk prediction model** with broader data and automated GIS workflows.
- **Publishing the model in a more accessible manner** could provide communities with **valuable insights into their wildfire risk**.

## Postscript: Commentary on why the accuracy is so high
* Accuracy is almost 100%, which is suspiciously high
* Since the model was trained on either 100% burned areas or 100% unburned areas, it only knows the clearly-delineated cases.
* Furthermore, when tested, the model was only given 100% burned or unburned areas
* The unburned area was a city center. There would never be a wildfire in a concrete jungle
* The opposite is similarly always true: in the mountains, away from all civilization, fires are extremely likely and are much harder to combat

## Extra goodies
- Correlation matrix of the geographic data:  
<img width="727" alt="Correlation matrix of the geographic data" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/d88c52a4-bb47-42c6-a990-91650ba1fa95">

- Summary of models and results for TopoData:  
<img width="1255" alt="Summary of models and results for TopoData" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/ee414012-b4b3-4a66-81f0-be9cbf02e35e">

- Summary of satellite imagery model:  
<img width="726" alt="Summary of satellite imagery model" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/11557b3f-2952-4ae1-95cc-4ca9d158ed11">

- Summary of metamodel:  
<img width="726" alt="Summary of metamodel" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/fb34dc97-acd4-4e0a-a8e7-891363b49f2e">

- Satellite image and historic wildfire boundaries (up to 2018):  
<img width="1319" alt="Satellite image and historic wildfire boundaries (up to 2018)" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/3dce0beb-fbaf-4b8a-abad-da2250486b8c">

- Satellite image and hillshade of study area:  
<img width="1320" alt="Satellite image and hillshade of study area" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/b6c75336-f339-4709-8f26-a580dd9b6498">

[^1]: [https://www.fire.ca.gov/incidents/2020/]
[^2]: [https://report.ipcc.ch/ar6syr/pdf/IPCC_AR6_SYR_SPM.pdf]
[^3]: [https://earthexplorer.usgs.gov]
[^4]: [https://data-nifc.opendata.arcgis.com/]
[^5]: [https://bigearth.net/]
