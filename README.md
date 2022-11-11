## Craft Optimum

This plugin allows the user to conduct server-side A/B testing in Craft.
As opposed to client-side testing (e.g using Google Optimize), the test variant is rendered on the server-side, resulting in better UX, performance and enhanced flexibility.

Once an experiment is set the data is automatically sent to Google Analytics 4 as a [custom dimension](https://support.google.com/analytics/answer/10075209).
You then have the full power of analytics to compare the test groups over different metrics (e.g conversion, engagement etc.)

### Requirements

1. Craft CMS 4.x or later.
2. A Google Analytics 4 (GA4) Account. 
3. Google Tag Manager script installed on the page (type `gtag` in the browser console to verify).

### Installation

1. Include the package:

```
composer require matfish/craft-optimum
```

2. Install the plugin:

```
php craft plugin/install optimum
```
### Usage

#### 1. Create an experiment in the control panel:
Click on the new "Experiments" menu item and then click on "New Experiment".
An experiment consists of the following fields:
- **Name** E.g 'Banner Types'
- **Handle** - E.g `bannerTypes`.  This will be used in two places:
  1. In naming the folder for the variants' templates.
  2. As the param name, when setting up GA4's custom dimension 
- **Enabled?** - Weather the experiment is currently active. This can be used to pause or permanently stop the experiment
- **Variants** - The different variants for the experiment. E.g if you are testing different hero banners you would set their reference here. An "original" variant is pre-set and refers to the control group, represented by your current code. It cannot be modified or deleted, other than its weight.
    Each variant comprises three fields:
  - Name: Human readable name. This will be sent to GA4 as the value (E.g "Wide Banner")
  - Handle: Used for naming the variant template in twig (E.g "wideBanner)
  - Weight: Relative weight (probability) in percents (e.g 40). The sum of all variants' weight must add up to 100%
- **Starts at**: Optional field to defer the experiment. If left empty experiment starts immediately (assuming that "Enabled?" is on).
- **Ends at**: Set to 30 days in the future by default.
#### 2. Create the variants in twig
Wrap the part of code you wish to test (your "original" variant) with the `{% optimum 'experimentHandle' %}` tag like so:

```html
{% optimum 'bannerTypes' %}
   // Original Variant Code
   <img src="original_banner.jpg"/>  
{% endoptimum %}
```
Then create templates corresponding to each variant (except for "original") using the following naming convention:
`_optimum/{experimentHandle}/{variantHandle}.twig`

E.g:
 - `_optimum/bannerType/wideBanner.twig`
 - `_optimum/bannerType/narrowBanner.twig`

Inside the template paste the code for the variation you wish to test. E.g:
```html
   // Wide Banner Variant Code
   <img src="wide_banner.jpg" class="wide-banner"/>  
```
#### 3. Set a Custom Dimension in GA4
The last piece of the puzzle is telling GA4 to aggregate the events sent from your site into a custom dimension.
1. Open GA for your property and go to **Configure->Custom Definitions**
2. Click on the **Create custom dimensions** button
3. In the Modal select **Dimension Name** (can be anything you want) and **Scope** (Event or User). Then under **Event parameter** type the experiment handle (e.g `bannerType`).
4. Click "Save"

------
All Done! Once GA has collected enough data, you can start comparing the performance of the different cohorts/test groups.

### Troubleshooting
Before opening an issue please make sure that:
1. Cookies are enabled 
2. Caching is disabled (e.g Blitz), as plugin decides in real-time which variant to serve.
3. GTM is installed on the page (type `gtag` in the console to verify).

### Caveats

- Note that the code inside the `optimum` tag is scoped. So variables defined inside the tag block containing the original variation (or in the variant templates) will not be available externally.
### License

You can try Activity Log in a development environment for as long as you like. Once your site goes live, you are
required to purchase a license for the plugin. License is purchasable through the [Craft Plugin Store](https://plugins.craftcms.com/optimum).

For more information, see Craft's [Commercial Plugin Licensing](https://craftcms.com/docs/4.x/plugins.html#commercial-plugin-licensing).
