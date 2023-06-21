# Blog Post #1. 
## Data ingest example with Upload feature in Kibana 
To begin this series of blog posts, I would like to start on how I got here. If that is interesting, read on, otherwise [click here to skip to the data ingest steps!](https://github.com/nicpenning/Elasti-daddy/blob/main/Blog%20Posts/Blog%20Post%20%231.%20Data%20Ingest.md#data-ingest-via-kibana-upload)

My wife and I have recently been blessed with a new baby and one thing that was highly recommended was tracking the amount of food our baby was eating.
This had us jotting down the feeding times for our little one which consisted of a `Start Time`, `End Time`, calculated `Duration` in minutes, and lastly the 
`Side` the baby nursed on. We then added how often mom and the baby took medicine, when milk was getting extracted for later feedings, and even how
much formula we supplemented with the other feedings. In summary, this is a tracking mechanism, however, when we reviewed the pages and pages of notes
it was hard to answer the questions: `How long on average is our baby going between feedings?`, `What times of day is the baby most likely to feed?`,
and `Can we actually recognize any patterns that could better prepare us for the day?` 

My personal goal is to see if I can answer the questions above by taking the data that we have hand written down, placing it into a document that will 
be saved as a CSV and then ingesting into Elasticsearch for analysis in Kibana. 

#### Simplified Process
`Hand written notes -> Spreadsheet -> Feed Me.csv -> Elasticsearch (Ingest) -> Kibana (Visualize)`

Along this journey I figured it would be a great opportunity to understand
how Elastic Integrations work so that I can build others down the road that are more practical that the world can enjoy. While I plan to have a fully
functional integration at the end of this project, it `will not be published` since it is a very niche integration that only those that follow this project will see.

Since I will cover standing up the Elastic stack in a later blog post, I will skip that here and go straight to the data ingest and a glimpse into the end goal.

So without futher ado, let's get into it! (Note: I will be using Elastic Stack version 8.8.1)

#### Data Ingest via Kibana Upload
1. Start up the Elastic Stack using the elastic-package tool (covered in later blog post)

`elastic-package stack up -d -v --version=8.8.1`

2. Navigate to Kibana's home page and click on Upload a File
	<details>
 
	1. Click on Elastic at the top of Kibana to get to the Welcome Page
	 
	2. Click on Upload File
	 
	3. This is a note that the URL you could navigate to is: `https://127.0.0.1:5601/app/home#/tutorial_directory/fileDataViz`
 
	![Screenshot 2023-06-18 134546](https://github.com/nicpenning/Elasti-daddy/assets/5582679/6de30cfc-47a5-4a1c-9c7e-83c18dbfb9dd)
	</details>

3. Drag and Drop or Browse to the `Feed Me.csv` file to upload.
	<details>

	[Feed Me.csv found here](https://github.com/nicpenning/Elasti-daddy/blob/main/Data/Feed%20Me.csv)
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/4160bfd3-24c1-4f50-a98e-c2abec534887)
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/8aa7bcbe-786b-4282-8557-54a71825e5e7)
	
	</details>
4. Click Override Settings to Tweak Settings for Import
	<details>
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/11b79ea8-5e30-47d4-8983-27d0642749fc)
	
	Now that we are at he point we can tweak our ingest of the file I want to point out a few settings that we will need to set to make sure we get the data into Elasticsearch that will be usable for our visualations and search. [Documentation on Upload feature in Kibana](https://www.elastic.co/guide/en/kibana/current/connect-to-elasticsearch.html#upload-data-kibana)
	
	⚠️ Note: The upload tool is great for a quick analysis of small files. This is not useful for any type of repeatable process which is why I wanted to demonstrate what we can do with a Proof of Concept before we dive into creating the integation. I believe this Upload tool is the fastest way to get this type of data intoElasticsearch with as little tooling possible.
	
	*Settings*
	You should be able to see a flyout window that has the following as the default settings we will soon change:
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/c6058ddb-87e4-4dad-a74c-2122b3ad2b72)
	
	We will select the following settings:
	- Should Trim Fields (This is selected because in my dataset I may have some spaces after the text. This will clean up the data for us quite nicely.)
	- Contains Time Field. (This will allow us to visualize our data over time since we need to have a Date data type.)
	
	When we select Contains Time Field, two new fields appear that we will set to the following settings:
	`Timestamp format` : `custom` (which will make the `Custom timestamp format` field appear.
	We will set the format to `M/d/yyyy H:mm` since this will match our date format of `5/24/2023 17:46`
	Lastly, we we make the `Start Time` our Timestamp field so we can see when each event started.
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/e2c7e6a0-2573-43fd-bd36-36f78b21516d)
	</details>

5. Click Apply
	<details>

	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/d5a6a643-0f42-4728-bd0a-fc692c390fc4)
	</details>

6. Click Import (at the bottom of the page)
	<details>
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/58cb4560-17f1-4e2e-b28a-1131dcea28a4)
	
	 - ⚠️ Note: The data will not be imported yet but rather you will be taking to the next step of the import process. This is a little confusing so I put in an [issue](https://github.com/elastic/kibana/issues/159826) for Kibana here to see if Elastic will make that button say *Next* instead.
	</details>

7. Click Advanced
	<details>

	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/3dcc9817-57f2-45ef-993f-3cd72b09a980)
	
	We are using the Advanced option for a couple of reasons:
	 - Ensure we get Date mappings for `Start Time` and `End Time`
	 - Ensure we apply the correct time zone for the data, tweak the `Medicine 💊` field to be an array, and make sure that the `Amount (ml/cc)` and `Duration` fields are a long.
	</details>

8. Set Index name and do not create a Data view
	<details>
	Set the Index name to `feed_me`. This will be the name of our log source we will use later. Also, the data-view will be imported along with the dashboard saved object later.
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/9925b186-cb5d-4feb-9350-0b4991e284b1)
	
	</details>
9. Update Mappings (you can copy paste the following code into the Mappings section)
	<details>
	Now we must update the `Start Time` and `End Time` from type `keyword` to type `date`, so the Mappings JSON looks like this:

	https://github.com/nicpenning/Elasti-daddy/blob/02e51b7a194cc933c5e6cd4044ac7c6f270d67e8/Mapping/feed_me_mapping.json#L1-L31
	</details>


10. Update Ingest Pipeline (you can copy paste the following code into the Ingest pipeline section)
	<details>
	Now we will correct the formatting of the Timestamp of the date/time fields, split the `Medicine 💊` values into an array, and make the `Amount (ml/cc)` and `Duration` fields a type of long.

	https://github.com/nicpenning/Elasti-daddy/blob/02e51b7a194cc933c5e6cd4044ac7c6f270d67e8/Ingest%20Pipeline/feed_me_ingest.json#L1-L72
	</details>

11. Click Import!
	<details>

	https://github.com/nicpenning/Elasti-daddy/assets/5582679/b56ab7a4-8d7a-4d23-8562-914cb7b1d81f
	
	Now the data is in Elasticsearch and ready to be visualized. If the data was successfully imported, now is the time to import a dashboard that I put together to finalize this initial blog post!
	</details>

12. Import Kibana Dashboard
	<details>
	Navigate to the `Stack Management` section of Kibana:
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/e287adff-a8bb-4640-beca-0c67a0262ce0)
	
	Then go to `Saved Objects`:
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/d8e2d788-af45-4556-a595-67001b6b60bf)
	
	Then click `Import`.
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/f67c66b8-e16c-4025-8587-fe5c7a0c7c50)
	
	Then upload the `Feed Analysis.ndjson` dashboard that has been provided [here](https://github.com/nicpenning/Elasti-daddy/blob/main/Kibana/Feed%20Analysis.ndjson).
	
	Lastly, click Import.
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/4f746709-18ba-44ad-8b33-f4b5154e1608)
	
	If successful it is time to look at our data!
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/4d3604c6-28d5-4966-9453-08743acc79a8)
	</details>

13. Click on `Feed Analysis` dashboard
	<details>
	
	![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/667cbc74-6285-41ec-9294-ef3b4a2be65e)
	
	You may have to update the time slider:
	
	https://github.com/nicpenning/Elasti-daddy/assets/5582679/e4623daa-2ccf-436e-bb18-10cb837d9040
	</details>
# 🪄Ta-da!

https://github.com/nicpenning/Elasti-daddy/assets/5582679/c359b5c6-d5f1-4c14-9ff6-ad63ed194765

To summarize, we took a CSV file and imported it into Elasticsearch using the Kibana Upload feature and then imported a Dashboard to look at the data. The next step is to build an integration that will make it easier to ingest this data with the Elastic agent as well as automatically import the pipelines and dashboard.

This concludes using the Kibana Upload tool to ingest the prepared CSV file for analysis.
