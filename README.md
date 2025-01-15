[//]: # (README.md)
[//]: # (Copyright © 2024 Joel A Mussman. All rights reserved.)
[//]: #

![Banner Light](https://raw.githubusercontent.com/cleancoderscollaborative/cdn/main/banners/banner-clean-coders-light.png#gh-light-mode-only)
![Banner Light](https://raw.githubusercontent.com/cleancoderscollaborative/cdn/main/banners/banner-clean-coders-dark.png#gh-dark-mode-only)

# Clean Coders Collaborative

This repository is the Jekyll source for the cleancoderscollaborative.github.io website.
Pushing to this repository triggers the Jekyll build, which publishes Markdown and
Liquid to the HTML site.
Look to the Jekyll documentation for instructions.

## Customizations

* The _layouts/page.html and post.html files have been modified
    with a new attrribute in the YAML header: hide_title.
    If that attribute is set to "true", the page or post title will not be
    copied into the content of the page.
* The _includes/header.html file has been modified to change the favicon.