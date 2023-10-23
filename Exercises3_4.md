### Centric Pricing Technical Test
#### Rubén Barco Terrones

#### Exercise 3
**Design a CI/CD and deployment plan to serve the final model in a cloud based production environment. This plan should include MLOps tools and KPIs monitoring and we don't expect you to implement any code here.**

There is an image with name `schema_exercise3.jpg` in which we can see the schema of the proposed environment.

Fist of all we need a Development Environment. Here we can work by creating new branches. There are two steps:
1. The first one is to work using Jupyter Notebooks in combination with FS (File System in cloud environments), SQL Databases or Blob Storages (in Azure) for example.
2. Once we have finished our goal with the notebooks, the second one is to create .py files (i.e. with the ETL, the Preprocessing, the Trining and the Prediction modules). Also a config file with parameters, options, etc. and the CI/CD File.

Once we commit the changes in the branch, the CI/CD that is going to **run all the tests** (integration, unit, etc.). If all the tests are OK, it can be merged into development or production and the Pipeline can be created.

The Pipeline is going to have 2 different parts.
1. **Training Pipeline**. In this Pipeline we are going to train the new models. It is going to have a ETL process, the Preprocessing of the images and then the Training. If the training ends correctly, some metrics are computed and can be shown for example in a PowerBI report. The model is saved for example in s3 type in AWS or in a Blob Storage in Azure. If the training of the model fails, an alert is sent to the development team.
2. **Prediction Pipeline**. This is the Pipeline that is going to be executed when we want to classify the images. It is going to have also the ETL and Preprocessing modules to obtain the images and prepare them. Then the model is loaded and the predictions are stored.

Both Pipelines can be automatized using for example AirFlow or with a Job in Azure. In the case of the Training, we could, for example, schedule that it is going to be executed once per week and the Prediction one one per day. 




#### Exercise 4
**Let’s imagine that now you have to load the product images from our database along with some additional text features like the product description provided by the retailers. The data is stored in an SQL relational database (postgresql) with the following schema:**

*There is a photo with name `sql_exercise4.png` with the database squema*

**Write a query (either in django ORM or in SQL) to extract, for every existing product, the following fields:**
1. **Product.Title**:
   If we want all the products that have images:

       SELECT Product.Title
       
       FROM Product
       
       INNER JOIN ProductImages ON Product.ID = ProductImages.ProductID
   
   If we want all the products, Whether or not they have images:
   
       SELECT Product.Title
       
       FROM Product
       
       LEFT JOIN ProductImages ON Product.ID = ProductImages.ProductID
   
3. **Image.Url for the images with the ImageIndex = 0. ImageIndex field states the priority order of images of a certain product. So for a given ProductId, the image with ImageIndex = 0 would be themost relevant image for that product:**

       SELECT P.Title AS ProductTitle, I.URL AS ImageURL
       
       FROM Product AS P
       
       JOIN ProductImages AS PI ON P.ID = PI.ProductID
       
       JOIN Image AS I ON PI.ImageID = I.ID
       
       WHERE PI.ImageIndex = 0
   
5. **ProductDescription.TranslatedText if exists, else ProductDescription.OriginalText for ProductDescriptions in CountryCode = ‘us’:**

       SELECT P.Title AS ProductTitle,
       
              CASE
       
                  WHEN PD.TranslatedText IS NOT NULL THEN PD.TranslatedText
       
                  ELSE PD.OriginalText
       
              END AS Description
       
        FROM Product AS P
       
        LEFT JOIN ProductDescription AS PD ON P.ID = PD.ProductID
       
        WHERE PD.CountryCode = 'us'
