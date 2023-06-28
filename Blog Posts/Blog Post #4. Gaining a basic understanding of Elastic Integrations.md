# Blog Post #4.
# 🚧 Under Construction 🏗 
## Gaining a basic understanding of Elastic Integrations

Before we can begin building our Integration, it would be wise to explain
what an Elastic Integration is. This blog post will cover what an Integration 
is composed of so we can get a handle of what we are building.

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

The Overview tab is where you will find a high level overview of what the Microsoft DHCP Imtegration does.

2. Logs

You will then see if the Integration captures Logs, Metrics or Traces and then show an example of that type of data.In the screenshot above, you will notice a log entry example of a Microsoft DHCP log in JSON format.

3. Settings

The settings tab is where we can install the Integration into the Elastic stack. We will see that accomplished later.

4. Add Microsoft DHCP

This button will apply this Integration to a new or existing Agent policy. Whichever agents are assigned the policy that has this Integration will then be expected to ingest Microsoft DHCP logs. Later on we will see the configuration settings for the integration, which interestingly enough, does not exist under the settings tab. 

</details>

#### 3. Install/Remove an Integration

#### 4. Understand Elasticsearch Assets

#### 5. Upgrade an Integration
