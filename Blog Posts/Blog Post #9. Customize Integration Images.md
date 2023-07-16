# Blog Post #9.
# 🚧 Under contruction 🏗️
## Customize Integration Images

In this blog post we will make some changes to the icons and images that are displayed for the integration. This will include the dashboard image as well.

The following screenshots are of the images and icons that we plan to replace.

On the overview page:
![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/16a15415-c734-4659-9bac-9eb71bb8881e)

In the policy:
![image](https://github.com/nicpenning/Elasti-daddy/assets/5582679/74b7bfaf-d5ea-4807-9a30-82f6e04144d9)


# 1. Add the Images

First off, we need an image that represents our project in the proper formats. We can figure out what type of images we need based on the `package-spec`
for the `manifest.yml` file that we will be updating. You can learn more about the `package spec` [here](https://github.com/elastic/package-spec/blob/main/spec/integration/manifest.spec.yml).

However, we do know that we need a `.svg` file for the [icons](https://github.com/elastic/package-spec/blob/e967b740ce752c6a4f8bfe527bae3996d311ebdb/spec/integration/manifest.spec.yml#L151) and `.png` for [screenshots](https://github.com/elastic/package-spec/blob/e967b740ce752c6a4f8bfe527bae3996d311ebdb/spec/integration/manifest.spec.yml#L185).

I went ahead and used DALL-2 to generate an image for use here. I was able to get a `.png` but then used a random website online to convert it to `.svg`.
During the converting process, I lost the color, but it will do for our example.



# 2. Reference the Images

# 3. Build, Deploy, and Triumph
