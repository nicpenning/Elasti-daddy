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

#### 1. Install Ubuntu on Windows 11 (Non-Admin Powershell Prompt)
<details>

`wsl --install -d Ubuntu`

⚠️ Fix DNS resolution issues (Optional - You may need to run this if you cannot access the internet from within Ubuntu on Windows)

```
echo -e "[network]\ngenerateResolvConf = false" | sudo tee -a /etc/wsl.conf
sudo unlink /etc/resolv.conf
echo nameserver 8.8.8.8 | sudo tee /etc/resolv.conf
```
</details>

#### 2. Install Docker (Inside of WSL 2 - Ubuntu)
<details>

```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Running Docker with your user instead of sudo

```
sudo usermod -aG docker $USER
sudo groupadd docker
newgrp docker
```

Test Docker usage

`docker run hello-world`

</details>

#### 3. Install Go (Inside of WSL 2 - Ubuntu)

<details>

```
wget https://go.dev/dl/go1.20.5.linux-amd64.tar.gz #(Find latest download link here - Linux: https://go.dev/dl/)
tar -xf go1.20.5.linux-amd64.tar.gz
sudo nano /etc/profile --> Add export PATH="~/go/:$PATH" to the bottom of the file
```

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
