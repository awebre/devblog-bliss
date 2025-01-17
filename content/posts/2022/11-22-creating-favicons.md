---
title: 'Favicon Generation with ImageMagick'
date: 2022-11-22T20:58:00-6:00
summary: "Some common tricks I've used for creating Favicons with ImageMagick"
tags:
  - post
  - ffmpeg
---

### Common Image Manipulation Tricks

Below are some quick tricks that can be used to manipulate images using
[ImageMagick](https://imagemagick.org/).

If you're looking to replace white areas with a translation:

`convert ./incognitum_3.jpeg -fuzz 5% -transparent white incognitum_icon.png`

- Tweak the "fuzz" argument until you get the right amount of white removal.

To create a scaled down version of any image:

`convert favicon-256.png -resize 128x128 favicon-128.png`
{aside}
This can be repeated for several resolutions until you have enough to generate a Favicon.
{/aside}

To create a favicon.ico from multiple source images:

`convert favicon-16.png favicon-32.png favicon-64.png favicon-128.png favicon-256.png -colors 256 favicon.ico`
