# Blog Post #1. 
## Data ingest example with Upload feature in Kibana 
To begin this series of blog posts, I would like to start on how I got here. If that is interesting, read on, otherwise [click here to skip to the data ingest steps!](https://github.com/nicpenning/Elasti-daddy/blob/main/Blog%20Posts/Blog%20Post%20%231.%20Data%20Ingest.md#data-ingest-via-kibana-upload)

My wife and I have recently been blessed with a new baby and one thing that was highly recommended was tracking the amount of food our baby was eating.
This had us jotting down the feeding times for our little one which consisted of a `start time`, `end time`, calculated `duration` in minutes, and lastly the 
`side` the baby nursed on. We then added how often mom and the baby took medicine, when milk was getting extracted for later feedings, and even how
much formula we supplemented with the other feedings. In summary, this is a tracking mechanism, however, when we reviewed the pages and pages of notes
it was hard to answer the questions: `How long on average is our baby going between feedings?`, `What times of day is the baby most likely to feed?`,
and `Can we actually recognize any patterns that could better prepare us for the day?` 

My personal goal is to see if I can answer the questions above by taking the data that we have hand written down, placing it into a document that will 
be saved as a CSV and then ingesting into Elasticsearch for analysis in Kibana. 

#### Simplified Process
`Handwriting -> Spreadsheet -> Feed Me.csv -> Elasticsearch (Ingest) -> Kibana (Visualize)`

Along this journey I figured it would be a great opportunity to understand
how Elastic Integrations work so that I can build others down the road that are more practical that the world can enjoy. While I plan to have a fully
functional integration at the end of this project, it `will not be published` since it is a very niche integration that only those that follow this project will see.

Since I will cover standing up the Elastic stack in a later blog post, I will skip that here and go straight to the data ingest and a glimpse into the end goal.

So without futher ado, let's get into it! (Note: I will be using Elastic Stack version 8.8.1)

#### Data Ingest via Kibana Upload
1. Start up the Elastic Stack using the elastic-package tool (covered in later blog post)

`elastic-package stack up -d -v --version=8.8.1`

Output
```shell
napsta@el33t-b00k-1:~$ elastic-package stack up -d -v --version=8.8.1
2023/06/17 18:11:38 DEBUG Enable verbose logging
Boot up the Elastic stack
Using profile /home/napsta/.elastic-package/profiles/default.
Remember to load stack environment variables using 'eval "$(elastic-package stack shellinit)"'.
Elasticsearch host: https://127.0.0.1:9200
Kibana host: https://127.0.0.1:5601
Username: elastic
Password: changeme
Local package-registry will serve packages from these sources:
- Proxy to https://epr.elastic.co
2023/06/17 18:11:39 DEBUG running command: /usr/local/bin/docker-compose version --short
2023/06/17 18:11:39 DEBUG Determined Docker Compose version: 2.18.1
2023/06/17 18:11:39 DEBUG running command: /usr/local/bin/docker-compose -f /home/napsta/.elastic-package/profiles/default/stack/snapshot.yml -p elastic-package-stack build
[+] Building 0.2s (8/8) FINISHED
 => [package-registry internal] load .dockerignore                                                                                         0.0s
 => => transferring context: 2B                                                                                                            0.0s
 => [package-registry internal] load build definition from Dockerfile.package-registry                                                     0.0s
 => => transferring dockerfile: 421B                                                                                                       0.0s
 => [package-registry internal] load metadata for docker.elastic.co/package-registry/package-registry:v1.20.0                              0.0s
 => [package-registry 1/3] FROM docker.elastic.co/package-registry/package-registry:v1.20.0                                                0.0s
 => [package-registry internal] load build context                                                                                         0.0s
 => => transferring context: 232B                                                                                                          0.0s
 => CACHED [package-registry 2/3] COPY profiles/default/stack/package-registry.yml /package-registry/config.yml                            0.0s
 => CACHED [package-registry 3/3] COPY stack/development/ /packages/development                                                            0.0s
 => [package-registry] exporting to image                                                                                                  0.0s
 => => exporting layers                                                                                                                    0.0s
 => => writing image sha256:1313851c397fa47eb313f9ca35dbcb27b1d21085ef732eb7de863432e2a9783d                                               0.0s
 => => naming to docker.io/library/elastic-package-stack-package-registry                                                                  0.0s
2023/06/17 18:11:39 DEBUG running command: /usr/local/bin/docker-compose version --short
2023/06/17 18:11:39 DEBUG Determined Docker Compose version: 2.18.1
2023/06/17 18:11:39 DEBUG running command: /usr/local/bin/docker-compose -f /home/napsta/.elastic-package/profiles/default/stack/snapshot.yml -p elastic-package-stack up -d
[+] Building 0.0s (0/0)
[+] Running 10/10
 ✔ Container elastic-package-stack-package-registry-1           Healthy                                                                    6.7s
 ✔ Container elastic-package-stack-elasticsearch-1              Healthy                                                                   33.9s
 ✔ Container elastic-package-stack-package-registry_is_ready-1  Started                                                                    7.1s
 ✔ Container elastic-package-stack-kibana-1                     Healthy                                                                   65.3s
 ✔ Container elastic-package-stack-elasticsearch_is_ready-1     Started                                                                   33.3s
 ✔ Container elastic-package-stack-fleet-server-1               Healthy                                                                   92.1s
 ✔ Container elastic-package-stack-kibana_is_ready-1            Started                                                                   66.0s
 ✔ Container elastic-package-stack-elastic-agent-1              Healthy                                                                  103.2s
 ✔ Container elastic-package-stack-fleet-server_is_ready-1      Started                                                                   92.6s
 ✔ Container elastic-package-stack-elastic-agent_is_ready-1     Started                                                                  103.6s
Done
```

2. Navigate to Kibana's home page and click on Upload a File
<details>
 
  1. Click on Elastic at the top of Kibana to get to the Welcome Page
 
  2. Click on Upload File
 
  3. This is a note that the URL you could navigate to is: `https://127.0.0.1:5601/app/home#/tutorial_directory/fileDataViz`
 
</details>

![Screenshot 2023-06-18 134546](https://github.com/nicpenning/Elasti-daddy/assets/5582679/6de30cfc-47a5-4a1c-9c7e-83c18dbfb9dd)

3. Drag and Drop or Browse to the `Feed Me.csv` file to upload.

[Feed Me.csv found here](https://github.com/nicpenning/Elasti-daddy/blob/main/Data/Feed%20Me.csv)
![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/4160bfd3-24c1-4f50-a98e-c2abec534887)

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/8aa7bcbe-786b-4282-8557-54a71825e5e7)

4. Click Override Settings to Tweak Settings for Import

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

5. Click Apply

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/d5a6a643-0f42-4728-bd0a-fc692c390fc4)

6. Click Import (at the bottom of the page)

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/58cb4560-17f1-4e2e-b28a-1131dcea28a4)

 - ⚠️ Note: The data will not be imported yet but rather you will be taking to the next step of the import process. This is a little confusing so I put in an [issue](https://github.com/elastic/kibana/issues/159826) for Kibana here to see if Elastic will make that button say *Next* instead.

7. Click Advanced

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/3dcc9817-57f2-45ef-993f-3cd72b09a980)

We are using the Advanced option for a couple of reasons:
 - Ensure we get Date mappings for `Start Time` and `End Time`
 - Ensure we apply the correct time zone for the data, tweak the `Medicine 💊` field to be an array, and make sure that the `Amount (ml/cc)` and `Duration` fields are a long.


8. Set Index name and Data view name to `feed_me`

This will be the name of our log source we will use later.
![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/448efe44-ebfa-464e-b5cc-0ee95194ad04)

9. Update Mapping (you can copy paste the following code into the Mappings section)

Now we must update the `Start Time` and `End Time` from type `keyword` to type `date`, so the Mappings JSON looks like this:
```json
{
  "properties": {
    "@timestamp": {
      "type": "date"
    },
    "Amount (ml/cc)": {
      "type": "long"
    },
    "Count": {
      "type": "long"
    },
    "Duration": {
      "type": "long"
    },
    "End Time": {
      "type": "date"
    },
    "Medicine 💊": {
      "type": "keyword"
    },
    "Side": {
      "type": "keyword"
    },
    "Start Time": {
      "type": "date"
    },
    "Type": {
      "type": "keyword"
    }
  }
}
```

10. Update Ingest Pipeline (you can copy paste the following code into the Ingest pipeline section)

Now we will correct the formatting of the Timestamp of the date/time fields, split the `Medicine 💊` values into an array, and make the `Amount (ml/cc)` and `Duration` fields a type of long.

```json
{
  "description": "Ingest pipeline created by text structure finder",
  "processors": [
  {
    "csv": {
      "field": "message",
      "target_fields": [
        "Medicine 💊",
        "Start Time",
        "End Time",
        "Duration",
        "Side",
        "Type",
        "Count",
        "Amount (ml/cc)"
      ],
      "ignore_missing": false,
      "trim": true
    }
  },
  {
    "date": {
      "field": "Start Time",
      "formats": [
        "M/d/yyyy H:mm"
      ],
      "target_field": "@timestamp",
	    "timezone": "America/Chicago"

    }
  },
  {
    "date": {
      "field": "End Time",
      "formats": [
        "M/d/yyyy H:mm"
      ],
      "target_field": "End Time",
	    "timezone": "America/Chicago"
    }
  },
  {
    "date": {
      "field": "Start Time",
      "formats": [
        "M/d/yyyy H:mm"
      ],
      "target_field": "Start Time",
	    "timezone": "America/Chicago"
    }
  },
  {
    "convert": {
      "field": "Amount (ml/cc)",
      "type": "long",
      "ignore_missing": true
    }
  },
  {
      "split": {
        "field": "Medicine 💊",
        "separator": ",",
        "ignore_missing": true
      }
  },
  {
    "remove": {
      "field": "message"
    }
  }
]
}
```
11. Click Import!

https://github.com/nicpenning/Elasti-daddy/assets/5582679/b56ab7a4-8d7a-4d23-8562-914cb7b1d81f

Now the data is in Elasticsearch and ready to be visualized. If the data was successfully imported, now is the time to import a dashboard that I put together to finalize this initial blog post!

12. Import Kibana Dashboard

Navigate to the `Stack Management` section of Kibana:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/e287adff-a8bb-4640-beca-0c67a0262ce0)

Then go to `Saved Objects`:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/d8e2d788-af45-4556-a595-67001b6b60bf)

Then click `Import`.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/f67c66b8-e16c-4025-8587-fe5c7a0c7c50)

Then upload the `Feed Analysis.ndjson` dashboard that has been provided [here](https://github.com/nicpenning/Elasti-daddy/blob/main/Kibana/Feed%20Analysis.ndjson).

and click Import!

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/4f746709-18ba-44ad-8b33-f4b5154e1608)

If successful it is time to look at our data!

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/4d3604c6-28d5-4966-9453-08743acc79a8)


To be continued...
