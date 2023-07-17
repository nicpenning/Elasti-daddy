# Blog Post #9.
## Customize Integration Images

In this blog post we will make some changes to the icons and images that are displayed for the integration. This will include the dashboard image as well.

The following screenshots are of the images and icons that we plan to replace.

On the overview page:
![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/16a15415-c734-4659-9bac-9eb71bb8881e)

In the policy:
![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/74b7bfaf-d5ea-4807-9a30-82f6e04144d9)


#### 1. Add the Images
<details>

First off, we need an image that represents our project in the proper formats. We can figure out what type of images we need based on the `package-spec`
for the `manifest.yml` file that we will be updating. You can learn more about the `package spec` [here](https://github.com/elastic/package-spec/blob/main/spec/integration/manifest.spec.yml).

However, we do know that we need a `.svg` file for the [icons](https://github.com/elastic/package-spec/blob/e967b740ce752c6a4f8bfe527bae3996d311ebdb/spec/integration/manifest.spec.yml#L151) and `.png` for [screenshots](https://github.com/elastic/package-spec/blob/e967b740ce752c6a4f8bfe527bae3996d311ebdb/spec/integration/manifest.spec.yml#L185).

I went ahead and used [DALL-E-2](https://openai.com/dall-e-2) to generate an image for use here. I was able to get a `.png` but then used a random website online to convert it to `.svg`.
During the converting process, I lost the color, but it will do for our example.

I have placed the images in this project under `Images`, but they need to exist in the integration under the `img` directory.

We will make a copy of the files from the `Images` directory that I have on this GitHub repository and move them to the integration with simpilfied names:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Images$ ls
'DALL·E 2023-07-16 13.57.28 - A baby girl feeding on a bottle from a happy man, cartoon.png'   Dashboard.png
 DALL·E-2023-07-16-13.57.28-A-baby-girl-feeding-on-a-bottle-from-a-happy-man-cartoon.svg
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Images$ cp Dashboard.png ../Integration/elasti_daddy/img/feed_me_dashboard.png
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Images$ cp DALL·E-2023-07-16-13.57.28-A-baby-girl-feeding-on-a-bottle-from-a-happy-man-cartoon.svg ../Integration/elasti_daddy/img/elasti_daddy_icon.svg
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Images$ cd ../Integration/elasti_daddy/img/
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/img$ ls
elasti_daddy_icon.svg  feed_me_dashboard.png  sample-logo.svg  sample-screenshot.png
```
Move to the next step for referencing these images.

</details>

#### 2. Reference the Images
<details>

Now let us edit the `manifest.yml` file for the integration to reference the images we stored in the `img` directory. 
We can simply replace the sample icon and picture with our own:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy/img$ cd ..
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ ls
LICENSE.txt  _dev  changelog.yml  data_stream  docs  img  manifest.yml
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ nano manifest.yml
```

Here is a snipped version of the manifest file I modified. Simply note that I changed the `- src:` and `title` lines, respective to their proper
icon or image.

```yaml
format_version: 2.8.0
name: elasti_daddy
title: "Elasti-daddy"
version: 0.0.1
source:
  license: "Apache-2.0"
description: "This is a package for preparing and analyzing motherhood and fatherhood data for taking care of a baby. The ai>
type: integration
categories:
  - custom
conditions:
  kibana.version: "^8.7.1"
  elastic.subscription: "basic"
screenshots:
  - src: /img/feed_me_dashboard.png
    title: Feed Me Dashboard
    size: 600x600
    type: image/png
icons:
  - src: /img/elasti_daddy_icon.svg
    title: Elasti-daddy
    size: 32x32
...snipped for brevity...
```

Now we should be able to move on to testing these changes out.
  
</details>

#### 3. Build, Deploy, and Triumph
<details>

As always, let us build, deploy and see if our changes took root:

```bash
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package build
Build the package
README.md file rendered: /home/napsta/GitHub/Elasti-daddy/Integration/elasti_daddy/docs/README.md
Package built: /home/napsta/GitHub/Elasti-daddy/build/packages/elasti_daddy-0.0.1.zip
Done
napsta@el33t-b00k-1:~/GitHub/Elasti-daddy/Integration/elasti_daddy$ elastic-package stack up -v -d --services package-registry
2023/07/16 14:46:30 DEBUG Enable verbose logging
Boot up the Elastic stack
...snipped for brevity...
Recreating elastic-package-stack_package-registry_1 ... done
Recreating elastic-package-stack_package-registry_is_ready_1 ... done
Done
```

🎉 Success! We can now see our icons and dashboard image worked!

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/2e0f1dca-a3b7-4d4c-9ace-ae8a3434b3ee)

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/0ff7cf53-60a0-40a2-8ad9-d47ce42a0a21)

![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/75d20a18-6372-4d62-bc93-fef55c30b9e5)
 
</details>

In summary, we were able to update the images and icons for this integration.

This concludes and proves that we are able to tweak the default images and icons for a custom integration.
