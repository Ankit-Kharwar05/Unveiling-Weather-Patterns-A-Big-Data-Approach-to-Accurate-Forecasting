# CDAC-Project

**Project Report On**

**Unveiling-Weather-Patterns-A-Big-Data-Approach-to-Accurate-Forecasting**

![Screenshot 2024-02-23 at 9 29 43 AM](https://github.com/darsh996/Unveiling-Weather-Patterns-A-Big-Data-Approach-to-Accurate-Forecasting/assets/97582053/38abb5a4-781b-4ff2-9a5f-5682ad6ed150)

Submitted in partial fulfillment for the award of
Post Graduate Diploma in Big Data Analytics (PGDBDA)
From Know IT(Pune)

Guided by:

Trupti Joshi Ma’am & Prasad Deshmukh Sir

Submitted By:

Ankit Kharwar<br>
Akhil Goplani<br>
Darshan Satone<br>
Manish Kumar<br>

**Introduction**

This project presents a comprehensive approach to weather forecasting by harnessing the power of big data technologies and advanced analytical methods. Focusing on 200+ Indian cities over 20 years, we employ a sophisticated blend of time series analysis, machine learning, and Apache Spark to delve into the intricacies of weather patterns. The project begins with a robust Extract Transform & Load (ETL) process, ensuring efficient data handling and scalability. Subsequently, we explore time series analysis and machine learning techniques to unveil hidden patterns within the temporal sequences of weather data. Python serves as the primary language, offering flexibility for data manipulation, and analysis, and Tableau for visualization. By uniting machine learning models with big data technologies, our goal is to provide accurate weather predictions for selective city in our dataset.

**Dataset Collection and Features**

We successfully collected a substantial dataset from NASA's prediction of worldwide energy resources through their data access viewer (https://power.larc.nasa.gov/data-access-viewer/).
Additionally, we express our gratitude to NASA for providing this valuable dataset, acknowledging their significant role in advancing scientific understanding and fostering collaborative efforts for the greater good.


**Architecture**

![image](https://github.com/darsh996/Unveiling-Weather-Patterns-A-Big-Data-Approach-to-Accurate-Forecasting/assets/97582053/fab185c4-e503-43c1-a25c-73730b5d9ca0)

In this architecture , we have followed following steps:
1. Get data through API and saving it into json format.
2. Preprocess data using PySpark and converting it into desired format.
3. Combine all the data related to one city into single file using join in PySpark.
4. Saving preprocessed data into MongoDB into database weather in collection name city.
5. Using data from MongoDB to train model and checking its RMSE.
6. Selecting model based on RMSE score and saving it for each city.
7. Pedicting the temperature for 2 days and displaying it using gradio for user interface.
8. Used Airflow to automate updation of daily weather data for each city.
9. Used Airflow to automate retraining of model on new data.


 **Preprocessing**
 
 First of All we have to understand the raw data format that we are getting from API. 
 As we have mentioned above the format in which we are getting the data is **Complex Json Format**. Now to transform it into **simple json format** we have used *PySpark*.
 **PySpark** provide us parallel processing and ability to handle big size data.
   1. Fisrt step is define schema for data for that we have to know about datatype in pyspark that can be used and what data we require from raw data.
   2. We have selected MapType(key,value) datatype as it is able to help our data to be furthur processed easily.
   3. Loading the data into dataframe by using schema defined by us with name *data_df*.
   4. After that for each parameter we created different dataframe (parameter_dataframe) and columns in that datframe were obtained by exploding mapType() columns into two columns of *Date* and *Value*.
   5. Now we combined all the parameter_dataframe into one dataframe named as Temperature_dataframe ,humid_dataframe or wind_dataframe using *inner join*.
   6. Now we combine temperature_dataframe, humid_dataframe and wind_dataframe into one dataframe using *outer join* as these dataframe come from different files so that even if there is null value for any timestamp. that value will be included in the final dataframe. name this df as city_df.
   7. Now as we are going to save all this data into MongoDB we require primary key to store data so that no value shoud be duplicatee itself in the final db. for that we are going to add one more column in the dataframe i.e, City_df using withColumn() function and column function *F.concat( city_df['Date'], F.lit('_'), F.lit('name_of_the_city'))* naming that column "_id". 
   8. We have column _id having value 2020121012_mumbai which is unique in all data we can get as it represent timestamp for that city.

 **MongoDB**
 
 We have used PySpark-mongoDB connector to connect mongoDB with PySpark. As it is very memory and time efficient to write in mongodb using pyspark connector in comparison to use pymongo connector.
 First we have to make database in mongodb with name weather and in which collection with name city using mongosh
 We followed mongoDB documents (https://www.mongodb.com/docs/spark-connector/current/) to make connection with spark
 Now write the pyspark dataframe into mongodb in append mode and as we have coulmn named _id which will work as primary key in mongoDB and prevent from duplicacy of the data.

**Algorithms**

**AutoRegressive Integrated Moving Average (ARIMA) :**

![image](https://github.com/darsh996/Unveiling-Weather-Patterns-A-Big-Data-Approach-to-Accurate-Forecasting/assets/97582053/f2bf0c53-f8d1-460c-b861-aaff0200752e)

The Root Mean Square Error (RMSE) is a crucial metric for evaluating the accuracy of a forecasting model, including the ARIMA model for weather temperature prediction. An RMSE of 8.1337 indicates that, on average, the model's predictions are approximately 8.1337 units away from the actual observed values.
In the context of weather temperature prediction, an RMSE of 8.1337 implies that the model's forecasts may deviate from the true temperatures by around 8 degrees Celsius. While this might be acceptable for some applications, it is essential to aim for lower RMSE values to improve the model's accuracy.

**Seasonal Autoregressive Integrated Moving Average (SARIMAX):**

![image](https://github.com/darsh996/Unveiling-Weather-Patterns-A-Big-Data-Approach-to-Accurate-Forecasting/assets/97582053/942138cc-063e-41d6-b3c4-556407cf8a84)

Achieving an RMSE of 0.6172 demonstrates the model's ability to effectively capture both seasonal patterns and the influence of external factors on temperature. This improvement in accuracy can significantly benefit various applications, such as energy management, agriculture, or transportation planning, by providing more precise and reliable weather forecast.

**Working Model:**

![Screenshot 2024-02-23 at 9 26 23 AM](https://github.com/darsh996/Unveiling-Weather-Patterns-A-Big-Data-Approach-to-Accurate-Forecasting/assets/97582053/b3b88815-e65d-46e9-905b-aae6024cfb1f)


**Airflow**

We have used airflow for continually updating weather data for every city in database and to retrain model with new data we get every day.
for that we have made two dags :-
1. newdata_dag : it schedule and pipeline the preprocessing part and mongodb part also.
2. remodel_dag : it schedule and pipeline the retrainging part and saving train model in pickle format and overright previous model.

**References**

Apache Spark. [https://spark.apache.org/]

MongoDB. [https://www.mongodb.com/]

Kafka. [https://kafka.apache.org/]

Python. [https://www.python.org/]

scikit-learn. [https://scikit-learn.org/]

The data has been taken from NASA prediction of worldwide Energy Resources https://power.larc.nasa.gov/data-access-viewer/
