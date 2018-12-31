---
title:  "Playing with the Zopflipng compression library"
author: "Eric Dupertuis"
date:   "2016-01-12"
categories:
    - Compression
tags:
    - Zopfli
    - Linux
slug: "playing-with-zopflipng-compression-library"
---

I saw [This blog post](http://blog.codinghorror.com/zopfli-optimization-literally-free-bandwidth/) on [codinghorror.com](http://codinghorror.com) about PNG images optimisation using Zopfli. And i decided to try it out and make some experiments with it. Codinghorror's blog post also talks about PNGout and gzip but I'm not going to.

## What is Zopfli ?

Zopfli is a data compression algorithm that encodes data into Deflate, gzip and zlib formats. It is based on an algorithm developed by Jyrki Alakuijala. A [first reference implementation](https://github.com/google/zopfli) was released to the public in february 2013.

What really interests us here it the ZopfliPNG tool, implemented by Google in May 2013. This tool allow lossless compression for PNG images.

![Much Tech](https://i.imgflip.com/mshs7.jpg)

## installing ZopfliPNG

first, you'll need to clone the [repository](https://github.com/google/zopfli) and cd into the newly created folder

    git clone https://github.com/google/zopfli.git
    cd zopfli

And then you simply have to build it (you may need to install build-essential if you're using ubuntu)

    make zopflipng

Then, to use it just type

    ./zopflipng input.png output.png

Personally I like having the tool available system-wide so :

    sudo mv zopflipng /usr/bin/

## Playing with zopfli

For my little test, I download a pack of icon from [Flaticon.com](http://www.flaticon.com/) So I have now 50 small png images to compress. A little `du -h` gives us a size of 340K

Now I'll just use a simple bash script to compress all of them and output them in another folder

    #!/bin/bash
    for file in original/*.png
    do
        zopflipng "${file}" compressed/$(basename "${file}")
    done

Now if we take a look at the size difference.

    340K	./original
    312K	./compressed

Hell yeah, we just saved 28k right there. It might not seem like a big deal but the compressed folder is 8.23% smaller (91.77% of the original size). If the images are 8% smaller on average, that's a lot of bandwidth that you just saved right there and the page load is going to be faster for the end user.

And if we compare the two images :

![Zopfli Comparison](/images/zopfli.png)

We can see there's not much difference (like, none..., it's lossless). The only real difference is the file size. Let's just take a look at four icons picked at random

| File | Original | Compressed | Percentage of original |
|:----:|:--------:|:----------:|:----------------------:|
|   1  |   7.5K   |    6.8K    |         90.65%         |
|   2  |   4.0K   |    3.5K    |          87.5%         |
|   3  |   2.4K   |    2.1K    |          87.5%         |
|   4  |   5.9K   |    5.4K    |          91.75         |
|  ... |    ...   |     ...    |           ...          |

Of course the compression depends on the image's complexity but we get images that are up to 13% smaller.

The downside is that it took quite a lot of time and power. Compressing the images took my computer 293 seconds using one core at 100% (zopfli doesn't use multiple cores). Now keep in mind that i'm running this on a laptop(2.4 Ghz Intel i3-3110M) and I used a single instance of Zopfli, I could have used multiple instances and compress multiple images in parralel (but i didn't want to write such a script because I'm lazy).

## Compressing bigger images

So for another test i tried to compress this 2836K image found on [Dribbble.com](https://dribbble.com/shots/631004-Wallpaper-Retina/attachments/53013) by [Tim Van Damme](https://twitter.com/maxvoltar)

Again :

    zopflipng wallpaper.png compressed.png

And now we wait, go grab a cup of coffee and a cookie because this is gonna take some time to compress. It took me 4 minutes and 36 seconds (276 seconds).

| Original | Compressed | Percentage of original |
|:--------:|:----------:|:----------------------:|
|   2836K  |    2557K   |         90.159%        |

So we won almost 10%, that's neat.

## So what now ?

![Compress all the images](https://i.imgflip.com/x82yl.jpg)

So we can see that Zopfli is a pretty great tool to compress PNG images. It takes quite a lot of CPU time but it really is just : compress once, deliver indefinitely. If you have to deliver a lot of images and icons to hundreds, thousands or millions of users, just think about the bandwith that you're saving.
Just imagine you're serving those 50 png icons every month to 300'000 *unique* users (so I don't bother about cache and stuff).

| Icons original | Icons compressed |  users  | Total original | Total compressed |
|:--------------:|:----------------:|:-------:|:--------------:|:----------------:|
|      340K      |       312k       | 300'000 |      102MB     |      93,6MB      |

So ok you just save 8.4MB of bandwith every month but here we are just talking about a pack of 50 icons. Maybe you have a lot more icons to distribute, profile pictures, banners and all kinds of images. And if you can reduce the size of the images you're also reducing the total size of a page, and if you make the web faster, you make the web better. (love that little catch phrase)

Have fun compressing stuff.