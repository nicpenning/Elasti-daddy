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

3. Drag and Drop or Browse to the `Feed Me.csv` file to upload
![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/4160bfd3-24c1-4f50-a98e-c2abec534887)

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/8aa7bcbe-786b-4282-8557-54a71825e5e7)

4. Now that we are at he point we can tweak our ingest of the file I want to point out a few settings that we will need to set to make sure we get the data into Elasticsearch that will be usable for our visualations and search.

To be continued...
