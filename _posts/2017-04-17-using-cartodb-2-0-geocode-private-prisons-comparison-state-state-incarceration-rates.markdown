---
title: Using CartoDB to visualize the relationship between private prisons and incarceration
  rates
date: '2017-04-17T20:08:00.000+00:00'
excerpt: The last post I wrote about CartoDB used the CartoDB API and Leaflet.js to produce a hover tooltip map of census data. Looking back, while it's quite useful to learn CartoDB's API, it's now possible to achieve those same visual cues and interactivity without spending time wading through complex, undocumented JavaScript.
categories:
- Data Visualization
- Mapping
tags:
- cartodb
- geocoding
- geospatial
- private-prisons
---

<p>The <a href="http://carlvlewis.net/visualizing-latest-census-estimates-using-cartodb-and-leaflet/" target="_blank">last post</a> I wrote about <a href="http://cartodb.com" target="_blank">CartoDB </a>used the CartoDB API and <a href="http://leaflet.js" target="_blank">Leaflet.js</a> to produce a hover tooltip map of census data. Looking back, while it's quite useful to learn CartoDB's API, it's now possible to achieve those same visual cues and interactivity without spending time wading through complex, undocumented JavaScript. You can now do everything I did <a href="http://carlvlewis.net/visualizing-latest-census-estimates-using-cartodb-and-leaflet/" target="_blank">here</a> using little more than CartoDB's frontend CMS.<!--more--></p>
<p>So, when I ran across <a href="https://www.dropbox.com/s/arkyewfj6z5psa6/enigma-enigma-prisons-all-facilities-7215ee9c7d9dc229d2921a40e899ec5f%20%281%2906.03.01%20AM.csv" target="_blank">this database </a>of all prisons and correctional facilities in the nation on <a href="http://enigma.io" target="_blank">Enigma.io</a>, I used the dataset to put together this visualization displaying all 137 <em>private </em>prisons and correctional facilities – all of which are  owned entirely by just five different private enterprises – and then added a simple chloropleth layer displaying incarceration rates by state underneath.</p>
<p>Can't see what's going on too well at the resolution below? Here's a standalone package:<br />
<h2><a href="http://carlvlewis.net/private-prisons" target="_blank">Click here to view the project live</a></h2><br />
<em>(yes, it's responsive!)</em></p>
<p><iframe src="//carlvlewis.cartodb.com/viz/d785d444-0cb0-11e4-b8bc-0e230854a1cb/embed_map?title=false&amp;description=false&amp;search=false&amp;shareable=true&amp;cartodb_logo=true&amp;layer_selector=true&amp;legends=true&amp;scrollwheel=true&amp;fullscreen=true&amp;sublayer_options=1%7C1&amp;sql=&amp;zoom=3&amp;center_lat=37.92686760148135&amp;center_lon=-85.869140625" width="100%" height="520" frameborder="0" allowfullscreen="allowfullscreen"></iframe></p>
<p><em><br />
</em><br />
This visualization is a case in which deeper statistical analysis and consideration of multiple variables would've bolstered the possible argument that private prisons lead to higher incarceration rates, if such a claim is true, that is (i.e., determining a statistically significant correlation between number of inmates in each state with the number of private prisons or correctional facilities located there). <!--more--></p>
<p>Such a <a href="http://journal.radicalcriminology.org/index.php/rc/article/view/44/html" target="_blank">data-driven study of the private prison industry </a>conducted by U.C. Berkely Ph.D. student Chris Petrella concluded that blacks are disproportionately incarcerated in private prisons as opposed to public jails.</p>
<p>A similar methodology could be applied to private prison populations in general, as many private prisons are required by state or local law to have a minimum quota of around 90 to 95 percent occupancy to keep their government contracts. It would be interesting, too, to visualize that data in a manner more easily accesible to the average user than an academic research journal.</p>
<h2><strong>Tools I would've used before new CartoDB:</strong></h2>
<ul>
<li>Batch geocode by <a href="http://www.findlatitudeandlongitude.com/batch-geocode/#.U8ZZY41dUd9" target="_blank">findlatitudelongitude</a> to geocode the address data into lat/long coordinates.</li>
<li><a href="http://colorbrewer2.org" target="_blank">ColorBrewer2.org </a>to find equidistant color scales.</li>
<li>Locating .shp, .kml or .geojson polygons for the U.S. states.</li>
<li>Excel to merge tables and bind geographic with numeric data.</li>
<li>A custom JavaScript powered control panel – like <a href="http://multimedia.savannahnow.com/media/restaurants/maps/savannahchatham.html#.U8Zbg41dUd8" target="_blank">the one I used here</a> – to categorize the locations by owner.</li>
<li>Leaflet.js</li>
<li>MapBox</li>
<li>Who knows?</li>
</ul>
<h3>All of those tasks are automated in the new CartoDB!</h3>
