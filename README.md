# Clustering_Test
This repo holds a Clustering K-Means model written in PySpark.

### Visualization of the clusetrized data
<img width="615" alt="Screenshot 2021-04-27 at 10 01 39" src="https://user-images.githubusercontent.com/71548024/116207045-bcf6c800-a73f-11eb-9523-88234d0a5f18.png">

### K-MEANS

*In order for the Colab Notebook to work, the JSON file has to be uploaded (i.e. /content/)*


A JSON file is used to create a DataFrame from which numerical variables are selected to create a vector.

```
from pyspark.ml.feature import VectorAssembler
df.columns
assemble = VectorAssembler(inputCols=[
 'latitude',
 'longitude',
 'number'], outputCol='features')
assembled_data = assemble.transform(df)
assembled_data.show(5)
```
<img width="697" alt="Screenshot 2021-04-27 at 10 13 30" src="https://user-images.githubusercontent.com/71548024/116208497-478bf700-a741-11eb-8acd-8c45dea646d1.png">


Once the vector has been created, a K-Means algorithm with squared euclidean distance is used in order to get a silhouette score.
The silhouette score will indicate the consistency of the number of clusters that are being created (i.e. k).

```
from pyspark.ml.clustering import KMeans
from pyspark.ml.evaluation import ClusteringEvaluator
silhouette_score=[]
k_list = []
evaluator = ClusteringEvaluator(predictionCol='prediction', featuresCol='features', \
                                metricName='silhouette', distanceMeasure='squaredEuclidean')
for i in range(2,10):
    
    KMeans_algo=KMeans(featuresCol='features', k=i)
    
    KMeans_fit=KMeans_algo.fit(data_scale_output)
    
    output=KMeans_fit.transform(data_scale_output)
    
    
    
    score=evaluator.evaluate(output)
    
    silhouette_score.append(score)
    k_list.append(i)
    
    print("Silhouette Score:",score,i)
```

We visualize the silhouette score compared to the number of clusters and analyze it through the Elbow Method.

<img width="459" alt="Screenshot 2021-04-27 at 10 15 22" src="https://user-images.githubusercontent.com/71548024/116208727-7e620d00-a741-11eb-90fe-d03db75c0c94.png">

It can be observed that the optimal number of clusters is k=4 and so such k is used to create the predictions that will lead to the clusterization of each row.

In order to have a compact DataFrame that will encompass the original data and the cluster, we transform both DataFrames into Pandas DataFrames so that they can be merged.

<img width="1005" alt="Screenshot 2021-04-27 at 10 16 06" src="https://user-images.githubusercontent.com/71548024/116208845-9afe4500-a741-11eb-8b68-6b7027f4d79f.png">

The Pandas DataFrame i sconverted to PySpark Dataframe so that we have the original values clustered.

<img width="804" alt="Screenshot 2021-04-27 at 12 31 14" src="https://user-images.githubusercontent.com/71548024/116227548-90997680-a754-11eb-9cd9-b87af7ef43d8.png">

A temporary view is created so that queries can be performed on the complete table.

<img width="807" alt="Screenshot 2021-04-27 at 12 51 35" src="https://user-images.githubusercontent.com/71548024/116229929-52ea1d00-a757-11eb-8da0-1feff6efbf95.png">

The final merged DataFrame is exported as JSON file so that other operations may be performed in the same format.

