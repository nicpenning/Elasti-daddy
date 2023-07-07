# Blog Post #5.
# 🚧 Under Construction 🏗️
## Creating a new custom integration (package)

Now that we have a good understanding of integrations, let us dive into creating the foundation for
our integration unsing the `elastic-package` tool. You can find Elastic's official `Contributing Guide`
[here](https://github.com/elastic/integrations/blob/main/CONTRIBUTING.md). But I will run us through 
creating an integration in this blog post. This run through will be simliar to the `Developer workflow: 
build and test integration` found [here](https://github.com/elastic/integrations/blob/main/docs/developer_workflow_design_build_test_integration.md)
except with a few more details along the way.

#### 1. Creating the Elasti-Daddy Integration
<details>
  
We start by running `elastic-package create package Elasti-daddy` in our Ubuntu on Windows terminal and fill in
the prompts:

```bash
napsta@el33t-b00k-1:~$ elastic-package create package Elasti-daddy
2023/07/06 21:43:15  INFO New version is available - v0.83.2. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.83.2
Create a new package
? Package name: Elasti-daddy
? Version: 0.0.1
? Description: This is a package for preparing and analyzing motherhood and fatherhood data for taking care of a baby.
The aim is to learn how Elastic Integrations are developed and deployed. Sample data includes breastfeeding, bottle
feeding (milk or formula), milk extraction, etc..
? Categories: custom
? Kibana version constraint: ^8.7.1
? Required Elastic subscription: basic
? Github owner: nicpenning/Elasti-daddy
New package has been created: Elasti-daddy
Done
```

What happened in the background was that a new directory called `Elasti-daddy` was created from where we ran that command from.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/ffa9d2c9-6a6e-40aa-ac79-39c9d45d4cf7)

Here is what was created inside of that directory:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/26240313-0d9c-4562-a89f-d53f19f209ce)

</details>

#### 2. Creating the data stream for our Integration

<details>

At this point we have the bare bones of the integration, but what we really need is the data stram, which is the
what the data will be indexed into. This will include templates that can include mappings and ingest pipelines.
Let us create the `feed_me` data stream that we will index our data into:

```bash
napsta@el33t-b00k-1:~/Elasti-daddy$ elastic-package create data-stream
2023/07/06 22:02:54  INFO New version is available - v0.83.2. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.83.2
Create a new data stream
? Data stream name: feed_me
? Data stream title: Feed Me
? Type: logs
New data stream has been created: feed_me
Done
```

We just created a data stream with the name of `feed_me` and a title of `Feed Me`. We also used the `logs` data stream type.

```bash
napsta@el33t-b00k-1:~/Elasti-daddy$ ls
LICENSE.txt  changelog.yml  data_stream  docs  img  manifest.yml
napsta@el33t-b00k-1:~/Elasti-daddy$ cd data_stream/
napsta@el33t-b00k-1:~/Elasti-daddy/data_stream$ ls
feed_me
napsta@el33t-b00k-1:~/Elasti-daddy/data_stream$ cd feed_me/
napsta@el33t-b00k-1:~/Elasti-daddy/data_stream/feed_me$ ls
agent  elasticsearch  fields  manifest.yml
```

Above we will find that a new directory called `data_stream` was created. When we navigated into that directory
we found the name of our data stream `feed_me` as another directory. Diving further into the `data_stream` directory we found
a set of new files and directories that start to unravel more of the needed components of our integration which are:

```
agent/stream/stream.yml/hbs : This is the stream for the agent which may be used for the Elastic Agent policy template, but I am unsure
elasticsearch/ingest_pipeline/default.yml : Contains our pipeline that we will use to ingest the data
fields/base-fields.yml : Which are all of the fields that will be used in our data set and their respective mappings
manifest.yml : This is used for customizing the integration settings which will be covered later.
```

</details>

#### 3. Build and test what we have so far

<details>

Now that we have created the integration and the data stream for the integration, let us see what we have by building this integration
and adding this to our test package registry so we can see it live in Kibana.

First we will open a new terminal in our Ubuntu on Windows and start up our stack if we haven't yet:

`elastic-package stack up -v -d --version=8.8.1`

Then we will go back to our original terminal and run the build command:

`elastic-package build`

When we run this command, we have receive the following error:

```bash
napsta@el33t-b00k-1:~/Elasti-daddy$ elastic-package build
Error: can't prepare build directory: can't create new build directory: package can be only built inside of a Git repository (.git folder is used as reference point)
```

This is where the documenation from Elastic falls short as this is not covered during the sample development of an integration flow.


..To be continued...

</details>

