# pwa-asset-generator ✨
[![npm](https://img.shields.io/npm/v/@pwabuild/pwa-asset-generator?color=brightgreen)](https://www.npmjs.com/package/@pwabuild/pwa-asset-generator) [![node](https://img.shields.io/node/v/@pwabuild/pwa-asset-generator)](https://www.npmjs.com/package/@pwabuild/pwa-asset-generator) [![Dependabot Status](https://api.dependabot.com/badges/status?host=github&repo=charistheo/pwa-asset-generator)](https://dependabot.com)

> A JavaScript library that automates PWA asset generation and image declaration. It automatically generates icon and splash screen images, guided by Web App Manifest specs and Apple Human Interface guidelines. It also updates manifest.json and index.html files with the generated images.

![Usage demonstration](https://raw.githubusercontent.com/charistheo/pwa-asset-generator/HEAD/static/demo.gif)

## Features

PWA Asset Generator automates the image generation in a creative way. Having [Puppeteer](https://pptr.dev) at its core enables lots of possibilities.

* Generates both icons and splash screens with optional `--icon-only` `--splash-only` `--landscape-only` and `--portrait-only` flags ✨

* Updates your `manifest.json` and `index.html` files automatically for declaring generated image assets 🙌

* Scrapes latest specs from Apple Human Interface guidelines website via Puppeteer to make your PWA ready for all/recent iOS devices out there 🤖

    * Supports offline mode and uses static spec data when things go wrong with scraping 📴

* Uses the Chrome browser as it’s a canvas of your fav image editor. It uses a shell HTML on the fly as an art board and centers your logo before taking screenshots for each resolution via Puppeteer 🤖

* You can provide your source in multiple formats; a local image file, a local HTML file, a remote image or HTML file 🙌

    * When it’s an image source, it is centered over the background option you provide 🌅
    * When it’s an HTML source, you can go as creative as you like; position your logo, use SVG filters, use variable fonts, use gradient backgrounds, use media queries while generating dark mode splash screens, use typography and etc. Your HTML file is rendered on Chrome before taking screenshots for each resolution 🎨

* Uses [puppeteer-core](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#puppeteer-vs-puppeteer-core) instead of puppeteer and only installs Chromium if it doesn't exist on the system. Saves waste of ~110-150mb of disk space and many seconds from the world per each user 🌎⚡️

* Supports dark mode splash screens on iOS! So, you can provide both light 🌕 and dark 🌚 splash screen images to differentiate your apps look & feel based on user preference 🌙

* Supports [PWA maskable icons](https://web.dev/maskable-icon/) by declaring them in manifest file adaptively 🖼

* Supports flexible tag formatting and path customization options to comply with various development environments. You can adjust your output using single quotes `--single-quotes`, self-closing tags `--xhtml`, path prefixes `--path` and path overrides `--path-override` 📝

## Install

```
$ npm install --global @pwabuild/pwa-asset-generator
```

## One-off execution

```
$ npx @pwabuild/pwa-asset-generator
```

## Usage

```
$ pwa-asset-generator --help

  Usage
    $ pwa-asset-generator [source-file] [output-folder]

    The assets will be saved to the folder where the command is executed if no output-folder provided.

  Options
    -b --background             Page background to use when image source is provided: css value  [default: transparent]
    -o --opaque                 Shows white as canvas background and generates images without transparency  [default: true]
    -p --padding                Padding to use when image source provided: css value  [default: "10%"]
    -s --scrape                 Scraping Apple Human Interface guidelines to fetch splash screen specs  [default: true]
    -m --manifest               Web app manifest file path to automatically update manifest file with the generated icons [default: ./manifest.json]
    -i --index                  Index HTML file path to automatically put splash screen and icon meta tags in [default: ./index.html]
    -a --path                   Path prefix to prepend for href links generated for meta tags
    -v --path-override          Override the path of images used in href/src tags of manifest and HTML files [default: ./app_icons]
    -t --type                   Image type: png|jpg  [default: jpg]
    -q --quality                Image quality: 0...100 (Only for JPG)  [default: 70]
    -h --splash-only            Only generate splash screens  [default: false]
    -c --icon-only              Only generate icons  [default: false]
    -f --favicon                Generate favicon image and HTML meta tag  [default: false]
    -e --maskable               Declare icons in manifest file as maskable icons  [default: true]
    -l --landscape-only         Only generate landscape splash screens  [default: false]
    -r --portrait-only          Only generate portrait splash screens  [default: false]
    -d --dark-mode              Generate iOS splash screen meta with (prefers-color-scheme: dark) media attr  [default: false]
    -u --single-quotes          Generate HTML meta tags with single quotes  [default: false]
    -x --xhtml                  Generate HTML meta tags by self-closing the tags  [default: false]
    -g --log                    Logs the steps of the library process  [default: true]

  Examples
    $ pwa-asset-generator logo.html
    $ pwa-asset-generator logo.svg
    $ pwa-asset-generator https://your-cdn-server.com/assets/logo.png ./ -t jpg -q 90 --splash-only --portrait-only
    $ pwa-asset-generator logo.svg ./assets --splash-only --xhtml --single-quotes
    $ pwa-asset-generator logo.svg ./assets --scrape false --icon-only --path "%PUBLIC_URL%"
    $ pwa-asset-generator logo.svg ./assets --icon-only --favicon --opaque false --maskable false --type png
    $ pwa-asset-generator logo.svg ./assets --dark-mode --background dimgrey --splash-only --quality 80
    $ pwa-asset-generator logo.svg ./assets --padding "calc(50vh - 5%) calc(50vw - 10%)" --path-override "./your-custom-image-folder-path"
    $ pwa-asset-generator https://raw.githubusercontent.com/charistheo/pwa-asset-generator/HEAD/static/logo.png ./temp -p "15%" -b "linear-gradient(to right, #fa709a 0%, #fee140 100%)"

  Flag examples
    --background "rgba(255, 255, 255, .5)"
    --opaque false
    --padding "10px"
    --scrape false
    --manifest ./src/manifest.json
    --index ./src/index.html
    --path "%PUBLIC_URL%"
    --path-override "./your-custom-image-folder-path"
    --type jpg
    --quality 80
    --splash-only
    --icon-only
    --favicon
    --maskable false
    --landscape-only
    --portrait-only
    --dark-mode
    --single-quotes
    --xhtml
    --log false
```

### Module

pwa-asset-generator is not only a CLI. It's a CLI wrapper around a JavaScript module. It's possible to access the underlying API of the library as it's described in below example;

```javascript
const pwaAssetGenerator = require('pwa-asset-generator');

// Generate images over a module function call, instead of using CLI commands
(async () => {
  const { savedImages, htmlMeta, manifestJsonContent } = await pwaAssetGenerator.generateImages(
    'https://raw.githubusercontent.com/charistheo/pwa-asset-generator/HEAD/static/logo.png',
    './temp',
    {
      scrape: false,
      background: "linear-gradient(to right, #fa709a 0%, #fee140 100%)",
      splashOnly: true,
      portraitOnly: true,
      log: false
    });
})();

// Access to static data for Apple Device specs that are used for generating launch images
const appleDeviceSpecsForLaunchImages = pwaAssetGenerator.appleDeviceSpecsForLaunchImages;
```

## FAQ

### What kind of image input should I use with this library? What are the safe margins for a logo?

Any image format that a Chrome browser can render within an `<img>` HTML tag, is a compatible image input. It can be an icon, an SVG file, a JPEG or PNG logo, even a WebP image.

pwa-asset-generator uses a Chrome tab as an art board. Your input image is being scaled to fit the viewport of the target device resolution while generating splash screens. Since your input image is being scaled for generating splash screens, it's best advised to use a vector image - like an SVG file as an input.

There's no particular safe margin requirement as 10% padding is added around your image input by default with CSS. That being said, you can customize the padding as it's described in the next answer. The library uses a similar approach while generating icons too, with the same default padding 10% around the image input. See it in action [here at this tweet](https://twitter.com/onderceylan/status/1190657108003282945) to understand the concept.

### How can I make an image smaller or larger relative to the background?
The default value for the padding surrounding the image is 10%. But it's just a css padding value that you can configure and override yourself with **-p --padding** option.

1. You can use a more advanced padding value based on your taste and goal;

    **Larger logo:** `--padding "calc(50vh - 20%) calc(50vw - 40%)"`

    **Smaller logo:** `--padding "calc(50vh - 5%) calc(50vw - 10%)"`

2. You can create your own html input file which uses css media queries and provides different padding options based on breakpoints: https://material.io/design/layout/responsive-layout-grid.html#breakpoints

### How can I generate a PNG image with a transparency?
Although the default background color is **transparent**, there's another option that you need to use for generating transparent images: **opaque**.

You need to run your CLI command with `--opaque false` option in order to get the transparency; `pwa-asset-generator logo.svg --opaque false`.

This might be confusing for some, but it's necessary to support the use of background values with alpha channels.

### How can I generate a transparent favicon, and app icons with opaque background?
Default behaviour of the library is to generate a favicon along with app icons. So, it's not possible to generate one without other.

However, you can use this workaround to work with this edge case:

* First, generate a favicon with `--opaque false --icon-only --favicon` options.
* Then, overwrite app icons with `--background "#FFF" --icon-only` options.

### How can I generate both dark mode and light mode splash screen images?
You need to execute two consequent commands in order to generate both dark mode and light mode splash screens for you PWA running on iOS.

Here's a pair of example commands that can be used for generating both modes;

```bash
npx pwa-asset-generator light-logo.svg ./assets --dark-mode --background dimgrey --splash-only --type jpeg --quality 80 --index ./src/app/index.html
npx pwa-asset-generator dark-logo.svg ./assets --background lightgray --splash-only --type jpeg --quality 80 --index ./src/app/index.html
```

> As you can see from the demonstration of dark mode splash screens [at this tweet](https://twitter.com/onderceylan/status/1188221265632350208), users have to re-add a PWA to the home screen in order to react to a system setting change.
>
> **An existing PWA on a home screen will not be able to recognize changed system settings for it's launch image. This is a limitation on iOS.**

### Are deprecated `device-width` and `device-height` media queries necessary?
Even though they're deprecated, `device-width` and `device-height` media queries are still being used by iOS to declare splash screen images for web apps added to a home screen.

When it's an exact match with device's resolution, iOS displays the splash screen as a launch image on bookmarks / PWAs added to a home screen.

### How can I use JSX syntax to generate the meta tags?
If you don't have HTML files in your project, and have a JSX/TSX files instead, you can either use `--xhtml` option or self-generate the meta tags.

The `--xhtml` option allows you to generate the required meta tags with self closing them - `< />`. This will allow copying generated tags directly to a JSX/TSX file.

Alternatively, you can use static data that this library exports to generate the required meta tags! pwa-asset-generator exposes the static Apple device specification data via it's module API. Here's an example JSX snippet;

```jsx
import { appleDeviceSpecsForLaunchImages } from 'pwa-asset-generator';

render() {
  return (
    <>
      {appleDeviceSpecsForLaunchImages.map((spec) => {
        return (
          <>
            <link
              key={`apple-splash-${spec.portrait.width}-${spec.portrait.height}`}
              rel="apple-touch-startup-image"
              href={`apple-splash-${spec.portrait.width}-${spec.portrait.height}.png`}
              media={`(device-width: ${spec.portrait.width /
                spec.scaleFactor}px) and (device-height: ${spec.portrait
                .height /
                spec.scaleFactor}px) and (-webkit-device-pixel-ratio: ${
                spec.scaleFactor
              }) and (orientation: portrait)`}
            />
            <link
              key={`apple-splash-${spec.portrait.width}-${spec.portrait.height}`}
              rel="apple-touch-startup-image"
              href={`apple-splash-${spec.portrait.width}-${spec.portrait.height}.png`}
              media={`(device-width: ${spec.portrait.height /
                spec.scaleFactor}px) and (device-height: ${spec.portrait
                .width /
                spec.scaleFactor}px) and (-webkit-device-pixel-ratio: ${
                spec.scaleFactor
              }) and (orientation: landscape)`}
            />
          </>
        );
      })}
    </>
  );
}
```

### My index.html file's format has been changed after an automated update. Is there any way to re-format it?

pwa-asset-generator uses [cheerio](https://cheerio.js.org/) for maintaining HTML tags in your index.html file. While doing so, it creates extra whitespaces and it might also introduce misaligned indentations.

A recommended way to maintain the same format for your index.html file would be using [Prettier](https://prettier.io/) and adding the following script to your project's package.json file.

```
"format:index": "prettier \"index.html\" --write"
```

Executing `npm run format:index` after using pwa-asset-generator assures the same format for your HTML file.

### When generating PNG images, there's no compression settings. Is there a way to introduce compression for PNG files?

pwa-asset-generator depends on Puppeteer and it's screenshot API for image generation. Puppeteer doesn't provide compression settings for PNG files for the time being.

However, you can use one of the lossless / lossy compression libraries - like [pngquant](https://pngquant.org/) to compress the generated PNG images.

## Troubleshooting

### "No usable sandbox!" error on Linux
In case of getting "No usable sandbox!" error on Linux, you need to enable [system sandboxing](https://github.com/GoogleChrome/puppeteer/blob/master/docs/troubleshooting.md#setting-up-chrome-linux-sandbox).
