# Blog Post #4.
# 🚧 Under Construction 🏗 
## Gaining a basic understanding of Elastic Integrations

Before we can begin building our Integration, it would be wise to explain
what an Elastic Integration is. This blog post will cover what an Integration 
is composed of so we can get a handle of what we are building.

You can find an overwhelming amount of documentation and explanation by Elastic officially at their website [here](https://www.elastic.co/integrations/) but I will share my perspective on the matter. 

Through this post we will do the following:

- Find Integrations in Kibana
- Look at an example Integration
- Install/Remove an Integration
- Understand Elasticsearch Assets
- Upgrade an Integration

#### 1. Find Integrations in Kibana
<details>

To begin, let's start up our Elastic stack (if you need to):

```
elastic-package stack up -v -d --version=8.8.1
```

Next, let's go to Kibana at https://127.0.0.1:5601 and at the home page, click on "Add Integrations".

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/2b44f0e3-35ac-40b5-839c-94a4df9ea39b)

What you will see before you are all of the current integrations that have been published to the Elastic Package Repository available to the public.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/4eeffb8c-91eb-449f-9174-a895880e39c2)

From the screenshot above you will see that there are 321 integrations across all categories at this time of writing! Fortunately each integration is
sorted into the various categories such as Productivity, Network, or Security to name a few.

You can then click on an integration to find out more details about it. You will do that in the next part of this blog post.

</details>

#### 2. Look at an example Integration
<details>

Let's take a gander at the Microsoft DHCP integration since it uses a CSV file.

Type in dhcp in the search box and then click on the Microsoft DHCP integration.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/2f7d498f-e94e-40c8-88fd-7987f46cd8ec)

This will take you to the Integration details.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/f004ccb9-ce8a-42f7-bb5c-bb636d7f0ab2)

1. Overview

The Overview tab is where you will find a high level overview of what the Microsoft DHCP Integration does. On the right hand side you will see some details such as version number, Elasticsearch Assets which we will dive into later, the minimum license required to use the Integration and even a link to the Changelog.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/6aa45ae7-ed03-43f3-93f6-9cbf692bed97)


2. Logs

You will then see if the Integration captures Logs, Metrics or Traces and then show an example of that type of data. In the screenshot above, you will notice a log entry example of a Microsoft DHCP log in JSON format.

Further down the page you will see what potential fields exist as well, which can be very handy if you are looking for a very specific set of data.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/9d5c6e47-f5ce-4cbd-90a5-20d3c14c4d1c)


3. Settings

The settings tab is where we can install the Integration into the Elastic stack. We will see that accomplished later.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/3628f02c-8038-44c1-8025-fd6737c80978)

4. Add Microsoft DHCP

This button will apply this Integration to a new or existing Agent policy and install the Integration if it hasn't been. Whichever agents are assigned the policy that has this Integration will then be expected to ingest Microsoft DHCP logs. Later on we will see the configuration settings for the integration, which interestingly enough, does not exist under the `Settings` tab.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/426f468d-ea67-4274-b646-eb0ba0932bb7)

</details>

#### 3. Install/Remove an Integration
<details>
Let's go ahead and install the Microsoft DHCP Integration. Simply go to the settings page, then click `Install Microsoft DHCP Assets`. You will notice there are 3 of them that were also noted in the earlier screenshot and also in the screen recording below. The 3 assets are Elasticsearch Ingest Pipelines. 

https://github.com/nicpenning/Elasti-daddy/assets/5582679/e5f2e7f6-f1cd-4d69-a99d-0a9e0a742c60

That's it. The Integration is now installed and is ready to be applied to an agent policy.

You can remove or even reinstall this Integration by clicking on their respective buttons.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/24de646f-13ba-4290-8926-88feb378754b)

</details>

#### 4. Understand Elasticsearch Assets

Elasticsearch Assets are a key component to why Elastic Integrations are so useful to the end users. It makes adopting a new Integration into the Elastic stack easy by managing everything in Kibana instead of handling `.yml` files like it was done in the past with the variety of beats.

When setting up a new Integration, there are many moving parts to be successful. In this example, the only Elasticsearch assets showing installed are Ingest Pipelines. What also gets installed are Index Templates and Component Templets which are vital to the ingestion of data so that it will have the correct data mappings so we can search and visualize the data properly. A quick example is that if the time stamp of the data was not properly mapped to a Date field, then we would not be able to filter and show results for a specific time frame on the set of data. Another example is if we had a numberical value mapped as a string then we could not build visualizations that could do simple math such as sums or averages.

Here is a screenshot of the Microsoft DHCP Index Template that was installed:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/bd04f506-7252-4ce4-8dcc-fb7cdcebc974)

This is the corresponding Component Tempalte that the Index Template above uses that was also installed, but nothing in the install process will tell you that. You can expect that most Integrations will have an Index Template and likely Comonent Templates.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/fcae0c35-1542-41b6-a71c-3bcea0f524e5)

In other Integrations, you may have Dashboards and Visualizations that come bundled with it. These are also considered Assets. A screenshot of a dashboard will even show up in the `Overview` tab if there is one. Our integration that we will build will have more than just pipelines so we will see this first hand later.

#### 5. Upgrade an Integration
