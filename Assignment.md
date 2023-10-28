## Problem Statement
I have decided to choose to build a text dataset from twitter for deepfake detection classifier. 

## Source of dataset
For my initial small dataset, I had multiple thoughts on how I would go about obtaining the dataset. I read through multiple research papers (refer Reference links) , walked through publicly available datasets and decided to use the kaggle dataset available for twitter text [1] . 

The author of the above research paper [2] employs a heuristically search through twitter to collect machine generated tweets. He searches for bot accounts using AI keywords related to text generation, GPT, LSTM.  After obtaining the accounts, they were filtered using whether the account was referring to autonomous text generation methods in the description or accounts similar to celebrity accounts which mimic human accounts were chosen to be bot accounts. 

### Some other ideas considered

I thought of obtaining the data from conversation with deepfake text bots. The person chatting with the bot would represent human data and the other end would be deepfake data. In this case, I am assuming that I would use one of the deepfake websites to generate the text. Although this solution is definitely not very scalable and the dataset would be very small.

For an ideal case, I also considered building a scraper which would basically scrap through the internet to obtain tweets for data. The web scraper has numerous applications like scraping twitter accounts tweets. It could scrape instagram for image data and video data. For text data, articles and journals on renowned newspapers could also be used. This can also be expanded to other social media like reddit, discord, etc. 

I also thought of building a custom pipeline which would send requests to websites like Fakeyou [3]. This website lets you generate the voice of your favorite character or celebrity. This could be used to obtain voice data. The application when combined with the scraper mentioned above could be used to get text, image and audio data.

### Architecture for the potential system that could not be built in time provided

![Fig3](https://drive.google.com/uc?export=view&id=16_RDUtHD8sIByuIjbGCXsdoQ5cv9wWWr)

I have considered using lambda for scraping the social media platform, news websites and deepfake AI websites to collect text, image and voice data. The lambda could then upload the data to s3 in potential parquet format. To make the code more scalable we could also use Fargate because of lambda limitations like memory size of 15GB for instance. Adding a SQS in between for storing the data in the queue would also make it more robust and failure proof. If required data archiving could be achieved using AWS Galcier. The idea behind using AWS Glue and Athena is for data warehousing and for its querying capabilities. AWS Sagemaker is used for training machine learning models. AWS CDK could be used to main the entire infrastructure for each region with integration tests.  


## Information being stored
The dataset obtained from kaggle contains 23 bots, 17 human accounts. Twitter rest API would be used to obtain the information from twitter. The dataset ideally needs to be balanced so that the classifier does not favor the majority class. Hence the tweets must be randomly sampled from human and bot classified accounts.  The current dataset contains 25836 samples.  The data that i am considered potentially to be stored are :

* Account ID
* Time stamp
* Tweet / payload
* Account type / label
* Class type 
* Screen name

For the system I have proposed, I would think of the following fields : 
* Account Type : Human or Bot
* Payload / Data 
* Class Type : Label
* Timestamp of data or post to use for data querying.
* Platform obtained from : This could be Twitter, Fakeyou, Wall Street Journal, etc. This field could be used with sample query like say obtaining all data from Twitter only.
* Data Type : This could be text, audio, video, image.

## Preprocessing the data

Preprocessing needed for the kaggle dataset :
* Duplicate tweets from the same account need to be removed. Timestamp will be used to perform this. Note that duplicated tweets by a bot impersonating the actual human shall not be removed.
* Tweets that are not in English are beyond the scope.
* Tokenization with punctuation is performed on the dataset.
* Removal of hashtag
* All user mentions should only contain token

In addition to above mentioned points, data Cleaning for proposed system can also include:

* Duplicate images, videos or audio obtained from the same source will be deleted.
* For image, normalization, rectification and transformation techniques will be employed.
* For audio data, use noise reduction techniques such as spectral subtraction, Wiener filtering.
* All the data (voice / image) can be transformed into the same format and normalized.

## Summary statistics from csv to evaluate performance of classifier 

Evaluation metrics like Accuracy, precision, recall and f1 score will be used to evaluate the classifier. 

For a nontechnical audience, the best evaluation would be using visualization techniques. 
For instance, Confusion matrix or seaborn heatmap can be used to visualize the actual vs predicted labels. Bar graphs will be used to compare accuracy or other statistical metric per class per different classifiers.

Below is a sample confusion matrix that I generated using sklearn that could be used 

![Fig2](https://drive.google.com/uc?export=view&id=1ZY5KqseAkTmUYxBsyDrnxNYERx2_fxQR)

A sample bar grapgh to use for visual presentation for different classifiers would be :

![Fig3](https://drive.google.com/uc?export=view&id=1twdCOs3wSeOy3bl_pVC9j6MOd-B0zzTl)

To present it to a non technical audience, the dataset visualization can also be implemented using AWS Data Visualization. 

## Insights
Precision talks about how precise you want the model to be. It weighs in the total predicted positive. If the cost of false positives is high, precision is the better metric.
 
Recall has a higher weight on actual positives. 

The choice for having a higher precision or recall is completely case dependent and I believe that in this case, having a more optimized recall score is the more important task. This is because the cost of misclassifying a bot as a human is significantly higher and could result in serious fraudulent activities.

This can be achieved by either assigning a higher weighting to the examples of a specific class  or tuning hyperparameters to maximize precision or recall on the validation set. For this case, I am assuming to be using a SVM classifier. Grid search is one of the techniques I have used to achieve this. More details on obtaining the best features and hyper parameter optimization can be obtained from the paper I published in IEEE [4]

## Reference
[1] https://www.kaggle.com/datasets/mtesconi/twitter-deep-fake-text/data

[2] [	arXiv:2008.00036](https://arxiv.org/abs/2008.00036)

[3] https://fakeyou.com/ 

[4] Z. A. Sunkad and Soujanya, "Feature Selection and Hyperparameter Optimization of SVM for Human Activity Recognition," 2016 3rd International Conference on Soft Computing & Machine Intelligence (ISCMI), Dubai, United Arab Emirates, 2016, pp. 104-109, doi: 10.1109/ISCMI.2016.30.

