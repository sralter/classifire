# brainstation_2023_ds_capstone
_Samuel Alter's BrainStation 2023 Data Science Capstone Project, Spring 2023_

## Motivation
* Wildfires, or the uncontrolled burning of vegetation, cause enormous damage and loss of life worldwide every year.[^1]
* **33 people died** and **>4.3 million acres burned** in California in 2020 alone. This is **equivalent to the total combined area of Puerto Rico and Rhode Island**.
* Although they are a natural component of some forest ecosystems, **wildfire incidents are projected to increase** in our warming world.[^2]
* While society transitions to a greener future, **there is a clear need to predict where wildfires are likely to occur** so that communities can protect themselves and evacuate when necessary.

## Introduction
* For this project, I collected a combination of **satellite imagery**, **topographic data** (aspect, elevation, and slope), and **historical fire boundaries** to train models that would predict wildfire risk (Figure 1). 
* I first used [QGIS](https://qgis.org/en/site/), an open-source mapping application, to setup the relationships between the datasets.
* Then used **Python** to process and **model the data**.
* I fed the processed data into machine learning and deep learning models to make my predictions.
* I chose to focus on the Santa Monica Mountains in Ventura and Los Angeles Counties, for its relatively manageable size, proximity to populated areas, and its east-west trend, which makes the topographic analysis easier.

<img width="696" alt="Figure 1: Data ingestion pipeline" src="https://github.com/sralter/brainstation_2023_ds_capstone/assets/25013680/03704211-5734-49b8-bb13-8cc88e2a8d65">

## Datasets Used


## References
[^1]: [https://www.fire.ca.gov/incidents/2020/]
[^2]: [https://report.ipcc.ch/ar6syr/pdf/IPCC_AR6_SYR_SPM.pdf]
