# Eleventy Plugin: Generate Social Images (using SVG)

Dynamically generate social media images for your Eleventy (11ty) pages. Unlike other similar plugins, this one uses SVG & does not depend on Puppeteer!

---

- [Eleventy Plugin: Generate Social Images (using SVG)](#eleventy-plugin-generate-social-images-using-svg)
  - [Introduction](#introduction)
  - [How does it work?](#how-does-it-work)
  - [Installation and Usage](#installation-and-usage)
    - [STEP 1: Install the package](#step-1-install-the-package)
    - [STEP 2: Include it in your `.eleventy.js` config file](#step-2-include-it-in-your-eleventyjs-config-file)
    - [Step 3: Use in your template](#step-3-use-in-your-template)
  - [Config Options](#config-options)
  - [Custom Fonts](#custom-fonts)
  - [Credits](#credits)

## Introduction

This plugin provides an 11ty ShortCode that can be used to automatically generate social images in your Eleventy website.

For example:

```
{% GenerateSocialImage "Eleventy plugin for generating social images (using SVG)" %}
```

will generate the following social image _(website, email address and author-image are set during configuration)_:

The social image is first created as SVG and then converted to PNG using [Sharp](https://github.com/lovell/sharp).

## How does it work?

* Generates the image using SVG and then converts it into PNG using [Sharp](https://github.com/lovell/sharp).
* Custom logic to wrap the title line in SVG (as Sharp does not support SVG foreignObject).
* Adds an author/promo image using Sharp composite (as Sharp does not support external image in SVG).

## Installation and Usage

### STEP 1: Install the package

```bash
npm install @fat-buddha-designs/eleventy-social-images/
```

### STEP 2: Include it in your `.eleventy.js` config file

```js
const generateSocialImages = require("@fat-buddha-designs/eleventy-social-images/");

module.exports = (eleventyConfig) => {
  eleventyConfig.addPlugin(generateSocialImages, {
    promoImage: "./src/img/my_profile_pic.jpg",
    outputDir: "./_site/img/preview",
    urlPath: "/img/preview",
    siteUrl: "Website ~ https://fatbuddhadesigns.co.uk/",
    siteEmail: "Email ~ info@fatbuddhadesigns.co.uk/",
    titleColor: '#dfa634',
    bgColor: '#fff',
    terminalBgColor: '#333',
    lineBreakAt: '48'
  });
};
```

or for ESM

```js
const generateSocialImages from "@fat-buddha-designs/eleventy-social-images/";

export default async function (eleventyConfig) {
  eleventyConfig.addPlugin(generateSocialImages, {
    promoImage: "./src/img/my_profile_pic.jpg",
    outputDir: "./_site/img/preview",
    urlPath: "/img/preview",
    siteUrl: "Website ~ https://fatbuddhadesigns.co.uk/",
    siteEmail: "Email ~ info@fatbuddhadesigns.co.uk/",
    titleColor: '#dfa634',
    bgColor: '#fff',
    terminalBgColor: '#333',
    lineBreakAt: '48'
  });
};
```


### Step 3: Use in your template

For example, in your `base.njk` template file, use it in the `<head>` for generating social image meta tags:

```html
<meta property="og:image" content="{% GenerateSocialImage title %}" />
<meta name="twitter:image" content="{% GenerateSocialImage title %}" />
```

## Config Options

| Option      | Type   | Default       | Description |
| ----------- | ------ | ------------- |-------------|
| promoImage  | string |               | Path to a promo Image (ideally, circular) that will be embedded in the social-images |
| outputDir   | string | "./\_site/img/preview" | Project-relative path to the output directory where images will be generated |
| urlPath     | string | "/img/preview" | A path-prefix-esque directory for the &lt;img src&gt; attribute. e.g. `/img/` for `<img src="/img/MY_IMAGE.jpeg">` |
| siteUrl    | string |               | The website url to show on the social-image |
| siteEmail    | string |               | The website email address to show on the social-image |
| titleColor  | string | "white"       | The color of the page-title |
| bgColor     | string |               | Optional background color. Otherwise, shows the gradient pattern |
| terminalBgColor| string | "#404040"  | Background color of the terminal window design |
| hideTerminal  | boolean | false      | If true, hides the terminal window design behind the title |
| customSVG     | string  |            | Custom SVG code to be added to the image. Use this to add your own design or text anywhere on the image |
| customFontFilename | string |        | Filename of custom local font used for title ([see **Custom Fonts**](#custom-fonts)) |
| lineBreakAt  | number | 35           | Maximum row length for wrapping the title. Required because SVG does not have auto-wrapping text. Should depends on the font used |

## Custom Fonts

The [Sharp library](https://github.com/lovell/sharp) uses librsvg that uses [fontconfig](https://www.freedesktop.org/software/fontconfig/fontconfig-user) to load external fonts. Therefore, the following steps are required:

1. Download your font file in project sub-folder. Eg: `./fonts/sans.ttf`
2. Create a file `fonts.conf` with the following content:

   ```xml
 <?xml version="1.0"?>
 <!DOCTYPE fontconfig SYSTEM "fonts.dtd">
 <fontconfig>
  <dir prefix="default">fonts</dir>
 </fontconfig>
 ```

3. Setup the following environment variable on your build server (eg: Netlify):

   ```bash
   FONTCONFIG_PATH=.
   ```

## Credits

* Original idea from [eleventy-plugin-social-images](https://github.com/manustays/eleventy-plugin-generate-social-images)
* I created my own version to allow for the website URL and email address