# Blog Post #3. 
## Starting up the Elastic stack

Now that we have the `elastic-package` tool (and all of the proper requirements to use it) we can now start up an entire Elastic stack
that will be composed of:
 - Elasticsearch
 - Kibana
 - Fleet
 - Elastic Agent
 - Elastic Package Repository

#### 1. Let us start up the stack
<details>

To startup the Elastic stack composed of all the components above, run the following command:

```
elastic-package stack up -v -d
```

You will see an output similiar to this:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/cd7ead6e-cb22-451b-993e-ce5391d3c1db)

⚠️ Note: Even though 8.8.1 has been released, the default at this time of writing for this tool is 8.7.1. We can override this setting using the `--version` command which I will demonstrate [later.](https://github.com/nicpenning/Elasti-daddy/blob/main/Blog%20Posts/Blog%20Post%20%233.%20Starting%20up%20the%20Elastic%20stack.md#3-advanced-deployment-option)

While this command is executing and building our stack, I will breakdown the flags used and what is happening.

`elastic-package` - The tool we give commands for managing our stack and Elastic package during development.

`stack` - This is using the stack parameter which allows us to bring up and down our Elastic stack.

`up` - This parameter is used for deploying the Elastic stack for use. The `down` version of this will bring down the stack.

`-v` - This is the verbose mode so we can see more of what is happening in the background (you can also use --verbose as the flag)

`-d` - This will run the docker containers in daemon mode, which means running as a background process so we can continue to use our terminal without interrupting the deployment of the stack.

If your deployment was successful, you should see a screen simliar to this:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/accf3549-d1ab-48ff-b2e9-5b984e12cb34)

Which means it is time to login into Kibana and make sure everything works!

You can do this by navigating to https://127.0.0.1:5601. This is an instance of Kibana that has TLS enabled, running locally on your host, and on the default port of 5601.

When first navigating to this site you will see a warning sign that says `Your connection is not private` that will look like this:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/0cdb5e4b-5768-4b64-8b78-a39170ef158b)

This is expected because during the creation of the stack self signed certificates were created to enable encryption for the stack but our browser does not trust them.

It is okay to proceed since we deployed this stack and it is running on our host. We can safely proceed by clicking `Advanced` and then clicking `Continue to 127.0.0.1 (unsafe)`:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/75efbdf4-ea00-4057-baae-aab7723a7b49)

⚠️ Note: The above works for Edge, but if you are using Chrome, you may not have that option so you must type `thisisunsafe` on the keyboard while that page is open. Google Chrome does not give you the option to simply click through, but rather you must use that hidden option by typing that text above. There is no window or text box to place that text, so again, just have the insecure web page open and type in `thisisunsafe` all together with no capitals letters and no spaces.

If you successfully got past the warning screen, you should know see the login page for Kibana!

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/3f0c540e-1b9f-4e8b-9664-d50f46a5718d)

To login to Kibana, we will use the default credentials that were assigned with the `elastic-package` tool which are:

```
username : elastic
password : changeme
```

🎉 Congrats! You know have a fully functioning stack to test with. You can click on the circle icon in the right hand corner to verify the version of Kibana running.

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/9babb981-b138-4821-a3d4-355cfbd46ba0)

</details>

#### 2. Taking down the stack
<details>

When we are finished using our stack, we can reclaim our resources (CPU/RAM) by bringing down the stack which is very simple. All you need to do is run the following in your terminal where you started the stack:

```
elastic-package stack down
```

Which usually takes less than 30 seconds. You will then see this output:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/3da588d6-36a9-4b68-b8d7-b5959e7e49cd)

That is it. If you try to navigate to your Kibana instance again then it will be unavailable since we took down the stack. To bring the stack back up again, simply go back to step 1 and repeat.
 
</details>

#### 3. Advanced Deployment Option
<details>

Now while we were able to run `elastic-package` and deploy the stack using the current default version of 8.7.1, we may want to use the latest version instead. To do this, we can take advantage of the `--version` flag like so:

```
elastic-package stack up -v -d --version=8.8.1
```

This will download and deploy that specific version of the Elastic stack simliar to how it did for the previous version.

What is nice is that you can test much older versions as well by setting the version number to which ever one you need to test.

🔮 Lastly, you can preview and test pre-release versions of the Elastic stack as well by using the `-SNAPSHOT` value appended to the next minor version release that is available. In our example, 8.9.0 has not been release yet, but we wish to test our future integration with it, plus it would be neat to see what is coming in 8.9.0, so we can run this to get and deploy the current state of 8.9.0:

```
elastic-package stack up -v -d --version=8.9.0-SNAPSHOT
```

This will go out and download the current snapshot of 8.9.0 and deploy it. Here is a screenshot of this command being successful:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/70990b05-eb0d-4e79-ad39-04886ffe6a64)

</details>

In summary, we were able to easily deploy the Elastic stack by using the default version but also understand how to use any version which includes an un-released version of the stack and we also figured out how to take down the stack. You can learn more about the `elastic-package` tool [here.](https://github.com/elastic/elastic-package#elastic-package) You may also learn more about the `stack` command that we used in this blog post [here.](https://github.com/elastic/elastic-package#elastic-package-stack)

This concludes the starting up and taking down of the Elastic stack blog post.
