# Blog Post #6.
## Testing the integration with the dataset

We have the Elasti-daddy integration installed with what we believe to be the correct data stream, fields, and ingest pipeline.
The next step is to add the integration to an Elastic Agent that has access to our `Feed Me.csv` file. Through this blog post we
will aim to install an Elastic Agent on our Ubuntu on Windows system so that it can have direct access to our Elastic Stack that
is running locally. Then we will create a policy with our integration and see if we can get the data ingested!

- Install Elastic Agent
- Create Policy
- Add Policy to Elastic Agent
- Check for Data Ingestion

#### 1. Install Elastic Agent
<details>

We will install the Elastic Agent before adding our policy that includes our Integration inside of it. To do this, we will be making
a few changes to the Fleet server to ensure that our Ubuntu on Windows can communicate with the Elastic Stack that was stood up by
the `elastic-package` tool. 

⚠️ Note: As I was going through this blog, I found out my date was over a day off. You can check the date of your Ubuntu on Windows 
by running `date` and reviewing the output. If it is incorrect, please execute `sudo hwclock -s` in your terminal to correct this.

We need to adjust the profile of the `elastic-package` tool to have Elasticsearch listen on 127.0.0.1:9200. To do this, I modified my profile here:

```bash
/home/napsta/.elastic-package/profiles/default/stack/snapshot.yml
```

⚠️ Note: You will need to change `napsta` to your username in your environment.

Then I tweaked the line `- "ELASTICSEARCH_HOST=https://elasticsearch:9200"` and `"FLEET_SERVER_ELASTICSEARCH_HOST=https://127.0.0.1:9200"` to 127.0.0.1:

```bash
...snipped for brevity...
environment:
    - "ELASTICSEARCH_HOST=https://127.0.0.1:9200"
    - "FLEET_SERVER_CERT=/etc/ssl/elastic-agent/cert.pem"
    - "FLEET_SERVER_CERT_KEY=/etc/ssl/elastic-agent/key.pem"
    - "FLEET_SERVER_ELASTICSEARCH_HOST=https://127.0.0.1:9200"
...snipped for brevity...
```

After I made these changes, I then saved them and took the stack down and brought it back up as we have done in the past.

```bash
elastic-package stack down
elastic-package stack up -d -v --version=8.8.1
elastic-package stack up -v -d --services package-registry
```

Now, let us adjust our Fleet settings so we can install our Elastic Agent on Ubuntu. Navigate to the Fleet Settings and add a new Fleet server and Elasticsearch server that the agent will be
able to connect to.

⚠️ Note: We are not creating a new Fleet server or Elasticsearch server, but instead we are using their IP addresses instead of their
DNS names because I don't know how to route the DNS names to the host :). This is a simple work around for now. Also, make sure that
when you add the Fleet Server that you make it the default server.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/aadec97b-0384-481e-8c91-840afb25ce85)

Again, note that we don't need to install a new Fleet server, we are just giving the Agents another way to connect to the current one.

Here is what your configuration should look like after the changes:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/06aeb39e-8238-4cd0-8f8e-97ae14c05039)

The caveat for the Elasticsearch server that we added is that you need to also copy the fingerprint from the current configuration:

https://github.com/nicpenning/Elasti-daddy/assets/5582679/e552e8f5-11fd-4633-a4fb-f10fd8fd2181

After that, let us adjust the policy to use our new Elasticsearch and Fleet servers.

Do this in the policy by selecting our `Local Host Elasticsearch` output:

https://github.com/nicpenning/Elasti-daddy/assets/5582679/4f7bcc20-b9cc-4e55-aeb1-a46b4c8b5ee9

Once those steps are completed, it is time to install our Elastic Agent.

Go back to Fleet and then click on Add Agent:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/0aa39fa2-45fe-49c8-bf0b-2cbf0d1b2495)

There will be a flyout and most likely will have the Linux installation already selected:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/0e1b4e40-8ade-4582-a7a6-7f5aae0d3e35)

Make sure that the command is using https://127.0.0.1:8220 and **not** https://fleet-server:8220.

Copy the commands and put them in a text editor or run them one by one.

Please note, we need to adjust the last install command to use `--insecure` because we added a fleet server without
managing the encryption. So the command to install will look something like this:

```bash
sudo ./elastic-agent install --url=https://127.0.0.1:8220 --enrollment-token={Your token goes here}= --insecure
```

Here is what it looked like to install in my terminal:

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.8.1-linux-x86_64.tar.gz
tar xzvf elastic-agent-8.8.1-linux-x86_64.tar.gz
cd elastic-agent-8.8.1-linux-x86_64
sudo ./elastic-agent install --url=https://127.0.0.1:8220 --enrollment-token={Your enrollment token} --insecure
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  513M  100  513M    0     0  19.8M      0  0:00:25  0:00:25 --:--:-- 20.5M
elastic-agent-8.8.1-linux-x86_64/NOTICE.txt
elastic-agent-8.8.1-linux-x86_64/README.md
...snipped for brevity...
Elastic Agent will be installed at /opt/Elastic/Agent and will run as a service. Do you want to continue? [Y/n]:Y
{"log.level":"warn","@timestamp":"2023-07-07T10:25:25.003-0500","log.logger":"tls","log.origin":{"file.name":"tlscommon/tls_config.go","file.line":104},"message":"SSL/TLS verifications disabled.","ecs.version":"1.6.0"}
{"log.level":"info","@timestamp":"2023-07-07T10:25:25.870-0500","log.origin":{"file.name":"cmd/enroll_cmd.go","file.line":478},"message":"Starting enrollment to URL: https://127.0.0.1:8220/","ecs.version":"1.6.0"}
{"log.level":"warn","@timestamp":"2023-07-07T10:25:26.086-0500","log.logger":"tls","log.origin":{"file.name":"tlscommon/tls_config.go","file.line":104},"message":"SSL/TLS verifications disabled.","ecs.version":"1.6.0"}
{"log.level":"info","@timestamp":"2023-07-07T10:25:26.935-0500","log.origin":{"file.name":"cmd/enroll_cmd.go","file.line":276},"message":"Successfully triggered restart on running Elastic Agent.","ecs.version":"1.6.0"}
Successfully enrolled the Elastic Agent.
Elastic Agent has been successfully installed.
```

If we are successful, we should see a healthy agent show up in our Fleet:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/1263cd9b-17e3-43a8-8d77-e15e7f5e4fee)

Success!

Now let us check back on our agent to see if we are seeing any events from the basic system integration that is currently deployed. To do this we will navigate
to Discover:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/2b3eb73d-5b97-41d7-9588-0534130f4021)

Then we need to filter out the other two agents that are already deployed that are sending logs to the stack by excluding these agent names:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/2198f8c8-cb7c-48c1-868f-62887bcf1e91)

Now we should remain with some events from today in Discover for our agent using the default `System` integration:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/9accc9c0-f0b3-4ce9-8a54-db01372472ea)

Success!

</details>

#### 2. Create Policy
<details>

Starting at the main Fleet page, let us add a new policy called `Feed Me`:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/4aa575e1-0310-4410-a1f8-072861b23e0f)

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/7eddb266-ae7f-4523-afce-a854fefc34f2)

Now we should see that our policy was created:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/ccf33e0c-0d1f-4657-8f8e-d695b793afa1)

⚠️ Note: We made need to set the settings of the Policy to use our Localhost Elasticsearch and Fleet server like we did with the System Integration
if we did not make them the defaults. So go ahead and do that now by clicking on our newly created policy:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/52b5eb51-9765-410c-b3b1-569adb1490ce)

After you do the above then go back to the Integrations Tab then click Add Integration:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/75e1af56-0cce-4e26-9ca4-68829eb4e7c2)

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/d6f4ef45-cd92-4e66-9a54-aca6bdc1b7d9)

Make sure you have Display Beta Integrations enabled and then search for our Elasti-daddy Integration:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/e54c4c53-38ea-4c0a-ba2a-e618e284c4f0)

Now click on the integration then click `Add Elasti-daddy`

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/ef522d26-8668-4597-b9ce-53faeb2de593)

We can leave most of the defaults but make sure that the Existing Hosts is selected with our new Policy name of Feed Me is the selected option then
click `Save and Continue`:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/c99a007f-9f59-4b80-9d4e-6740b98b56b1)

Lastly, click `Add Elastic Agent later` since we will do that in the next step:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/2e45fcf0-c039-462d-905a-4fc36ddaf89f)

</details>

#### 3. Add Policy to Elastic Agent
<details>
Now is the time to add our Policy that contains our Integration. From the Fleet Agents page, select the `...` under actions beside our Ubuntu host and
then select `Assign to new policy`:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/2282f6b2-58bd-41bf-b54c-1be8de711151)

A dialog box will pop up where we can then select your `Feed Me` policy then click `Assign Policy`:

https://github.com/nicpenning/Elasti-daddy/assets/5582679/3b25ee19-0d8d-45b2-91fd-1ea7047c39c3

We should now see our host with the new policy:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/300a2237-7486-49e3-a48e-56609f47958b)

</details>

#### 4. Check for Data Ingestion
<details>

Now it is time to see if our Integration can ingest data from the `Feed Me.csv` file.

If you remember correctly, we made the default location for the file path `~/feed_me.csv` which immediately tells us that this integration will be looking for
a file that does not currently exist. So instead, we will update our integration to where the current file currently exists and with it's current name (which is case sensitive).

You can move the file and name it whatever you wish but I will use the following directory to ingest the data:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Data$ ls
'Feed Me.csv'
```

This means we will need to update the Integration to use `~/GitHub/Elasti-daddy/Data/Feed Me.csv`.

I will go back to Feed Me Policy and edit the integration.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/6e72695d-0337-4aad-a716-0dd460d6d20f)

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/85db0247-9a42-4e6d-a2c9-dc1229d3131b)

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/b69aa92c-9947-4d94-ac9b-cba9ac32af37)

This will correct the health of the agent, however, I had an oversight on the relative path that I had selected for our `Feed Me.csv`.
It is worth mentioning that the Elastic Agent runs as a root or system user, so that relative path will not work for us. So instead, we
will put in the full path for the file we wish to ingest:

```
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Data$ find $(pwd) -name Feed\ Me.csv
/home/napsta/GitHub/Elasti-daddy/Data/Feed Me.csv
```

Your output will be different based on your user name if you are following along. Otherwise, you can move the file to a directory like `/tmp/feed_me.csv`
instead and then put that as the path in the integration for ingest. I chose to use this GitHub path since I will want to make changes to the file and not 
have to worry about copying the file to a different directory everytime I have a new change.

I will update the integration again to see what happens.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/bd02de73-3228-49a5-9d60-e593678cdc92)

After we point to our path of our file, let us check `Discover` and use the search bar to find our data stream by entering in this filter: `data_stream.dataset :"elasti_daddy.feed_me"` and then selecting `Last 90 days` to make sure we hit our date and timestamps for the data set:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/e40d5224-3643-4643-9ebc-aca07f3066ed)

# 🎉 Wow, we did it! 

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/15bd5c44-a5be-477b-95bc-9c22fc2f01f8)

We have over 300 events at this time of writing and the dataset that we used. Let us check out a sample event to see if the fields contain the data 
that we expected by clicking on the expand (arrows) button on the left hand side of an event:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/229fd6dc-7a0b-4c85-9cf6-8eb7997b8f0e)

Above we can see our `Amount` field. But we also see a `?` which means that we need to refresh our page in our browser so that Kibana can provide the correct
data view mapping that we had set in our index template. We are using the default logs-* data view which is what caused this.

After refreshing, and scrolling through the document, we can see that our fields have the proper mappings!

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/20914121-883b-4aa6-8c76-3681fa33150c)

The last thing we will do to wrap up this post is select all of the fields that we had created as custom fields to show them as columns in `Discover`. 
To do this, we can click on the icon to the left of the document count to expand the window:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/28ae41ee-1b81-451d-b7fa-f51ba537376c)

Then hover over the fields we want to add as columns and hit the `+` sign and see them add to the right hand side as columns.

We can then reorganize them to get a better idea of how the data lives in the CSV which makes it easier to interpret.

https://github.com/nicpenning/Elasti-daddy/assets/5582679/3bd3ff77-0618-40dd-919e-6dc79d56255e

As we scroll through the data, it appears that most of the columns have data as they are expected to.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/f7738a69-b9ed-46e8-bb17-466d1f8f0036)

</details>

In summary, we were able to install our integration and ingest data from the `Feed Me.csv` provided from this project.

This concludes and proves that we are able to ingest data with our integration. The rest of the blog posts will refine the integration so that it can include
our dashboard as we saw in the first blog post, but also give a better text inside of the Integration page when it gets installed.
