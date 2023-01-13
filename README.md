# Mission·E
#### High level Overview
This README outlines the Mission·E platform – an online platform that was developed with the goal of reducing fertilizer-based greenhouse gas emissions. We have developed a platform designed to be used by both farms and those who work within the agricultural industry and are interested in quantifying greenhouse gas emissions 

## Installation
Installation of the Mission·E dashboard is not necessary since it is a platform independent web application, however keep an eye out for us at our future [homepage](https://www.mission-e.ca).
## Architecture

The web app is designed with a modern microservice architecture. By separating each aspect of the application into a discrete chunk driven by purpose, the overall application becomes more adaptable to future changes and more scalable as instances can be multiplied horizontally.

### Diagram

In order to capture the complex layout of the microservices an architecture diagram is used. Remote sensing data is brought in from external cloud providers and preprocessed to capture only the most essential data. At the core is the private API which manages access to and from the relational database which stores both raw data and data at intermediate stages of processing. The backend is split into two services to accommodate for the differences in complexity and compute times for each data processing objective. The API gateway manages access to the two “frontend” services – the dashboard UI that allows farmers to input and view data, as well as the public API allowing analysts to programmatically obtain data at scale.

![Architecture diagram]()

### Languages + Frameworks
The key languages and frameworks used are summarized below:

+ Backend (R)
    + neuralnet
    + MASS
+ Website (HTML + CSS)
    + html5up
+ Frontend (R)
    * shiny
    * shinydashboard
    * leaflet
    * rgdal
    * tidyverse
+ API (Python)
    * FastAPI
    * SQLAlchemy

## Backend

### Model
We developed this methodology after an extensive literature review examining methods with which nitrous oxide emissions that can be quantified without extensive field sampling. We used a model developed by the University of New Hampshire and referred to as a DeNitrification-DeComposition model (University of New Hampshire, 2007). The DNDC model uses a biochemical computer simulation to model the behaviours of carbon and nitrogen in agricultural-based ecosystems. This model can be used to estimate crop yield, soil carbon sequestration, nitrogen leaching, and trace gas emissions in agroecosystems. The main drivers of this model are climate, agricultural practices, soil properties and crop type. Figure 1 outlines a process breakdown of the DNDC model, including input variables. There are two components to this model: 
The first portion of the DNDC model estimate soil properties based on input parameters such as soil temperature, moisture, pH, redox potential (Eh) and substrate concentration (University of New Hampshire, 2007).
The second component, consisting of the “nitrification, denitrification and fermentation sub-models, predicts emissions of carbon dioxide (CO2), methane (CH4), ammonia (NH3), nitric oxide (NO), nitrous oxide (N2O) and dinitrogen (N2) from the plant-soil systems” (Guo et al., 2020).

![DNDC Model]()

### Data Requirements
There were extensive data required in order to run this model. This section will highlight the necessary parameters required. 
#### Soil and Climate
In order to run this model, we need to input soil properties on a site-specific basis. The input window for this model is as follows:

![Soil Properties]()

A sample of the input parameters is as follows:

+ Soil texture
+ Soil pH
+ Clay content
+ Land use type

#### Agricultural Practives
The following inputs are examples of agricultural practices require to run this model: 

+ Crop type
+ Harvest month : a number between 1 and 12
+ Tillage application : the annual number of tilling applications per year

#### Fertilizer Application

Data on the application of fertilizer was required to determine the baseline amounts applied and the associated nitrous oxide emissions.

#### Input data sources

For our model, we used open-source input variables. The **land use dataset** was obtained from the Cropland Data Layer from the U.S. Department of Agriculture. This dataset outlines all the crop types currently being farmed across the U.S. This is a raster-based data layer with a 30-meter spatial resolution that can be viewed on the MissionE platform. 
The **soil properties** were obtained from U.S. Natural Resource Conservation Service Soil Survey Geographic Database. 
**Local meteorology** was obtained from the Copernicus Climate Data Store. This platform provides daily meteorological data in a raster-based format and is modelled using the ERA5 chemical transport model. This dataset is provided at a 1 km by 1 km resolution. 
The farming management data was obtained using pre-developed surveys from a 2022 paper by Guo et al. called “Assessment of Nitrogen Oxide Emissions and San Joaquin Valley PM2.5 Impacts From Soils in California.” This dataset provides a breakdown of agricultural practices and other variables for each country in California, including the following:

+ Irrigation
+ Fertilizer application
+ Total soil organic carbon
+ Crop type
+ Mean temperature

After calculating the nitrous oxide emissions, we develop a statistical regression model to understand the correlation between fertilizer use and emissions. Using this model, we estimated the proportion of fertilizer that can be considered an excess amount, and then used the remaining fraction of applied fertilizer as a base case. 
To determine the cost savings, we developed an industry average cost of fertilizer per kilogram and multiplied that by the excess quantify of fertilizer being applied. This was used as the cost savings on an annual basis.

## Frontend

### Features
**GHG Quantification**: Our platform allows the end user to identify field-level greenhouse gas emissions based on excess fertilizer.

**Fertilizer application rates**: By quantifying nitrous oxide emissions on a field level, we can identify the excess nitrogen applied for each field and crop type. Using this information, we can determine the optimal fertilizer application rate.

**Cost savings**: The cost of fertilizers can be high. By using the platform and determining the optimal fertilizer rate, the farmer can save money on the volume of fertilizer used, thereby reducing the cost of each yield.

### Website
When vising the future Mission·E homepage potential customers are greeted with information regarding the necessity of our solution – facts documenting the effects of overapplication of fertilizer, excess emissions and reduced soil carbon. The resulting solution is a breakdown of the key features of the application to address the various needs. Finally information about the pricing structure is provided. By pressing the login button the user is redirected to the web application where existing users can be further redirected based on their account type.

![Website 1]()
![Website 2]()
![Website 3]()

### Web App
Since Mission·E is a web application installation is not required. Instead it can be accessed from any device running a modern web browser – including laptops, tablets and mobile phones. As such, farmers are unrestricted in how they use the application, whether it be in the office or out on the field.

### UI for farmers
The principle goal of the application is to provide farmers with insights that allow them to implement more efficient and sustainable practices to help their bottom line and the planet. It is also essential that the farmers using the application are not weighed down by the need to perform a lot of complex data entry or calculations. The UI consists primarily of a map allowing them to gain an overview of their operation and their fields. New fields can easily be added, and farmers are able to draw precise plot boundaries themselves eliminating the need for costly surveying. With boundaries mapped out,  farmers can add their crop types and key inputs on a field basis, allowing them to keep track of this information and feeding the model with the necessary information to make predictions. By sending the information to the application backend and receiving the appropriate output, the frontend can then display important predictions such as the economically optimal nitrogen rate (EONR) on a field basis, and the associated fertilizer and cost savings.

![Farmer UI]()

### UI for analysts
Analysts are given a more high level overview to both allow them to develop more generalized insights, as well as protecting the data privacy of the farmers. Trends for key agricultural insights such as fertilizer usage, emissions and irrigation are provided on a regional basis, and may be provided on a more precise level in the case of more specific partnerships between the farm and analyst firm. Valuable data such as the crop distribution are freely available in the US, but not in Canada. As such we have used the example of California to forecast what these insights would look like after the data has been collected for the Canadian context.

![Analyst UI]()

### Public API for analysts
In order to make data processing at scale easier a public API has been included as a core component of the product roadmap. This allows data clients to programmatically access all of the data they need, and integrate it seamlessly into existing data pipelines.

## Progress
+ Backend – 40%
    + Key model parameters identified.
    + Data sources obtained.
    + Model structure implemented.
+ Website – 100%
    + Website complete and ready for hosting.
+ Frontend – 70%
    * All essential dashboard features implemented.
    * Key controls for both farmer and analyst clients available.
+ API – 30%
    * Entity-relationship diagram created for database.
    * API endpoints defined in OpenAPI Specification document.

## Next steps
### Validation
To bring this product to market, we will need to test the efficacy of our model by conducting extensive ground-based validation techniques. This will require soil surveys of numerous fields across our area of interest, as well as  comparing the results of our modelled nitrous oxide emissions with measured quantities on a field-specific basis.

## Opportunities
**Carbon offset platform**: The DNDC model has the potential to quantify soil carbon sequestration. With this capability, we hope to build out MissionE to provide a carbon offset marketplace. This marketplace will allow farmers to sell portions of their fields as carbon sinks for those looking to buy carbon offsets.

**Climate risk assessments**: We want to harness the power of machine learning and satellite remote sensing data to develop climate risk assessments for those in the agricultural sector. As the climate continues to become more unpredictable, farmers will need a strong understanding of the climate risks that their sites may face. We will use our experience in developing machine learning models with satellite remote sensing data to develop risk maps of climate-related risks including flooding, wildfire, drought, etc.

**Scope 3**: Our emission estimates can be used to quantify scope 3 emissions for those who work alongside farmers and within the agricultural sector. Our model provides extensive greenhouse inventory data on a field-level basis. These data can be used as input values alongside the yield volumes and cost of goods to develop a comprehensive scope 3 emissions framework. 

## Closing notes
We want to thank the developers of the RBC Innovation Challenge for this opportunity.

## Authors
Priya Patel, P.Eng., M.Sc.
Mark Spahl

## References
Kuwayama, T., Croes, B. E., & FitzGibbon, M. (2020). Assessment of Nitrogen Oxide Emissions and San Joaquin Valley PM2.5 Impacts From Soils in California. Journal of Geophysical Research: Atmospheres, 125(24), e2020JD033304. https://doi.org/10.1029/2020JD033304
University of New Hampshire. (2007). User ’ s Guide for the DNDC Model. Oceans, 61.


