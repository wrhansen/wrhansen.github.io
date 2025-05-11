# wrhansen.dev blog

## Overview

This is my blog site done using the following tech:

* hugo
* terminal theme (by panr)
* github actions
* hosted on github pages

## How to handle images

* put the image under the `static/resources/` directory in the repo
* use the `image` shortcode found [here](https://github.com/panr/hugo-theme-terminal#built-in-shortcodes)
* set src to `/resources/<image-name.png>`
* when the site builds, you should now see your image.

## Development

You can use the custom dev container that exists in order to run this site locally.
From within the devcontainer terminal just run the following:

```sh
hugo server
```

And it will start a development server of the site with hot reload functionality

## Other info

* poison theme repo: <https://github.com/lukeorth/poison>
