On October 7th 2023, Hamas launched a terror attack on Israel, slaughtering hundreds of innocent civilians, brutally murdering babies and raping women. They then proceeded to rain thousands upon thousands of rockets at every civilian target they could aim at. Many people have to live with the fear that they could be outside at any time when the rocket sirens go off, without knowing if there's any safe place nearby to take shelter in.

We decided to do something about that.

# Overview
We built a website named Miklat Finder that allows users to find protected spaces ('miklats') nearest to their location (or from a given address) in a given city. This site is both desktop and mobile friendly.

The site has the following features:
- search for the 3 nearest miklats to the user's location
- search for the 3 nearest miklats to a given address (OR by clicking on the map once it has been loaded)
- show additional miklats nearby
- gets Google Maps walking directions to a given miklat (by tapping on it)
- differentiates between public and private miklats
- get additional instructions on what to do if you can't get to a miklat in time (taken from Pikud Haoref's website)


# API and site hosting
Miklat Finder uses the Google Maps Javascript API and Places API to query for map data and address search data. Additionally, we use the Distance Matrix API to calculate approximate time (in seconds) to walk to the nearest miklat. You will need to setup your own application in Google Cloud and enable billing to get access to such data (you may be able to get free credits for a trial). Alternatively, you can use a different API -- more on that below.

We are hosting the site via GitHub Pages, so you may need to modify the file structure here to get it to work elsewhere (GitHub Pages also cannot deploy to dev urls). The database is hosted on jsonbin.io.

**Note:** in order to prevent unauthorized/malicious access, we set our Google API key to only accept requests from the url of the site. You may wish to do the same, and have a separate API key for testing purposes.

## Using another map API
While Google Maps is the default API used by Miklat Finder, the code is de-coupled in such a way as to allow for other APIs to be used in place of it. All API-specific code can be found in `map-api.js`, and the Google Maps API libraries are loaded in `<head>` tag of `index.html` .

# Adapting the code
To adapt Miklat Finder for your particular city, you will need to do the following:

**index.js**
 - in the `<head>` tag, replace `|YOUR KEY|` with your Google Maps API key
 - in the `<h1>` tag, replace `|YOUR CITY|` with your city name (in all capitals)
 - replace both instances of abc@example.com with your developer email

**en.json/he.json/other locale files**
- replace all instances of `|YOUR CITY|` with your city name (the `banner-message` version should be in all capitals)
- replace all instances of abc@example.com with your developer email

**city.js**
- replace all coordinates in `CITY_COORDS` with a list of arrays that contain the latitude+longitude boundaries of your city

**map.js**
- replace `<DATABASE URL>` with the url where your database is being hosted

If you wish to host the database locally, follow these steps:
1. Comment out `MIKLATS = null;` in `map.js`
2. Comment out the inner body of `fetchMiklats` in `map.js`
3. In the database file, add `MIKLATS = ` to the beginning. It should look something like this (no need for `record` field):
`MIKLATS = [{
  "name": "(name here)"...`
4. Add `<script src="<FULL PATH TO DATABASE FILE>"></script>` below `<link rel="stylesheet" href="styles.css">`


# Database format
The database used by Miklat Finder is a JSON file that contains a field named `record` with a list of key/value pair objects. These objects have the following fields (note that fields with a * are not currently used by this version of the Miklat Finder):

    "name"*: miklat name. String value
    "address": address of the miklat. Miklats with blank addessses will be ignored
    "lat": latitude coordinate of the miklat. Float value
    "long": longitude coordinate of the miklat. Float value
    "size": size of the miklat in meters squared. Integer value
    "type"*: type of miklat (eg 'Public', 'Apartment', etc). String value
    "description"*: description of the miklat. String value
    "isPublic": whether or not the miklat is public. Boolean value
    "comments": additional info about the miklat (eg 'floor -1'). String value
    "cityID"*: city id of the miklat. Only used for public miklats. Integer value
    "addressHeb": address of the miklat in Hebrew. String value
    "nameHeb"*: miklat name in Hebrew. String value
    "commentsHeb": additional info of the miklat in Hebrew. String value
   If the English address/comment field is missing (or empty), but the Hebrew is not, the Hebrew version will be used (and vice versa). If an additional language is supported (and selected) and an address/comment field is missing, the languages will be selected in the following order of what is available:
   >[language selected] ---> Hebrew ---> English

To add additional languages support for the database, each miklat entry should have an `address<LOCALE CODE>` field and a `comments<LOCALE CODE>` field. The locale code can be any variation (eg ru, RU, \_RU, etc) so long as it contains the locale code that is set in the `<head>` tag of `index.html`.

# Adding additional languages
Miklat Finder by default supports both Hebrew and English. More languages can be added pretty easily -- copy the `en.json` file in `locales/` and name it `<LOCALE CODE>.json`, change the values to their appropriate translation, and change `EN_JSON` to `<LOCALE CODE>_JSON`. Once you've done all that, follow the following steps.

 1. add the `<script src="locales/<LOCALE CODE>.json"></script>` tag above the `<meta>` tag
 2. add the locale code to `langsAvailable` array constant
 3. add the locale code+direction to `direction` Object constant
 4. add the following to the `switch` statement:
		 `case "<LOCALE CODE>":
         document.title = "<MIKLAT FINDER TRANSLATED>";
         break;`
 5. append a new `<button>` element to the first`<div>` of the `<body>` tag that looks like the following:
`<button onclick="<LTR OR RTL METHOD>(); localizePage('<LOCALE CODE>'); document.title = '<MIKLAT FINDER TRANSLATED>';">LANGUAGE NAME</button>`
6. in `localization.js`,  add the following to the switch statement:
	`case "he":
       locale_json = <JSON VARIABLE NAME>;
       break;`
Ta-da! Your version of Miklat Finder now supports that language :)

Final note: if you decide to add to the instructions below the map, make sure to list in the disclaimer where the information was pulled from.

Stay safe everyone! Am yisrael chai!
