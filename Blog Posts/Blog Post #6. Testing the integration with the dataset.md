# Blog Post #6.
# 🚧 Under Construction 🏗️
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

⚠️ Note: As I was going through this blog, I found out my date was over a day off. You can check the date of your Ubuntu on Windows by running `date` and reviewing the output. If it is incorrect, please execute `sudo hwclock -s` in your terminal to correct this.

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

https://github.com/nicpenning/Elasti-daddy/raw/main/Screenshots/Create%20new%20policy.mp4
<video src="https://github.com/nicpenning/Elasti-daddy/raw/main/Screenshots/Create%20new%20policy.mp4" controls="controls" style="max-width: 730px;">
</video>

</details>

#### 3. Add Policy to Elastic Agent

#### 4. Check for Data Ingestion
