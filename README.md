
# ap062
Source Code of "**Project Green-Globe**", AP062, InnovateFPGA Design Contest 2021-22.

This repository is created to describe the functionality and source code of the "Project Green-Globe" project which is a Customized and Intelligent Watering System for Horticulture. 

Read complete project paper at **https://www.innovatefpga.com/cgi-bin/innovate/teams.pl?Id=AP062**

## Appendix
We, Team AP062, have developed an authentic autonomous plant watering system to regulate the overwatering and underwatering issues that happened while watering plants. As these two scenarios are affecting and having their active impressions on two major #GlobalChallenges that are #WaterConservation and #ClimateChange, we thought to regulate those situations and address these issues with the help of the #Technology to create sustainability.
With this project, we have four main goals -
    1. The regulation of water waste creates an impact on conscious water conservation.
    2. Growing Plants effectively and efficiently in a healthy manner by saving resources.
    3. Keeping a record of the plant's behavior and providing supplies.
    4. Initiating the idea of connecting the INDUSTRY 4.0 technology to conserve natural resources and address global challenges.
    
The developed system is successfully delivering the essential needs of delivering the water to the plants and eliminating human effort while watering plants. We developed the system in three possible implementation methods (i.e., POT LEVEL IMPLEMENTATION, CROP LEVEL IMPLEMENTATION, GARDEN LEVEL IMPLEMENTATION) initially and the system is made in such a way that it can adapt to any kind and any scale of Gardens and Crops.

![block-diagram-ap062-120ppi](https://user-images.githubusercontent.com/103910360/163804966-25fcbe92-4805-4742-8569-f656b6a5b801.jpg)
The above is the Block Diagram of the system

There are 5 total units in the system we developed. 
    **1) Primary Control Unit (PCU)** - The control unit which performs all the computation and processes the triggers according to the code flow.
    **2) Secondary Control Unit (SCU)** - The control unit which takes the input signals from PCU and performs actuations accordingly.
    **3) Plant Monitoring Unit (PMU)** - The plant monitoring unit is a sensor in one kind of implementation of this system. The records of water delivery and the soil moisture sensor values from the plant are monitored and saved in the cloud storage point. The plant monitoring unit is a mix of some hardware with cloud and internet.
    **4) Cloud Unit (CLU)** - We are using the cloud for storage. The cloud is a two-way communication for both the primary and secondary control units. We store databases and save the records of logs of every action performed.
    **5) Internet API Block** - For monitoring weather conditions and predicting rainfall, we are using some internet APIs. For authentic data, we have chosen Azure Maps and Weather Conditions services.
    
We have chosen three different areas on our campus to check the system performance on three different implementation methods.  We implemented the system on POT LEVEL, CROP LEVEL, and GARDEN LEVEL. We get the desired results and efficient metric values with provided telemetry and computation in the **#TerasicDE10-NanoDevelopmentBoard**. In POT LEVEL, the system works with 94.37% efficiency, in CROP LEVEL, the system works with 97.88% efficiency and in GARDEN LEVEL, the system works with 96.14% efficiency. The system also saves the records of logs in the cloud storage point.

### FLOWCHARTS
![flowchart-ap062-120ppi](https://user-images.githubusercontent.com/103910360/163806331-708b3d59-ca29-4df1-8e9d-851d6c60ebd5.jpg)
The above is the flow chart of the code

![flowcharts-sub](https://user-images.githubusercontent.com/103910360/163806379-69e26563-4cd1-49d0-b473-a3c8071e9269.jpg)
The above is the subblocks inside the flow

### Importing Modules
Required Modules are `requests`, `json`, `mysqli.connector`, `boltiot`, `numpy`, `pandas`, `sklearn` 
- Required Modules are mentioned in the `requirement.txt` file.
- Run the `python3 -m pip install -r requirements.txt` command to install all the required modules.

### Defining the manual functions for Converting different units.
There are certain functions to convert the temperature, pressure, and relative humidity metrics to different units.
### Defining Constants
There are several constants used in the code for fetching APIs, Water Flow, Calculation Parameters, and coordinates. These all are defined in this section.
### Defining Plants' Details
There are several kinds of plants in all three different implementation levels.
The details of those plants, scientific names, and water required for that plant have been mentioned inside the code and those values are assigned to identifiers for further use.
The total water required for each implementation method is calculated as 
- `TotalWaterSuppy = WaterRequired*TotalPlantsNumber` 
Here the `WaterRequired` is the amount of water required for each plant and `TotalPlantsNumber` ` is the total number of plants in that implementation method.

### Getting Date and Month
The date and month are taken from the `date.today()` method from the `datetime` module.
- `today = date.today()`
- `thisMonth = today.month`

### Defining Months and Number of Sets
We took 4 seasons and identified them with their months. The `numberOfSets` is defined based on these seasons. If it is a torrid season with hot temperatures like summer and months in autumn, the `numberOfSets` is set to `2`. It means the total water delivery of a plant/tree per day is divided into two times a day. It is set to `1` for the remaining months in different seasons.
The pot level implementation is different for this as only one plant can be kept in the pot and the `numberOfSets` is set to `1` always.

### Fetching Weather Details From APIs
The `fetchCurrentConditions()` is written to fetch the current weather conditions using the coordinates of the implementation location (i.e., Nuzvid - INDIA).
This function fetches the metric values `Temperature`, `Pressure`, `Relative Humidity`, `Dew Point`, `Ceiling`, `Pressure Tendency`, `Wind Speed and Direction`, `Wind Gust`, `Temperature Summary of Past Hours`, `Precipitation Summary of Past Hours` and push these values to the `telemetry` database in the cloud using `sendTelemetrytoDB()` function.

### Get Temperature and Humidity Values from RFS Module.
The RFS Module comes with in-built temperature and humidity sensors. We take the code from `https://github.com/terasic/InnovateFPGA2021/blob/main/docs/PaaS-Provision.md` which is provided by the support team of Terasic. 
We slightly modified the code to take temperature and humidity values from that but **we didn't upload it in the `main.py` as there might be chances of copyright issues**.
What ultimately happens in this section is, that the code runs for every interval of time and takes the temperature and humidity values from the RFS module, and pushes it to the cloud storage point which is a database. These temperature values are taken for calculating and forecasting the further temperature conditions of the day using `Regression Methods` and those values are used for `Rainfall Prediction`.

### Parameters for Rain Prediction
The `paramsForRainPrediction()` takes 5 parameters of `temperature`, `wind`, `pressure`, `humidity`, `dewpoint`, `precipitation` for calculating the probability of rainfall with the given metric values. Based on the values of the parameters, they are identified as `hot`, `mild`, `cold`, `high`, `low` and then convert to numbers as `0`, `1`, and `2`.
These values are taken into a list for further calculation of Rainfall chances.

### Conditional Probability For Rain Fall Prediction
We have used the **Gaussian Naive Bayes** method to calculate the chances of rainfall.
The `predictRaiFall(currentValues)` function is written to perform Gaussian NB on the Current values of weather conditions which are coded as parameters into a list in the above section and return the value as `1` or `0`. 
We made a dataset according to different possible conditions (verified manually) and named it `GNBTrainData.csv`. The `predictRaiFall(currentValues)` initially loads the dataset and uses it for training the model and later performs the operation on the passed parameters of current weather condition values.
If the returned value is `1`, it means there is a chance for rainfall and if the returned value is `0`, it means there is no chance for rainfall according to the current weather conditions.

### Regression Method for Temperature Prediction
This block runs a total of three Machine Learning Models for temperature forecasting and rainfall prediction.
- We are using `Linear Regression` and `Polynomial Regressions` to predict the temperature forecast. The Regression Models are trained with the dataset given `regTrainData.csv`. Using that, the forecast values are predicted after collecting the actual temperature values that are taken from RFS Module.
- Later we are performing `Z-Score Analysis` on the predicted values of temperature forecast to find any sudden steep in temperature values. If there is a steep, then it is considered an anomaly, and the system understands to perform rainfall prediction.
- If there is an anomaly observed, the program calls the `predictRaiFall(currentValues)` function which uses the `Gaussian NB` method to predict rainfall chance.

The `tempraturePredictor()` function performs all the three above said tasks and returns with a value of `1` or `0`. 
If it is `1`, then there is a chance for Rainfall and the system triggers the `rainFallAPICall()` function.

### Rainfall API CALL FUNCTION
The `rainFallAPICall()` function request the Azure Weather APIs to check the rainfall and get the details of the rain.
It returns the values of `Precipitation Type`, `Precipitation Intensity`, `Rain Value`, `Rain Value Units`, and `Hours of Rain`.

### Schedule Watering
This block schedules the timing to deliver the water to plants. The logic of the program schedules the timing for all the three implementation methods according to the weather conditions and rainfall possibility.
The watering schedule is possibly made for the morning time if it is `numberOfSets = 1` set and if the `numberOfSets = 2`, the schedule could be planned for both morning and evenings.

The `valveRunTime(waterPerSet_)` function calculates the runtime needed for each particular implementation level. It takes the `WATERFLOW` constant and multiplies it with the `waterPerSet_` parameter which is passed through the argument when the function is called.

If there is rainfall predicted and confirmed, based on some conditions of the rain measurement and hours of rain, the `numberOfSets` and `deliverAmountPerSet` variables change.

### Pushing data to the cloud
After all the computations are completed, the code flow then goes to the `pushDataToCloud(rainApiCall_)` function. 
In this function, the values of all the three implementation level's water amounts and runtime are assigned into tuples and then the function pushes the data to the database using the `connectionString`.
Since the values of water supply, the number of sets, and runtime change if there is rain, the `pushDataToCloud(rainApiCall_)` function takes the value of `rainApiCall_` which is either `1` or `0` as a parameter. 
If the `rainApiCall_` is `1`, it means that there is rainfall and the altered values are filled with `NaN` and then they are pushed to the cloud.
If the `rainApiCall_` is `0`, it means that there is no rainfall and the exact values are pushed to the cloud without any changes.

## Secondary Control Unit (SCU)
![scu_flowchart-ap062-986](https://user-images.githubusercontent.com/103910360/163924181-3256aaee-8c7d-47b3-9207-c4221ef8e64d.jpg)
The above is the flowchart of secondary control unit.

Secondary control unit takes the latest record from Database and check for the schedule timings of different implementation levels. Later perform actuations according to those timings.
The loop runs for every 15 minutes and check for the schedule timings and deliver water whenever assigned. 

