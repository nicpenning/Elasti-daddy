# Blog Post #8.
# 🚧 Under Construction 🏗️
## Adding the Dashboard to the Integration

The last part of our integration is including the dashboard that has been created for this project. This will require us to export the
dashboard from Kibana using the `elastic-package export` capability along with a build and deploy. This should be a pretty easy endeavor.

Let us get into it!

#### 1. Import Kibana Dashboard
<details>

We will start by importing the dashboard called `Feed Analysis - Integration.ndjson` found [here]() into Kibana.

I have detailed this process in Blog Post #1 in the [Import Kibana Dashboard Section](https://github.com/nicpenning/Elasti-daddy/blob/main/Blog%20Posts/Blog%20Post%20%231.%20Data%20Ingest.md#12-import-kibana-dashboard). The same steps still apply here.

Once the dashboard is imported, move on to the next step to export it.

</details>

#### 2. Export Kibana Dashboard with elastic-package
<details>

Now is the time to export the dashboard by running the `elastic-package export dashboards` command.

⚠️ Note: You may get an error if this is the first time running this command. It will look like something like this:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package export dashboards
Export Kibana dashboards
Error: can't create Kibana client: undefined environment variable: ELASTIC_PACKAGE_KIBANA_HOST. If you have started the Elastic stack using the elastic-package tool, please load stack environment variables using 'eval "$(elastic-package stack shellinit)"' or set their values manually
```

To correct this, we simply run the command `'eval "$(elastic-package stack shellinit)"` which is stated in the error output like so:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ eval "$(elastic-package stack shellinit)"
Detected shell: bash
```

Now, let us try our export again:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package export dashboards
Export Kibana dashboards
? Which dashboards would you like to export?  [Use arrows to move, space to select, <right> to all, <left> to none, type to filter]
> [ ]  [Elastic Agent] Agent Info (ID: elastic_agent-0600ffa0-6b5e-11ed-98de-67bdecd21824)
  [ ]  [Elastic Agent] Agent metrics (ID: elastic_agent-f47f18cc-9c7d-4278-b2ea-a6dee816d395)
  [ ]  [Elastic Agent] CloudWatch Input Metrics (ID: elastic_agent-a7b5e7a0-cd44-11ed-869d-e7dc1b551cd2)
  [ ]  [Elastic Agent] Input Metrics (ID: elastic_agent-a8192f90-cd3f-11ed-869d-e7dc1b551cd2)
  [ ]  [Elastic Agent] Integrations (ID: elastic_agent-1a4e7280-6b5e-11ed-98de-67bdecd21824)
  [ ]  [Elastic Agent] Overview (ID: elastic_agent-a148dc70-6b3c-11ed-98de-67bdecd21824)
  [ ]  [Elastic Agent] S3 Input Metrics (ID: elastic_agent-77cdb1c0-cd45-11ed-869d-e7dc1b551cd2)
  [ ]  [Elastic Agent] TCP Input Metrics (ID: elastic_agent-7d110ba0-cd45-11ed-869d-e7dc1b551cd2)
  [ ]  [Elastic Agent] UDP Input Metrics (ID: elastic_agent-87ad4330-cd45-11ed-869d-e7dc1b551cd2)
  [ ]  [Elastic Agent] Winlog Input Metrics (ID: elastic_agent-1badd650-d136-11ed-b85f-4be0157fc90c)
  [ ]  [Logs System] New users and groups (ID: system-0d3f2380-fa78-11e6-ae9b-81e5311e8cab)
  [ ]  [Logs System] SSH login attempts (ID: system-5517a150-f9ce-11e6-8115-a7c18106d86a)
  [ ]  [Logs System] Sudo commands (ID: system-277876d0-fa2c-11e6-bbd3-29c986c96e5a)
  [ ]  [Logs System] Syslog dashboard (ID: system-Logs-syslog-dashboard)
  [ ]  [Metrics System] Host overview (ID: system-79ffd6e0-faa0-11e6-947f-177f697178b8)
  [ ]  [Metrics System] Overview (ID: system-Metrics-system-overview)
  [ ]  [System Windows Security] Failed and Blocked Accounts (ID: system-d401ef40-a7d5-11e9-a422-d144027429da)
  [ ]  [System Windows Security] Group Management Events (ID: system-bb858830-f412-11e9-8405-516218e3d268)
  [ ]  [System Windows Security] User Logons (ID: system-bae11b00-9bfc-11ea-87e4-49f31ec44891)
  [ ]  [System Windows Security] User Management Events (ID: system-71f720f0-ff18-11e9-8405-516218e3d268)
  [ ]  [System] Windows Overview (ID: system-Windows-Dashboard)
  [ ]  Feed Analysis (ID: 4b9253d0-0aea-11ee-8c83-cf257c04c6b8)
```

Now we are presented with a list of dashboards found in Kibana for export. Use the arrow keys to select the `Feed Analysis` dashboard
byt hitting space bar to select it and hit enter to move on.

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package export dashboards
Export Kibana dashboards
? Which dashboards would you like to export?  [Use arrows to move, space to select, <right> to all, <left> to none, type to filter]
...snipped for breveity...
> [x]  Feed Analysis (ID: 4b9253d0-0aea-11ee-8c83-cf257c04c6b8)
```

IF successful, you should see this output:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package export dashboards
Export Kibana dashboards
? Which dashboards would you like to export? Feed Analysis (ID: 4b9253d0-0aea-11ee-8c83-cf257c04c6b8)
Done
```

When this command is executed, the tool will export the dashbaord as a `JSON` file in a new `kibana\dashboard` directory that was created:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ ls
LICENSE.txt  _dev  changelog.yml  data_stream  docs  img  kibana  manifest.yml
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ cd kibana/
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/kibana$ ls
dashboard
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/kibana$ cd dashboard/
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/kibana/dashboard$ ls
elasti_daddy-4b9253d0-0aea-11ee-8c83-cf257c04c6b8.json
```

Now we can move on to build and deploy the package to see if the dashboard is now included in the integration.

</details>


#### 3. Build, Deploy and Triumph
<details>

Finally, let us build and deploy the integration to see if the Dashboard will show up as an asset in Kibana:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/kibana/dashboard$ elastic-package build
Build the package
README.md file rendered: /home/napsta/GitHub/Elasti-daddy/Integration/elasti_daddy/docs/README.md
Package built: /home/napsta/GitHub/Elasti-daddy/build/packages/elasti_daddy-0.0.1.zip
Done
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/kibana/dashboard$ elastic-package stack up -v -d --services package-registry
...sniped for brevity...

```

Excellent! It appears that our dashboard is showing up in the integration now!

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/92a992ca-2c4e-4186-b7f3-5f522c7c3ff1)

The final check is to make sure that the integration actually works. 

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/66246134-aa1f-417b-be34-e2d365fdde94)

Take notice that when we go to install, it shows 2 assets (This is likely the ingest pipeine and our new dashboard we bundled with the integration).

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/15ee17b4-0fa9-4db8-9945-7f3f157d762d)

It appears that we had an error trying to install, but confusingly enough, it shows that the integration is installed:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/08dd811e-08a9-4d2c-87f3-51df2bb55665)



</details>

