# Blog Post #7.
## Update Overview (README.md) Text

The main page of the Elasti-daddy Integration is still using the sample text that we want to update and make more accurate. This blog post outlines
the process to get that updated to something more usable.

This is what the Integration looks like now:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/6268bc5b-7165-4bce-87be-039be3349334)

Let us dive right in and get this cleaned up!

#### 1. Create the README.md Template
<details>

We will start off by locating the README.md file that is in the `elasti_daddy/docs/` directory and taking a peek inside.

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ cd docs
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/docs$ ls
README.md
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/docs$ nano README.md
<!-- Use this template language as a starting point, replacing {placeholder text} with details about the integration. -->
<!-- Find more detailed documentation guidelines in https://github.com/elastic/integrations/blob/main/docs/documentation_guidelines.md -->

# Elasti-daddy

<!-- The Elasti-daddy integration allows you to monitor {name of service}. {name of service} is {describe service}.

Use the Elasti-daddy integration to {purpose}. Then visualize that data in Kibana, create alerts to notify you if something goes wrong, an>

For example, if you wanted to {sample use case} you could {action}. Then you can {visualize|alert|troubleshoot} by {action}. -->

## Data streams

<!-- The Elasti-daddy integration collects {one|two} type{s} of data streams: {logs and/or metrics}. -->

<!-- If applicable -->
<!-- **Logs** help you keep a record of events happening in {service}.
Log data streams collected by the {name} integration include {sample data stream(s)} and more. See more details in the [Logs](#logs-refere>

<!-- If applicable -->
<!-- **Metrics** give you insight into the state of {service}.
Metric data streams collected by the {name} integration include {sample data stream(s)} and more. See more details in the [Metrics](#metri>

<!-- Optional: Any additional notes on data streams -->

## Requirements
...snipped for brevity...
```

Keep in mind that the documentation uses markdown language. You can follow the documentation on how to use markdown [here](https://www.markdownguide.org/).

The current README.md that exists was automatically generated when we created the integration. We will create some new files and directories to generate
a new README.md file.

What we want in the end is a `README.md` template that has most of the text that we want dispalyed on the Overview page but also have the ability to show
a sample event in JSON and also the fields available for the integration. Fortunately, the `elastic-package build` mechanism will generate the sample event
and the fields when specific reference text exists inside of the README.md template.

To do this, we need to create a new directory with a new README.md file in the root of the elasti-daddy Integration called `_dev/build/docs`:

```
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ mkdir _dev/build/docs
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ cd _dev/build/docs
```

Then let's create our new `README.md` and use the text below (after the `nano README.md` command to create the file):

```
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/_dev/build/docs$ nano README.md
```

```
# Elasti-daddy

This integration was developed as a project for preparing and analyzing motherhood and fatherhood data for taking care of a baby. The aim is to learn how Elastic Integrations are developed and deployed. Sample data includes breastfeeding, bottle feeding (milk or formula), milk extraction, etc..

## Data streams

The Elasti-daddy integration collects one type of data streams: logs.

## Requirements

You need any of the `Feed Me.csv` files found at the Elasti-daddy project site [here](https://github.com/nicpenning/Elasti-daddy/tree/main/Data)

## History

For all the details on the project, please visit the [Elasti-daddy project page](https://github.com/nicpenning/Elasti-daddy).
For step-by-step instructions on how to set up an integration, see the

#### Example

{{event "feed_me"}}

{{fields "feed_me"}}
```

You will notice the two reference texts that start with `{{` and end with `}}`. This is the part of the README.md that will be generated from files
within the integration. The `{{fields "feed_me"}}` will use all of the fields found in each .yml file in the directory :
`/elasti_daddy/data_stream/feed_me/fields` which is currently `base-fields.yml` and `fields.yml`:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/data_stream/feed_me/fields$ ls
base-fields.yml  fields.yml
```

The `{[event "feed_me"}}` reference text, however, will pull from a file called `sample_event.json` which we have not created yet.

We will create our `sample_event.json` file now in the `elasti_daddy/data_stream/feed_me` directory and use the text below (after 
the `nano README.md` command):

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/data_stream/feed_me/fields$ cd ..
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/data_stream/feed_me$ nano sample_event.json
```

```JSON
{
  "container": {
    "id": "Data"
  },
  "agent": {
    "name": "el33t-b00k-1",
    "id": "362d573c-5198-42b0-b5e3-5eea158373d3",
    "type": "filebeat",
    "ephemeral_id": "fc552cf0-f45d-4728-879e-98c2254c6add",
    "version": "8.8.1"
  },
  "log": {
    "file": {
      "path": "/home/napsta/GitHub/Elasti-daddy/Data/Feed Me 7 Weeks.csv"
    },
    "offset": 60533
  },
  "elastic_agent": {
    "id": "362d573c-5198-42b0-b5e3-5eea158373d3",
    "version": "8.8.1",
    "snapshot": false
  },
  "Duration": 18,
  "Side": "Left",
  "input": {
    "type": "log"
  },
  "Type": "Breastfeeding",
  "@timestamp": "2023-07-10T19:20:00.000-05:00",
  "ecs": {
    "version": "8.0.0"
  },
  "End_Time": "2023-07-10T19:35:00.000-05:00",
  "Start_Time": "2023-07-10T19:20:00.000-05:00",
  "data_stream": {
    "namespace": "default",
    "type": "logs",
    "dataset": "elasti_daddy.feed_me"
  },
  "host": {
    "hostname": "el33t-b00k-1",
    "os": {
      "kernel": "5.10.102.1-microsoft-standard-WSL2",
      "codename": "jammy",
      "name": "Ubuntu",
      "type": "linux",
      "family": "debian",
      "version": "22.04.2 LTS (Jammy Jellyfish)",
      "platform": "ubuntu"
    },
    "containerized": false,
    "ip": [
      "172.31.99.180"
    ],
    "name": "el33t-b00k-1",
    "mac": [
      "00-15-5D-65-A9-94"
    ],
    "architecture": "x86_64"
  },
  "event": {
    "agent_id_status": "verified",
    "ingested": "2023-07-10T22:07:47Z",
    "timezone": "-05:00",
    "dataset": "elasti_daddy.feed_me"
  }
}
```

</details>

#### 2. Test README.md Build
<details>

Now that we have the README.md template created in our new directory and have created our sample_event.json file, it is time to
run the `elastic-package test` command to make sure the README.md file can properly be generated. This will also check to make sure
our sample event has every field accounted for.

From the root directory of our integration, let us test the package:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package test
2023/07/11 22:57:41  INFO New version is available - v0.84.0. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.84.0
Run test suite for the package
Run static tests for the package
--- Test results for package: elasti_daddy - START ---
FAILURE DETAILS:
elasti_daddy/feed_me Verify sample_event.json:
[0] field "container.id" is undefined
[1] field "ecs.version" is undefined
[2] field "input.type" is undefined
[3] field "log.file.path" is undefined
[4] field "log.offset" is undefined


╭──────────────┬─────────────┬───────────┬──────────────────────────┬────────────────────────────────────────────┬──────────────╮
│ PACKAGE      │ DATA STREAM │ TEST TYPE │ TEST NAME                │ RESULT                                     │ TIME ELAPSED │
├──────────────┼─────────────┼───────────┼──────────────────────────┼────────────────────────────────────────────┼──────────────┤
│ elasti_daddy │ feed_me     │ static    │ Verify sample_event.json │ FAIL: one or more errors found in document │    608.061µs │
╰──────────────┴─────────────┴───────────┴──────────────────────────┴────────────────────────────────────────────┴──────────────╯
--- Test results for package: elasti_daddy - END   ---
Done
Error: one or more test cases failed
```

Interesting! So when we ran our test with the sample data I provided, it appears that some additional fields got added during the ingestion 
of our data. You can see the following fields have not been accounted for in any of the .yml files in the fields directory because that are "undefined":

```
[0] field "container.id" is undefined
[1] field "ecs.version" is undefined
[2] field "input.type" is undefined
[3] field "log.file.path" is undefined
[4] field "log.offset" is undefined
```

To correct this, let us add these to our current fields .yml files. The fields that were added somewhere along the way appear to be a part of the
Elastic Common Schema (ECS). This is a common schema for field mappings and their respective definitions.You can learn more about ECS [here](https://www.elastic.co/guide/en/ecs/current/ecs-reference.html). Since these are ECS fields, there is a special ecs.yml file we need to create
that will contain a shortcut reference to what these fields must be mapped to. For example, `container.id` is found [here](https://www.elastic.co/guide/en/ecs/current/ecs-container.html#field-container-id) in their documentation and states that this field must be a
`type: keyword`. Instead of adding to any of our fields.yml with this format:
```yaml
- name: 'container.id'
  type: keyword
  description: This is the ID of the container.
```

We get to instead add this to a new file called `ecs.yml` in our fields directory and place it in the file like so:

```
- external: ecs
  name: container.id
```

This will make sure it uses the current up-to-date field mapping type and description from the ECS standards.

So to use all of these ECS fields, let us create the ecs.yml file in the fields directory:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ nano data_stream/feed_me/fields/ecs.yml
```

```
- external: ecs
  name: container.id
- external: ecs
  name: ecs.version
- external: ecs
  name: input.type
- external: ecs
  name: log.file.path
- external: ecs
  name: log.offset
```

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/05a07edc-8f6e-4626-8d4a-7c7df9d19a61)

Now, let us run our test again:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package test
2023/07/11 23:13:02  INFO New version is available - v0.84.0. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.84.0
Run test suite for the package
Run system tests for the package
--- Test results for package: elasti_daddy - START ---
No test results
--- Test results for package: elasti_daddy - END   ---
Done
Run asset tests for the package
--- Test results for package: elasti_daddy - START ---
╭──────────────┬─────────────┬───────────┬───────────────────────────────────────────────────────────┬────────┬──────────────╮
│ PACKAGE      │ DATA STREAM │ TEST TYPE │ TEST NAME                                                 │ RESULT │ TIME ELAPSED │
├──────────────┼─────────────┼───────────┼───────────────────────────────────────────────────────────┼────────┼──────────────┤
│ elasti_daddy │ feed_me     │ asset     │ index_template logs-elasti_daddy.feed_me is loaded        │ PASS   │      1.222µs │
│ elasti_daddy │ feed_me     │ asset     │ ingest_pipeline logs-elasti_daddy.feed_me-0.0.1 is loaded │ PASS   │        110ns │
╰──────────────┴─────────────┴───────────┴───────────────────────────────────────────────────────────┴────────┴──────────────╯
--- Test results for package: elasti_daddy - END   ---
Done
Run pipeline tests for the package
--- Test results for package: elasti_daddy - START ---
No test results
--- Test results for package: elasti_daddy - END   ---
Done
Run static tests for the package
--- Test results for package: elasti_daddy - START ---
╭──────────────┬─────────────┬───────────┬──────────────────────────┬────────┬──────────────╮
│ PACKAGE      │ DATA STREAM │ TEST TYPE │ TEST NAME                │ RESULT │ TIME ELAPSED │
├──────────────┼─────────────┼───────────┼──────────────────────────┼────────┼──────────────┤
│ elasti_daddy │ feed_me     │ static    │ Verify sample_event.json │ PASS   │   1.013687ms │
╰──────────────┴─────────────┴───────────┴──────────────────────────┴────────┴──────────────╯
--- Test results for package: elasti_daddy - END   ---
Done
```

Success! Now that our build is passing, we can move on to build the integration and deploy it to see how it turns out.

</details>

#### 3. Build and Deploy
<details>

We will build our integration like we have before so that we can go ahead and get it deployed into our stack.

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package build
2023/07/11 23:16:31  INFO New version is available - v0.84.0. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.84.0
Build the package
Error: updating files failed: updating readme file README.md failed: rendering Readme failed: executing template failed: template: README.md:26:2: executing "README.md" at <fields "feed_me">: error calling fields: collecting fields files failed: visiting fields failed: can't import field: importing external field "container.id": external fields not allowed because dependencies file "_dev/build/build.yml" is missing
```

Looks like we have an error due to using the newly created external fields. The error leads us to believe that we need a `_dev/build/build.yml` file that will
contain some dependencies. 

To correct this, I figured out that the following text is needed inside of the `build.yml` file:

```
dependencies:
  ecs:
    reference: git@v8.0.0
```

So let us go ahead and create that file with that text:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ nano _dev/build/build.yml
```

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/7c4e4502-28cd-4579-b925-bc44622797e2)

Now let us try and build again:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package build
2023/07/12 01:19:25  INFO New version is available - v0.84.0. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.84.0
Build the package
Error: updating files failed: updating readme file README.md failed: rendering Readme failed: executing template failed: template: README.md:26:2: executing "README.md" at <fields "feed_me">: error calling fields: collecting fields files failed: visiting fields failed: can't import field: field definition not found in schema (name: input.type)
```

We have a new error stating that one of our fields is not valid. Looking back, I made the mistake thinking that `input.type` was an ECS field, but it was not.
So since it is not, we cannot place that field in the ecs.yml but instead will need find a different place to house that field. 

After looking for where this field type is used in another [official integration](https://github.com/elastic/integrations/blob/main/packages/windows/data_stream/powershell/fields/beats.yml), I found that it could live in the `beats.yml` file. So we will create that file and populate it with this `input.type` field. 

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ nano data_stream/feed_me/beats.yml
```

```
- name: input.type
  type: keyword
  description: Type of Filebeat input.
```

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/99067b9f-64b1-427e-bfa2-48e0ac525a71)

We also need to remove that `input.type` field from the current `data_stream/feed_me/ecs.yml`. Which will look like this:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ nano data_stream/feed_me/fields/ecs.yml
```

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/7e47068f-60b6-4a35-935b-8d2af4750a91)

Let us try our build again:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package build
2023/07/12 01:31:22  INFO New version is available - v0.84.0. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.84.0
Build the package
Error: updating files failed: updating readme file README.md failed: rendering Readme failed: executing template failed: template: README.md:26:2: executing "README.md" at <fields "feed_me">: error calling fields: collecting fields files failed: visiting fields failed: can't import field: field definition not found in schema (name: log.offset)
```

Now it turns out `log.offset` is not a valid ECS field either. I could not find a good example where this is being used in other integrations as a sample event
so I will instead remove it from the `ecs.yml` and from our sample event.

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ nano data_stream/feed_me/fields/ecs.yml
```

```
- external: ecs
  name: container.id
- external: ecs
  name: ecs.version
- external: ecs
  name: log.file.path
```

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ nano data_stream/feed_me/sample_event.json
```

```
{
  "container": {
    "id": "Data"
  },
  "agent": {
    "name": "el33t-b00k-1",
    "id": "362d573c-5198-42b0-b5e3-5eea158373d3",
    "type": "filebeat",
    "ephemeral_id": "fc552cf0-f45d-4728-879e-98c2254c6add",
    "version": "8.8.1"
  },
  "log": {
    "file": {
      "path": "/home/napsta/GitHub/Elasti-daddy/Data/Feed Me 7 Weeks.csv"
    }
  },
  "elastic_agent": {
    "id": "362d573c-5198-42b0-b5e3-5eea158373d3",
    "version": "8.8.1",
    "snapshot": false
  },
  "Duration": 18,
  "Side": "Left",
  "input": {
    "type": "log"
  },
  "Type": "Breastfeeding",
  "@timestamp": "2023-07-10T19:20:00.000-05:00",
  "ecs": {
    "version": "8.0.0"
  },
  "End_Time": "2023-07-10T19:35:00.000-05:00",
  "Start_Time": "2023-07-10T19:20:00.000-05:00",
  "data_stream": {
    "namespace": "default",
    "type": "logs",
    "dataset": "elasti_daddy.feed_me"
  },
  "host": {
    "hostname": "el33t-b00k-1",
    "os": {
      "kernel": "5.10.102.1-microsoft-standard-WSL2",
      "codename": "jammy",
      "name": "Ubuntu",
      "type": "linux",
      "family": "debian",
      "version": "22.04.2 LTS (Jammy Jellyfish)",
      "platform": "ubuntu"
    },
    "containerized": false,
    "ip": [
      "172.31.99.180"
    ],
    "name": "el33t-b00k-1",
    "mac": [
      "00-15-5D-65-A9-94"
    ],
    "architecture": "x86_64"
  },
  "event": {
    "agent_id_status": "verified",
    "ingested": "2023-07-10T22:07:47Z",
    "timezone": "-05:00",
    "dataset": "elasti_daddy.feed_me"
  }
}
```

Okay, now that we have taken care of that quirk. Let us build and see where we are at:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package build
2023/07/12 01:41:03  INFO New version is available - v0.84.0. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.84.0
Build the package
README.md file rendered: /home/napsta/GitHub/Elasti-daddy/Integration/elasti_daddy/docs/README.md
Error: building package failed: invalid content found in built zip package: found 1 validation error:
   1. item [beats.yml] is not allowed in folder [/home/napsta/GitHub/Elasti-daddy/build/packages/elasti_daddy-0.0.1.zip/data_stream/feed_me]
```

Apparently I made the mistake of putting the `beats.yml` in the wrong directory so let us move it to the fields directory and try again.

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ cd data_stream/feed_me/
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/data_stream/feed_me$ ls
agent  beats.yml  elasticsearch  fields  manifest.yml  sample_event.json
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/data_stream/feed_me$ mv beats.yml fields/
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/data_stream/feed_me$ elastic-package build
2023/07/12 01:46:08  INFO New version is available - v0.84.0. Download from: https://github.com/elastic/elastic-package/releases/tag/v0.84.0
Build the package
README.md file rendered: /home/napsta/GitHub/Elasti-daddy/Integration/elasti_daddy/docs/README.md
Package built: /home/napsta/GitHub/Elasti-daddy/build/packages/elasti_daddy-0.0.1.zip
Done
```

Success! Now we can recycle our package repository and see if our changes took in Kibana.

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package stack up -v -d --services package-registry
...snipped for brevity...
Done
```

🎉 It worked! We now have an updated and good looking Overview for the Integration that includes a sample event and the fields used for it.

https://github.com/nicpenning/Elasti-daddy/assets/5582679/39d00672-50d0-45ae-b88e-514ada7012bb

</details>

In summary, we were able to generate a README.md file that ultimately servers as the Overview text in the integration.

This concludes the blog post on properly updating the Overview for our integration.
