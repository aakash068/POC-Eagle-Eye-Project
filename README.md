# **POC-Eagle-Eye-Project**

Identify Prospects from Online Source for Business Expansion, classify them as per their Business Segments and rank them as per their popularity

![Capture1](https://user-images.githubusercontent.com/53387262/141434144-98a28fae-7878-43d9-a074-1ff00ad7a64f.PNG)

## **Project Overview**:

![Capture](https://user-images.githubusercontent.com/53387262/141350655-3202b1f8-b2b9-4c08-878b-91ab077c2424.PNG)

## **Filtering POCs based on Latitude - Longitude**

The POCs have their Names and location identified by latitude-longitude. Since, the main focus is POCs from these 4 regions(Region- A,B,C,D), I filtered POCs lying only in these regions.

![Capture1](https://user-images.githubusercontent.com/53387262/141431503-596f0541-ad66-4e2c-b0bf-6605f4cc4dbf.PNG)

## **Fetching Address for each POC from latitude-longitude**
Once we filter out POCs from these regions, we find address for that POC from latitude-longitude. This address will be used in subsequent steps of POCs matching to identify Prospects. I have passed latitude-longitude values as query string into the _googlemaps_ api to fetch the Address. 

![Capture1](https://user-images.githubusercontent.com/53387262/141432066-5368e117-3480-4043-b61b-d7b24388cc04.PNG)

Using API to fetch info often results in error, so for some POCs, Addresses returned are null and void, so can't use these in further stages. 

## **Google ID Mapping**
In order to find Prospects - POCs that are not present in Internal Database, Google ID will be used to match a External POC with all Internal POCs. So, Google ID is a primary & Unique Key for each POC Name & Address Combination.
To find Google ID for each POC, combine the POC Name and its address into a string and pass this query into an _googlemaps_ api to fetch the Unique Google ID. 

![Capture1](https://user-images.githubusercontent.com/53387262/141432437-0c969e4d-0dd5-40c6-a9be-07fbee045539.PNG)

Normally, different iterations have to be done by considering all possible combinations of address query. I automated this mapper and this process converges for each query and then starts the iterations and so on. 

## **Matching & Identifying Prospects**
![Capture](https://user-images.githubusercontent.com/53387262/141349967-62784520-edf8-49ae-abf4-f087f2fdb8bd.PNG)
#### _Geohash Refining:_
In Matching, we are comparing POCs within a Geohash. A Geohash is a unique identifier for a particular region on the Earth. 

We see some Geohash contains more POCs, so more comparisons had to be done, so matching becomesslow. That???s why, I split a Geohash if it contains more than 150 POCs & re-tag the POCs to those refined Geohashes. Using this I was able to increase Effectiveness of Matching.

![Capture](https://user-images.githubusercontent.com/53387262/141356700-94d14053-5c35-40d0-b5d4-00fce8398ad8.PNG)

Earlier the Time aken to compute matching depends on N- External POCs and M- Internal POCs, with number of Geohases mostly constant, so order of **_O(N * M)_**. But once the number of POCs within a Geohash is capped (count <= 150), Time Complexity is of order **_O(N)_**, with the increase in Geohashes is insignificant.  

## **Segmentation of POCs**
Here, I identify the Business Segment for each POC from the description provided by the user as Category Labels.
#### _Category Label based Segmentation:_
The Classification Model is trainined on a similar set of unique desciptions against which I have a manually recorded Segment. Each description is transformed into a vector using TF-IDF Featuriser, then Classifier is fit on these vectors with Segments as responses. Once the model is trained on these labels-segment data, I use this model to predict the Segment for the Prospects I have filtered out. 

![Capture](https://user-images.githubusercontent.com/53387262/141358715-c6312478-20d0-49a3-b66f-d1f1327f808b.PNG)

Training Data using the Tf-Idf vectoriser is a sparse matrix of very high dimension. This might result in high risk of overfitting and the train-test time complexity increases quadratically with increase in these vector dimensions.
I have used Feature Engineering using _Cosine Similarity_ to reduce the dimension and form features that explains the most variances. This resulted in increase of classification accuracy

#### _Name based Segmentation:_
I observed some segments were wrongly predicted by Model since the description recorded by User is wrong. I decided to correct those from the POC Names directly.

The idea is, if POC Name has the words-"Pub","Bar","Wine"... then that POC should ideally belong to "Bar"-Segment. 

![Capture](https://user-images.githubusercontent.com/53387262/141360452-6655e2ae-5210-4e51-ba9f-ee44fa5050a6.PNG)

Using, feature engineering and probability mass functions, I find words that are very frequent in a particular Segment(_**High Term frequency in a Segment**_) and highly rare accross all other segments(and high _**Inverse Document Frequency = log(1 + N/n)**_ ), and using these important words I corrected those Segments.  

## **Indices Calculation:**
![Capture](https://user-images.githubusercontent.com/53387262/141420843-32189ac5-f536-4c82-9586-a1314fcf2295.PNG)

## **Dashboard:**
![Capture](https://user-images.githubusercontent.com/53387262/141426801-2876b082-afec-44f2-9332-adeebab438e2.PNG)


