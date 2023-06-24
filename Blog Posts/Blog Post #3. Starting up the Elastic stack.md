# Blog Post #3. 
# 🚧Under construction 🏗️
## Starting up the Elastic stack

Now that we have the `elastic-package` tool (and all of the proper requirements to use it) we can now start up an entire Elastic stack
that will be composed of:
 - Elasticsearch
 - Kibana
 - Fleet
 - Elastic Agent
 - Elastic Package Repository

#### 1. Let's start up the stack
<details>

To startup the Elastic composed of all of the components above, run the following command:

```
elastic-package stack up -v -d
```
You will see an output simliar to this (Note, this command used a version of the static that was programmed into elastic-package at this time which was 8.7.1):

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/cd7ead6e-cb22-451b-993e-ce5391d3c1db)

Even though 8.8.1 has been released, the default is 8.7.1. We can override this setting using the `--version` command which I will demonstrate later.

While this command is executing and building our stack, I will breakdown the arguments and what is happening.

`elastic-package` - The tool we give commands for managing our stack and Elastic package during development.
`stack` - This is using the stack parameter which allows us to bring up and down our Elastic stack
`up` - This parameter is used for deploying the Elastic stack for use. The `down` version of this will bring down the stack.
`-v` - This is the verbose mode so we can see more of what is happening in the background (you can also use --verbose as the flag)
`-d` - I believe this is the daemon mode, but I can't find this in the docs to be sure. But it works when using it!

</details>
