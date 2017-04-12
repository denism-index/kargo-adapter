# Table of Contents
1. [Introduction](#intro)
    * [Repo Structure](#repo)
    * [Getting Started](#gettingStarted)
2. [Questionnaire](#questionnaire)
3. [Requirements](#requirements)
4. [Partner Module Overview](#overview)
    * [Configuration](#configuration)
    * [Event Model](#eventModel)
    * [Creating a Partner Module](#creatingPartnerModule)
5. [Utility Libraries](#helpers)
    * [Utils](#utils)
    * [Network](#network)
    * [BidRoundingTransformer](#bidRounding)
6. [Testing](#testing)

# <a name='intro'></a>Introduction

<b>Welcome to the Index Exchange Partner Certification Process! </b>

Below you will find everything you need to complete the certification process and be a part of the Header Tag Wrapper! Should you have any questions, please refer to this README first, it should cover everything you need to complete the certification.

Good Luck!

## <a name='repo'></a>Repo Structure
* `README.md` (this file)             - this is the main documentation file which should contain everything you need to complete the certification process. If anything is unclear, please refer to this document first.
* `partnerConfig.js`                - this is the configuration object that contains your partner specific configs.
* `headerTagWrapper.js`               - a minified version of our Header Tag Wrapper.
* `index.html`                        - a simple test page that contains some googletag slots and loads our wrapper for testing purposes.
* `partnerModule.js`                  - this is your partner module file, by default it contains a template divided into multiple sections which need to be filled out.

##  <a name='gettingStarted'></a>Getting Started
0. <b> Fill out the Certification Questionnaire</b>
    * This is a standard [questionnaire](#questionnaire) that is required for every partner that goes through the certification process.
    * It is required to help us understand and setup your module correctly on our end.
    * Please refer to the requirements and guidelines section of this README before filling out the [questionnaire](#questionnaire).
    * <b>Your answers should be submitted as part of the merge request along with the rest of your bidder code.</b>
1. <b>Clone and host this repo locally</b>
    * Once the repo is hosted locally, you should be able to see a simple test page with 4 googletag slots (3 desktop and 1 mobile).
    * By default, the Index Exchange bidder will be bidding $1.50 on all slots except the top-most 728x90 slot. 
        * By default you should see Index Exchange certification ads rendering on all slots except that 728x90 slot, which should be rendering a fallback/house ad.
    * Once you have setup your module, you can test the auction by bidding higher or lower to see your ads winning.
2. <b>Fill out partnerModule.js</b>
    * partnerModule.js is where all of your partner code will live.
    * In order to fill this partnerModule correctly, please refer to the [Partner Module Overview](#overview) and the [Utility Libraries](#helpers) sections.
    * <b>Please refer to the [Partner Requirements and Guidelines](#requirements) when creating your module, these are strictly enforced.</b>
    * The module is automatically loaded by the test page, so once your code has been added, you will be able to test it right away.
3. <b>Fill out partnerConfig.js</b>
    * This file is where your partner-specific configuration and slot mappings are be defined.
    * By default, the partners object will contain two entries (INDX and your module-specific code).
    * Please fill out your module's configuration with appropriate xSlot data and mappings. For more information on those, refer to the [Configuration](#configuration) section of this README.
4. <b>Testing and Verification</b>
    * Once your partner module has been filled out and your partner-specific configuration has been added to partnerConfig.js, you are ready to start testing.
    * Open the index.html test page and you should see the HeaderTagWrapper loaded as well as your partnerModule.
    * In order to confirm your module's functionality, please complete the required [test plan](#testing) outlined below in this readme.
5. <b>Submitting for Review</b>
    * Once the module has been verified, please submit a pull request for the Index Exchange team to review and certify your bidder.

# <a name='questionnaire'></a> Partner Certification Questionnaire
Please submit your answers as part of the merge request along with the rest of your bidder code.

### Configuration
* What configuration terminology should our Index Exchange implementation engineers use when asking for your partner specific configuration?
* What language do you refer to your inventory/configuration? Examples of what we've seen from other exchanges include Placement Ids, Ad Units, Ad Slots, Tag Ids, etc.  

### Bid Request Endpoint
* Does the adapter support Private Market Place guaranteed deals  ie. deal IDs? or deals that compete with open market.
* How is this defined in the bid response? Do we check presence of dealID parameter?
* Does the adapter support multiple sizes per slot? ie. Passing in multiple sizes for a single ad unit.

### Bid Response
* What is the format of the returned bids? (i.e. currents, cents/dollars, net/gross by default)

### Test and Mocking Bids
* Are you able to provide us a test endpoint, test parameters for the endpoint, or test configuration which can address the following needs to improve our ability to test the partner module:
    * Returns open market bid consistently.
    * Should support a flag to return deal bids.
    * Should support a flag to pass on bid.
* Request parameters match with the partner's production end points. (This is to ensure the test endpoint is an accurate representation of production.
* Can you please send us an example response, these should include examples of the following:
    * Open Market and/or Private Market Bids
    * Passes
    * Deal responses

### DFP Setup
* Typically, Index Exchange will be setting up the DFP line items with our predefined keys and standard creative tag.
    * Are there any special keys or creative tags that you must use in your header bidding workflow that we should be aware about?

Our standard creative tag in dfp looks as follows:
```html
<script type="text/javascript">
var w = window;
for (var i = 0; i < 10; i++) {
    w = w.parent;
    if (w.headertag) {
        try {
            w.headertag.<PARTNER_ID>.render(document, %%PATTERN:TARGETINGMAP%%, '%%WIDTH%%', '%%HEIGHT%%');
            break;
        } catch (e) {
            continue;
        }
    }
}
</script>
```

<br>

# <a name='requirements'></a> Partner Requirements & Guildelines
In order for your module to be certified, please refer to the following list of requirements and guidelines.
Items under required <b><u>must</b></u> be satisfied in order to pass the certification process. Items under recommended are recommended for an optimal setup and should be followed if possible.

### General

#### Required
* The only targeting keys that can be set are predetermined by Index. The partner module should not be setting targeting on other keys.
* Must support the following browsers: IE 9+, Edge, Chrome, Safari, and Firefox

#### Recommended
* Prefetch should be implemented and enabled by default. Prefetch reduces the publisher's page load time.
* If you've decided to implement prefetch and the demand from your ad server expires after a certain amount of time, please configure a global demand expiry within your plugin. The wrapper must know when to fetch the demand again in the case where prefetch demand expires.
* Please use our helper libraries when possible. Refer to our utilities library documentation below. All of the utility functions in this library are designed to be backwards compatible with most browsers, hence no need to worry about browser compatibility.

### Bid Request Endpoint

#### Required
* Must provide cache busting parameter. Cache busting is required to prevent network caches.
* Partner endpoint domain must be consistent and cannot be configurable. Load balancing should be performed by the endpoint.

#### Recommended
* Your module should support single request architecture (SRA) which has a capability to send multiple slots and multiple sizes in a single request data.
* Your endpoint should support HTTPS request. When wrapper loads in secure pages, all requests need to be HTTPS. If you're unable to provide a secure endpoint, we will not be able to make requests to your ad servers.
* Partner should use AJAX method to make bid requests. Please use our [Network](#network) utility library for making network requests, particularly the `Network.ajax` method. This ensures the requests will work with all browsers.
* Should support a currency input parameter. Otherwise, all returned bid must be in USD.

### Bid Response

#### Required
* Returned bid must be a <b>net</b> bid.
* Pass on bid must be explicit in the response. When you pass on a bid, it must be explicit such that the wrapper does not need to wait until auction times out.
* The current of the bid must be in USD unless it is configurable in the bid request.
* Size must be returned in the response. Sizes that publisher requested must be returned. This size parameter will be validated.
* Encrypted prices must be flagged in the response.

### Pixels & Tracking Beacons

#### Required
* Pixels/beacons must be dropped only when the partner wins the auction and their ad renders. 
* Dropping pixels during auction slows down the execution of auctions and is not allowed by Index Exchange.

### DFP Setup

#### Required
* DFP line items, creatives, and render functions will be set up by the Index Exchange.
<br><br>

# <a name='overview'></a> Partner Module Overview

## <a name='configuration'></a>Configuration

The main configuration object that is used by the Header Tag Wrapper is the `headertagconfig` object. This configuration is configured by the publisher.
This object contains all the configuration required for the wrapper, including partner specific slot mappings, prefetch settings, timeouts, and any other partner specific configuration.
The partner specific slot mappings dictate how ad slots on the page will map to partner specific configuration.
There are 2 concepts to be familiar with when understanding how slots on the page are mapped back to partner specific configuration. Header Tag Slots, which refers to htSlots and partner-specific configuration, which refers to xSlots in the codebase.
* htSlots - This is an abstraction of the googletag.Slot object.
  * htSlot id's will be passed into the partner's `getDemand` and `prefetchDemand` functions.
  * These will need to be mapped to xSlots.
* xSlots - These are also an abstraction for partner specific configuration that will be mapped to htSlots.
  * These represent a single partner specific configuration.
  * An xSlot is how a partner can map their ad server specific identifiers (placementIDs, siteIDs, zoneIDs, etc) to the `googletag.Slot` object represented as an `htSlot`.
  * It can represent a single or multiple sizes.
  * Multiple xSlots can be mapped to the same htSlot.
  * An xSlot can only be mapped to a single htSlot.

Example Partner Configuration Mapping
```javascript
{
    "partners": {
        "TPL": {
            "xSlots": {
                "xSlotID1": {
                    "placementID": "123",
                    "sizes": [ [300, 250], [300, 600] ]
                },
                "xSlotID2": {
                    "placementID": "345",
                    "sizes": [ [300, 250] ]
                }
            },
            "mapping": {
                "htSlotID-1": [ "xSlot1" ],
                "htSlotID-2": [ "xSlot2" ]
            }
        }
    }
}
```
The partner module would need to map the htSlotIDs to xSlots when demand is requested. 

## <a name='eventModel'></a> Event Model

1. The Header Tag Wrapper script tag is loaded on the page.
2. Wrapper specific configuration validation is performed.
3. All the partner modules are instantiated.
    * Partner specific configuration validation is performed - checking that all the required fields are provided, that they in correct format, etc.
4. If prefetch is configured for the partner, the wrapper requests demand from the partner modules before any googletag display calls are made. Prefetch is a feature that can be implemented inside of the partner module to retrieve demand in advance.
    * A pre-configured set of slots (prefetch slots) are requested from each partner module prior to when a google tag display call is made.
    * The wrapper calls `prefetchDemand` for every partner module that has that setting enabled.
    * The call provides the partner module with an array of `htSlots` that need to be fetched.
    * The partner module then maps `htSlots` to respective `xSlots` and requests demand for them.
    * Once requests return, the demand is stored in a `demandStore` object for later consumption.
5. A googletag display or refresh call is made.
The wrapper again (similar to prefetch) requests demand from the partner modules for the required slots (the slots being refreshed or displayed).
    * The wrapper calls `getDemand` for every partner module.
    * The partner module again maps the required `htSlots` to the respective xSlots.
    * If the partner module has previously prefetched the required slots in the prefetch stage, that demand is used.
    * If not, demand is fetched by the partner module and stored in in a demand object.
    * Once all demand for all required slots have been retrieved by the partner, the demand is returned to the wrapper.
6. The wrapper applies targeting using the keys specified by the partner.
7. If the partner wins the auction in gdfp, their creative code will be returned and executed.
    * The creative code contains a call to a partner module render function.
8. The partner ad is rendered.

## <a name='creatingPartnerModule'></a> Creating a Partner Module

### Step 1: Module Initialization (Sections A, B, C and E)
The parter module needs to initialize itself. This includes validating the configuration its been provided with. 

#### Section A
This section is mainly about setting up the behavior of partner itself. Things like targeting types, partnerID (used internally to reference the partner), different types of analytics and whether prefetch is supported.
Everything in this section can be left as is except the partnerID.

#### Section B
This section is for validating partner specific configuration that is provided by the user. Slot mappings and other fields that are required for all bidders are validated for you. If there are partner specific fields that need validation, this is the place to validate them.

#### Section C
Similar to section B but for any partner specific xSlot mapping information (i.e. placementID must be a number).

#### Section E
This section is for simply copying any useful information (such as slot mappings) from the main configuration object to internal variables. Creating a direct map from htSlotID to an xSlot and vice versa would be useful to have and can be done here.

### Step 2: Prefetch Demand (Sections F and G)
This step is for configuring how demand will be prefetched when the wrapper invokes this modules `prefetchDemand` function. This is an optional step and only needs to be implemented if the partner wishes to support the prefetch feature.

#### Section F
In this section, the partner module needs to request demand for the htSlots that are provided in the info variable. The info variable contains the htSlotIds for which demand needs to be gathered. These `htSlotIDs` need to be first mapped back to partner specific xSlots.
The partner module should then make a demand request to their server. Once the request is complete, the returned demand should be processed, and stored under the htSlotID that it was initially requested for in the `demandStore[correlator]` object for later consumption.
Any returned creative code must be stored inside the global `creativeStore` object.

#### Section G
Nothing needs to be done in this section except making sure that the prefetch callbacks get executed.

### Step 3: Get Demand (Sections H and I)
This step is for retrieving demand when a googletag display or a refresh call is made.

#### Section H
In this section the partner must fetch demand for the requested htSlots that was provided in the slots object. These slots must be mapped back to xSlots and the appropriate xSlots must be fetched.
Once all slots have been fetched and parsed, they must be mapped back to htSlotIDs and be placed in a demand object. In this format:
```javascript
{
    slot: {
        <htSlotId>: { // the htslotID
            timestamp: Utils.now(),
            demand: {
                <key>: <value>, // key is the targeting key, should be targeting
                <key>: <value>,
                ...
            }
        },
        ...
    }
}
```
Any returned creative code must be stored inside the global `creativeStore` object using some sort of unique id and size.
The targeting keys should be the keys found in the `targetingKeys`. This should include the `omKey` or `pmKey` key for the cpm or deal as well as an `idKey` used to correlate the request with the creative to be used to retrieve the creative from the `creativeStore` object in the `renderAd` function if they partner is to win the auction. The targeting is passed to the `renderAd` function on a win, which is then used to retrieve the creative.
Once all the demand has been gathered, the partner module should invoke the provided callback with the demand as an argument.

#### Section I
This section is an optional response callback that can be used for parsing any demand that is returned.

### Step 4: Rendering (Section J)
This step is for rendering the winning creative. If the partner module's line item wins, the creative code will be returned and inserted into the iframe for that googletag slot. The standard creative code will contain a call to the partner module's specific `renderAd` function.
The function in Section J should work as is as long as the creative is stored correctly in the `getDemand`/`prefetchDemand` functions.

# <a name='helpers'></a> Utility Libraries
There are <i>three</i> helper objects available to you in you partner module.
* Utils - contains numerous utility and validation helper methods.
* Network - contains helper methods for making network request.
* BidRoundingTransformer - contains helper methods for rounding and transforming bids.

## <a name='utils'></a> Utils

### Value Validation

* `isObject(entity)` - Return true if entity is an object.
* `isArray(obj)` - Return true if obj is an array.
* `isNumber(entity)` - Return true if entity is a number.
* `isString(entity)` - Return true if entity is a string.
* `isBoolean(entity)` - Return true if entity is a boolean.
* `isFunction(entity)` - Return true if entity is a function.
* `isEmpty(entity)`
    * if entity is a string, return true if string is empty.
    * if entity is an object, return true if the object has no properties.
    * if entity is an array, return true if the array is empty.
* `isInRange(value, min, max)` - return true if `max > value > min`.
* `isArraySubset(arr1, arr2, matcher)` - Return true if `arr1` is a subset of `arr2`.

### Google Publisher Tag
* `getGSlots()` - Return an array of google tag slots on the page.
* `divIdToGSlot(divId)` - Return the googletag slot associated with `divId`.
* `getGSlotDivIds(slots)` - Return the divIDs of the google tag slots `slots`.

### System
* `now()` - Return the number of milliseconds since 1970/01/01.

### Browser
* `getDeviceTypeByUserAgent()` - Return 'mobile' or 'desktop' based on userAgent
* `getViewportWidth()` - Return viewport width.
* `getViewportHeight()` - Return viewport height.
* `getProtocol(httpValue, httpsValue)` - Return `document.location.protocol` or `httpValue` if `document.location.protocol` is http and `httpsValue` if `document.location.protocol` is https.
* `getPageUrl()` - Return the page's url.
* `addScriptTag(src, asyncEnabled, onLoad)` - Appends a script tag onto the page with `src`, using `asyncEnabled` to toggle async and `onLoad` callback when the script finishes loading.

### Utilities
* `generateCorrelator()` - Generates a random correlator value, used internally inside the header tag wrapper to identify different sessions.
* `randomSplice(arr)` - Randomly splice an array `arr`.

## <a name='network'></a> Network
* `objToQueryString(obj)` - Creates a query string based on the properties of the object `obj`.
* `isXhrSupported()` - Return true is Xhr requests are supported in the current browser/page.
* `buildUrl(base, path, queryString)` - Return a url string using the given `base` url, `path`, and `queryString` object.
* `ajax(args)` - This should be the default method for making all of your bid requests. 
Make an ajax network request using the `args` object, which should be of the form: 
    * `url` - url string
    * `method` - the method of the request (i.e. GET).
    * `partnerId` - the id of the partner, used for stats.
    * `jsonp` - flag to signifify a jsonp request.
    * `withCredentials` - withCredentials flag.
    * `onSuccess` - on success callback.
    * `onFailure` - on failure callback.
* `jsonp(args)` - Same as ajax but only for jsonp requests, use this if the above does not work.

## <a name='bidRounding'></a> BidRoundingTransformer
* `transformBid(rawBid)` - Transform rawBid into the current format. This includes, rounding/flooring according to the bidTransformConfig that was used to instantiate the library. The bidTransformConfig is an object of the format:
    * `floor` - Minimum acceptable bid price.
    * `inputCentsMultiplier` - Multiply input bids by this to get cents.
    * `outputCentsDivisor` -  Divide output bids in cents by this.
    * `outputPrecision` - Decimal places in output.
    * `roundingType` - Should always be 1.
    * `buckets` - Buckets specifying rounding steps.

Example of `bidTransformConfig`:

```javascript
var bidTransformConfig = {          // Default rounding configuration
    'floor': 0,
    'inputCentsMultiplier': 100,    // Input is in dollars
    'outputCentsDivisor': 100,      // Output as dollars
    'outputPrecision': 2,           // With 2 decimal places
    'roundingType': 1,              // Floor instead of round
    'buckets': [{
        'max': 2000,                // Up to 20 dollar (above 5 cents)
        'step': 5                   // use 5 cent increments
    }, {
        'max': 5000,                // Up to 50 dollars (above 20 dollars)
        'step': 100                 // use 1 dollar increments
    }]
};
```

<br><br>
# <a name='testing'></a> Partner Module Testing & Verification
Before submitting your module for review by Index Exchange, your module <b>must</b> satisfy the below test cases.

1. The partner module is sending bid requests.
    * Sending requests for the correct slots, placements, sizes, etc.
    * The bids are hitting the correct url with the correct query parameters.
2. The partner module is correctly parsing the returned bid responses.
    * The bid responses are being parsed correctly.
    * The bids are being stored correctly in `demandStore` (for prefetch) and `demand` (for regular demand).
    * The creatives are being stored correctly in the `creativeStore` object.
3. Targeting is correctly applied and present on google tags `ads?` requests.
    * The partner specific keys (ix_PARTNERID_cpm, ix_PARTNERID_id, etc) should be present.
    * The current values for those keys should be set.
4. Partner-Specific test ads are displaying.
    * In order to see your test ads displaying, you must win the auction in DFP.
        * We have 2 sets of test line items setup for your specific targeting key.
            * One for $1.0 bids and one for $2.0 bids. This is so you can see win/loss against our default index bids of $1.5.
        * Hence to see your ads displaying, you must submit $2.0 bids into dfp through your targeting key.
            * For example, ix_PARTNERID_cpm=300x250_2.0
    * Your returned creatives should be activating line items and winning with the correct bids.


