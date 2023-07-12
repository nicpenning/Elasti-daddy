# Blog Post #7.
# 🚧 Under Construction 🏗️
## Update Overview (README.md) Text

The main page of the Elasti-daddy Integration is still using the sample text that we want to update and make more accurate. This blog post outlines
the process to get that updated to something more usable.

This is what the Integration looks like now:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/6268bc5b-7165-4bce-87be-039be3349334)

Let us dive right in and get this cleaned up!

#### 1. Update the README
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

The current README.md that exists was automatically genereated when we created the integration. We will create some new files and directories to generate
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

An example event for `{data stream name}` looks as following:

<--event "feed_me"-->

#### Exported fields

{{fields "feed_me"}}

```

</details>

#### 2. Build and Deploy
