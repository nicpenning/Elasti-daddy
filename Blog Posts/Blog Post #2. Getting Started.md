# Blog Post #2. 
# 🚧 Under construction 🏗 
## Getting started with the coding environment

To begin the development of an Elastic Integration that will prepare everything we need to 
ingest the `Feed Me.csv` file, we will need to have the proper tools installed to run the `elastic-package` 
tool. This tool will allow us to spin up the Elastic stack for testing and aid in the creation,
testing, and publishing of an integration.

Here are the pre-requisities:
- Windows Subsystem for Linux 2 (Optional)
- Docker
- Go
- elastic-package

Note: Each of these tools should be executable from any path to be successful.

This blog post will cover getting all of those tools installed and how to test them to make sure
the environment is ready to go. I will be using Windows Subsystem Linux 2 on Windows 11 throughout
this project, but this should work on any environment as long as these 3 tools have been deployed.

#### 1. Install Ubuntu on Windows 11
<details>

To install Windows Subsystem for Linux 2 on Windows 11, open up a command prompt (not as an admin) and run this:

`wsl --install -d Ubuntu`

Once installed, we can log into this newly installed Operating System:

`wsl -d Ubuntu-22.04`

Congrats, you are now in your Ubuntu OS inside of Windows.

⚠️ Note: If the command above doesn't work because you see this error: `There is no distribution with the supplied name.`, you can check to see what distributions have been installed by running:

`wsl --list`

Then run `wsl -d {distribution}` by placing in the version of Ubuntu that is listed as installed.

In my environment I see this as the output:
```
PS C:\Users\nicpe> wsl --list
Windows Subsystem for Linux Distributions:
Debian (Default)
Ubuntu-22.04
```

Now make sure you have internet access by running:

`curl google.com`

If you see `curl: (6) Could not resolve host: google.com` then you need to fix DNS by follow the commands below.

⚠️ Fix DNS resolution issues (Optional - You may need to run this if you cannot access the internet from within Ubuntu on Windows)

```
echo -e "[network]\ngenerateResolvConf = false" | sudo tee -a /etc/wsl.conf
sudo unlink /etc/resolv.conf
echo nameserver 8.8.8.8 | sudo tee /etc/resolv.conf
```

For more details on setting up WSL 2 you can see Microsoft's documentation [here.](https://learn.microsoft.com/en-us/windows/wsl/install#install-wsl-command)
</details>

#### 2. Install Docker (Inside of WSL 2 - Ubuntu)
<details>

Now it is time to install docker when you are inside of Ubuntu.

As a reminder, to get logged into Ubuntu, open up command prompt or powershell (not as an admin) and run `wsl -d Ubuntu-22.04`.

Or you can use the drop down inside of the terminal like so:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/c650ad49-46fe-405b-94d4-fed7dae89494)

Then Ubuntu will be running:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/d534ff33-4556-4845-8932-3bbb4cdf1729)


When you try to run docker or docker-compose right now it will fail:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/66012272-cc01-49b9-b655-b3f26217de29)

So we need to install docker and docker-compose:

```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Example Output:
```
napsta@el33t-b00k-1:~$ curl -fsSL https://get.docker.com -o get-docker.sh
napsta@el33t-b00k-1:~$ sudo sh get-docker.sh
# Executing docker install script, commit: c2de0811708b6d9015ed1a2c80f02c9b70c8ce7b

WSL DETECTED: We recommend using Docker Desktop for Windows.

...snipped for brevity...

================================================================================
```

Now we should be able to run docker and see the following if we were successful:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/20623856-bfbd-4d95-9883-8010dc1fae83)

Then we need to install docker-compose and start the service:

```
sudo apt-get install docker-compose
sudo service docker start
```

If all is successful, then you should be able to test our docker installation by running:

```
sudo docker run hello-world
sudo service docker start
```

Then you should see this output if it worked:

```
napsta@el33t-b00k-1:~$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
719385e32844: Pull complete
Digest: sha256:c2e23624975516c7e27b1b25be3682a8c6c4c0cea011b791ce98aa423b5040a0
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
...snipped for brevity...
```

Lastly, it is a good idea to run docker with your user so you don't have to use sudo everytime.

To do this, run the following:

```
sudo usermod -aG docker $USER
sudo groupadd docker
newgrp docker
```

The last check is to make sure docker-compose can execute, so run this command:

```
docker-compose
```

Then make sure you have this output:

```
napsta@el33t-b00k-1:~$ docker-compose
Define and run multi-container applications with Docker.

Usage:
  docker-compose [-f <arg>...] [--profile <name>...] [options] [--] [COMMAND] [ARGS...]
  docker-compose -h|--help
...snipped for brevity...
```

If you have made it through all of these steps and have the correct outputs, you have docker and docker-compose installed and are ready to install Go!

</details>

#### 3. Install Go (Inside of WSL 2 - Ubuntu)

<details>

Now it is time to install `go`. When we first try to run `go` we will see the following output which tells us that it is not installed:

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/155ebc27-6103-425d-9778-05a0b6be3e4b)

So let us get it installed:

```
wget https://go.dev/dl/go1.20.5.linux-amd64.tar.gz #(Find latest download link here - Linux: https://go.dev/dl/)
tar -xf go1.20.5.linux-amd64.tar.gz
sudo nano /etc/profile --> Add export PATH="~/go/:$PATH" to the bottom of the file
```

This is what it looks like when editing the /etc/profile to add the PATH for `go`

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/b69bdc65-350b-454e-be70-107c7fdc497f)

Then to make sure this new PATH works, you will need to restart your Ubuntu instance by stopping and restarting the `LxssManager` service

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/69514de1-b49c-4cc0-8b66-c0b66e37eb4f)


</details>

#### 4. Install elastic-package (Inside of WSL 2 - Ubuntu)

<details>

```
wget https://github.com/elastic/elastic-package/releases/download/v0.81.0/elastic-package_0.81.0_linux_amd64.tar.gz #(Find latest download link here elastic-package_*.*.*_linux_amd64.tar.gz - https://github.com/elastic/elastic-package/releases)
mkdir ~/eptar -xf elastic-package_*.*.*_linux_amd64.tar.gz -C ~/ep
sudo nano /etc/profile --> Add export PATH="~/ep/:$PATH" to the bottom of the file
```

Now let's try to run the elastic-package tool from our home directory.

```
elastic-package --version
```
</details>

To summarize, we ...

This concludes ...
