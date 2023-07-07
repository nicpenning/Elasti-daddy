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
napsta@el33t-b00k-1:~$ elastic-package create package
2023/07/06 21:43:15  INFO New version is available - v0.83.2. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.83.2
Create a new package
? Package name: elasti_daddy
? Version: 0.0.1
? Description: This is a package for preparing and analyzing motherhood and fatherhood data for taking care of a baby. The aim is to learn how Elastic Integrations are developed and deployed. Sample data includes breastfeeding, bottle feeding (milk or formula), milk extraction, etc..
? Categories: custom
? Kibana version constraint: ^8.7.1
? Required Elastic subscription: basic
? Github owner: nicpenning/Elasti-daddy
New package has been created: elasti_daddy
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

This is where the documentation from Elastic falls short as this is not covered during the sample development of an integration flow.

Let us try to clone this Elasti-daddy repo and place the integration we created inside of it to see if we have any luck. We will create a new directory called GitHub since our repository has the same name as our integration. Then we will create a folder called Integration in our Elasti-daddy repository. Lastly, we will copy the integration into that directory and then try our build again.

```bash
cd ~/
napsta@el33t-b00k-1:~$ mkdir GitHub
napsta@el33t-b00k-1:~$ cd GitHub
napsta@el33t-b00k-1:~/GitHub$ git clone https://github.com/nicpenning/Elasti-daddy.git
Cloning into 'Elasti-daddy'...
remote: Enumerating objects: 335, done.
remote: Counting objects: 100% (213/213), done.
remote: Compressing objects: 100% (196/196), done.
remote: Total 335 (delta 159), reused 17 (delta 17), pack-reused 122
Receiving objects: 100% (335/335), 98.65 KiB | 711.00 KiB/s, done.
Resolving deltas: 100% (178/178), done.
napsta@el33t-b00k-1:~/GitHub$ cd Elasti-daddy/
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy$ mkdir Integration
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy$ cp ~/Elasti-daddy/ ~/GitHub/Elasti-daddy/Integration/ -r
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy$ cd Integration/Elasti-daddy/
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/Elasti-daddy$ elastic-package build
2023/07/06 22:45:37  INFO New version is available - v0.83.2. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.83.2
Build the package
Error: building package failed: invalid content found in built zip package: found 1 validation error:
   1. file "/home/napsta/GitHub/Elasti-daddy/build/packages/Elasti-daddy-0.0.1.zip/manifest.yml" is invalid: field name: Does not match pattern '^[a-z0-9_]+$'

```

The error above is due to the fact that we used the `Package Name` as `Elasti-daddy` which is invalid because it must be all lowercase with the option of numbers and an underscore. Instead we had an uppercase character and a dash which caused this build to fail.

To correct this, we will need to rename the directory and adjust the manifest file. However, it will be quicker to remove our integration and re-create our integration and data stream. Start in our GitHub/Elasti-daddy/Integration directory and perform the following:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration$ rm Elasti-daddy -r
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration$ elastic-package create package
2023/07/06 23:39:48  INFO New version is available - v0.83.2. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.83.2
Create a new package
? Package name: elasti_daddy
? Version: 0.0.1
? Description: This is a package for preparing and analyzing motherhood and fatherhood data for taking care of a baby. The aim is to learn how Elastic Integrations are developed and deployed. Sample data includes breastfeeding, bottle feeding (milk or formula), milk extraction, etc..
? Categories: custom
? Kibana version constraint: ^8.7.1
? Required Elastic subscription: basic
? Github owner: nicpenning/Elasti-daddy
New package has been created: elasti_daddy
Done

napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package create data-stream
2023/07/06 23:42:12  INFO New version is available - v0.83.2. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.83.2
Create a new data stream
? Data stream name: feed_me
? Data stream title: Feed Me
? Type: logs
New data stream has been created: feed_me
Done
```

Now we should see `elasti_daddy` as our integration name now for the directory:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/075ae2ec-b870-4a12-8462-dfd7cebbeb6d)

Let us go into the new directory and try our build again.

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package build
2023/07/06 23:54:02  INFO New version is available - v0.83.2. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.83.2
Build the package
Package built: /home/napsta/GitHub/Elasti-daddy/build/packages/elasti_daddy-0.0.1.zip
Done
```

Success! Now let us see if the integration shows up in our Kibana instance. To do this, we need to refresh our `package-repository` by running `elastic-package stack up -v -d --services package-registry` from our integration directory:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package stack up -v -d --services package-registry
...snipped for brevity...
elastic-package-stack_package-registry_1 is up-to-date
Starting elastic-package-stack_package-registry_is_ready_1 ... done
Done
```

Navigate to Kibana and go to the Integrations page, select `Display Beta Integrations` (since we are using the version number 0.0.1), and then search for Elasti-daddy:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/2369c2a5-d4dd-4863-888d-9746e1ac130c)

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/cb163083-7111-468a-a4c7-644f5127a003)

If it worked, then we should see our integration. Let us click on that integration to see more details!

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/21e59c08-45bc-4828-b658-30093d3e4760)

This is great! Our base integration is there but there is a lot of work that needs to be done to make this integration usable.

If we click on `Add Elasti-daddy` in the right hand corner we can see other details that we still need to modify.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/36b6241b-4f3e-4f2b-a7e1-2a6a59f16070)

Let us move on to tweaking a few core settings to make this integration usable with our data set.

</details>

#### 4. Change core settings for Integration to ingest our data
<details>

To start, let us fix up the following:
- Data Stream
- Field Mappings
- Ingest Pipeline
- Read Me

1. Data Stream
We will start with the Data Stream manifest file by updating the content from the default text that current looks like this:

 ![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/80d97928-e0a4-4f87-92c7-e58fd26061ea)

to:

```yaml
title: "Feed Me"
type: logs
streams:
  - input: logfile
    title: Feed Me Logfile
    description: Collect events from the feed_me.csv
    vars:
      - name: paths
        type: text
        title: Paths
        multi: true
        show_user: true
        required: true
        default:
          - ~/feed_me.csv
```

⚠️ Note: We added `show_user: true` and `required: true` as additional settings so we can make sure these take effect in the Kibana UI.

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/data_stream/feed_me$ nano manifest.yml
```

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/10a7654f-9fd8-414d-a5af-d3e9fd7615d4)

Save the changes by hitting Crtl-X then `Y` and hit enter.

We will also update the `policy_templates` section of the `manifest.yml` file that is found in the root of the integration to this:

```yaml
policy_templates:
  - name: feed_me
    title: Feed me
    description: Collect events for the Elasti-daddy project.
    inputs:
      - type: logfile
        title: Collect events from feed_me.csv
        description: Collect events from the feed_me.csv for the Elasti-daddy project
```

Now, let us run our build again and restart our package registry and Elastic stack to see our changes.


```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package build
2023/07/07 01:43:12  INFO New version is available - v0.83.2. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.83.2
Build the package
Package built: /home/napsta/GitHub/Elasti-daddy/build/packages/elasti_daddy-0.0.1.zip
Done
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package stack down
...snipped for brevity...
Done
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package stack up -v -d --version=8.8.1
...snipped for brevity...
Done
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package stack up -v -d --services package-registry
...snipped for brevity...
Done
```

After restarting the package-registry, let us go check Kibana for our changes:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/92f425e7-bde7-4276-bd57-aa6d40b482b2)

As you can see above, our integration is now defaulting to our settings that we adjusted in the data stream manifest.yml file!

2. Field Mappings

Okay, let us move on to updating the field mappings by adding a fields.yml file to our data stream.

These are all of the fields, their appropriate mapping, and the file name that we will need to add that into for the integration:

```
@timestamp : date : base-fields.yml
Amount (ml/cc) : long : fields.yml
Count : long : fields.yml
Duration : long : fields.yml
End Time : date : fields.yml
Medicine 💊 : keyword : fields.yml
Side : keyword : fields.yml
Start Time : date : fields.yml
Type : keyword : fields.yml
```

We will also need to add a description to each field so in the end, each field will need to be added to the appropiate file in this format:

```
- name: <field_name
  type: <mapping_type>
  description: <description of what the field is useful for>
```

Since `@timestamp` is already included by default in the `base-fields.yml`, then we just need to create the `fields.yml` with the following text:

```
- name: 'Amount (ml/cc)'
  type: long
  description: The volume of substance contained.
- name: 'Count'
  type: long
  description: The total number of items.
- name: 'Duration'
  type: long
  description: The amount of time between the start time and end time.
- name: 'End Time'
  type: date
  description: The end time of the event.
- name: 'Medicine 💊'
  type: keyword
  description: The type of medicine consumed.
- name: 'Side'
  type: keyword
  description: The left or right breast which was fed from.
- name: 'Start Time'
  type: date
  description: The start time of the event.
- name: 'Type'
  type: kyword
  description: The type of event that has occurred.
```

3. Ingest Pipelines

4. Read Me
</details>
