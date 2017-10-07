---
title: Visualizing 2012 census estimates using CartoDB and Leaflet
date: 2012-08-06 05:53:43 -04:00
---

<p>I've been tinkering around with some new mapping tools lately, and figured I'd put them to good use by displaying the <a href="http://www.census.gov/popest/">2011-2012 population estimates</a> released last week by the U.S. Census Bureau. The inherently geographical nature of the census makes it a data set just begging to be mapped.</p>
<p>Rather than the de facto <a href="https://developers.google.com/maps/documentation/javascript/">Google Maps JavaScript API V3</a>, I decided to go with <a href="http://cartodb.com">CartoDB</a> and <a href="http://leaflet.cloudmade.com">Leaflet</a> to see what I could produce.</p>
<p>As I mentioned in a recent <a href="http://carlvlewis.net/?p=2422">post</a>, CartoDB offers an excellent <a href="http://google.com/fusiontables">Fusions</a>-esque interface, although it allows for far less front-end customization and requires more beneath the hood programming. Nonetheless, CartoDB can make pretty maps right out of the box, which you can then fully customize using the CartoDB API and basic SQL statements. There's one caveat, however: The service only allows you to upload 5 tables for free. That could be a dealbreaker for cash-strapped news organizations and freelance data journalists.</p>
<p>Anyhow, I downloaded a .zip shapefile package of all 159 Georgia counties from the <a href="http://www.census.gov/geo/www/cob/co2000.html">U.S. Census Bureau</a>, then brought the package into CartoDB using the service's default upload interface. Using Excel, I calculated the <a href="http://www.mathgoodies.com/lessons/percent/change.html">percent change</a> from the most recent population estimates to last year's estimates. I then added the resulting values as a column in my CartoDB table, which you can see <a href="https://carlvlewis.cartodb.com/tables/2948">here</a>.</p>
<p>After playing a bit with the <a href="http://developers.cartodb.com/documentation/cartodb-apis.html">API</a>, I was able to format a diverging chloropleth map from my table with the following style parameters, written using <a href="http://0to255.com">0to255</a> to ensure an equidistant color scheme:</p>
<pre name="code" class="css">
#statewidepop {
   line-color:#FFFFFF;
   line-width:1;
   line-opacity:0.52;
   polygon-opacity:1;
}
#statewidepop [percent_change<=5.5] {
   polygon-fill:#558740
}
#statewidepop [percent_change<=4] {
   polygon-fill:#609948
}
#statewidepop [percent_change<=3] {
   polygon-fill:#6AA84F
}
#statewidepop [percent_change<=2.25] {
   polygon-fill:#BECFA8
}
#statewidepop [percent_change<=1.5] {
   polygon-fill:#D0D8BB
}
#statewidepop [percent_change<=0.75] {
   polygon-fill:#B8CCA1
}
#statewidepop [percent_change<=0.3] {
   polygon-fill:#D9DCC4
}
#statewidepop [percent_change<=0] {
   polygon-fill:#D3BAAF
}
#statewidepop [percent_change<=-0.5] {
   polygon-fill:#E7D1C5
}
#statewidepop [percent_change<=-1] {
   polygon-fill:#D8A696
}
#statewidepop [percent_change<=-2] {
   polygon-fill:#C36E59
}
#statewidepop [percent_change<=-3] {
   polygon-fill:#BC5942
}
#statewidepop [percent_change<=-4] {
   polygon-fill:#B34027
}
#tl_2009_13_county[percent_change<=-5] {
   polygon-fill:#AB2B10
}
</pre>
<p>Check out the resulting map:</p>
<p><iframe src="https://carlvlewis.cartodb.com/tables/statewidepop/embed_map" width="500" height="305"></iframe></p>
<p><em></em></p>
<p>The map above shows the percent change in population from July 2010 to July 2011 in all 159 Georgia counties, as estimated by the U.S. Census Bureau. The darker the green, the higher the positive percent change. The darker the red, the higher the negative percent change. Click on a county to see its percent change.<br />
<!--more--></p>
<p>Pretty nice, huh? But what if I want to customize the style of the pop-up windows or perform more advanced functions like creating custom image markers or switching between layers? That's where <a href="http://leaflet.cloudmade.com">Leaflet</a>, an open-source JavaScript library, comes in handy.</p>
<h1>Using the Leaflet library</h1>
<p><iframe src="http://carlvlewis.net/leaflet/home%20copy.html" frameborder="no" scrolling="no" width="530" height="305"></iframe></p>
<p><em>The map above displays the estimated percent change in population of various midstate counties between July 2011 and July 2012. The greener the county, the higher its percent increase. The deeper red the county, the higher its percent decrease. Click on a county to see more precise totals, or select a group from the dropdown in the top right corner for a breakdown of the population changes by race.</em></p>
<p>To get a wider range of flexibility, I called up a segment of the statewide data – the counties within the <a href="http://www.google.com/url?sa=t&amp;rct=j&amp;q=&amp;esrc=s&amp;source=web&amp;cd=3&amp;ved=0CHsQFjAC&amp;url=http%3A%2F%2Fen.wikipedia.org%2Fwiki%2FMacon%2C_Georgia&amp;ei=77LBT8O0B6al2AW6qYlk&amp;usg=AFQjCNFR3fNx8wCTK88zuWVl30ObDsMuwg">Macon/Warner Robins</a> metropolitan area – using the <a href="http://leaflet.cloudmade.com">Leaflet</a> javascript. Leaflet allows you to reference layers from CartoDB or Google Maps from within its API, making integration a breeze. All you have to do is reference a <a href="http://developers.cartodb.com/documentation/cartodb-apis.html#section-7">few lines of code</a> and your CartoDB data will appear as a layer on your Leaflet map automagically. But even on its own, Leaflet is pretty robust, especially for being so lightweight.</p>
<p>In the map above, I took the county shapefile package from earlier, converted it to GeoJSON using <a href="http://www.kyngchaos.com/software/qgis">QGis</a>, then, following these <a href="http://leaflet.cloudmade.com/reference.html#geojson">parameters</a>, called up the GeoJSON data for the selected counties using the Leaflet script. For the underlying map tiles, I created a custom style using Cloudmade, then referenced it using my API key and the following line of script:</p>
<pre class="javascript">var cloudmade = new L.TileLayer('http://{s}.tile.cloudmade.com/41fc7e18cef34d6fb34756efd8240787/63501/256/{z}/{x}/{y}.png',</pre>
<p>Because I also wanted to show a breakdown of the data by race for the same geography, I added in a <a href="http://leaflet.cloudmade.com/reference.html#control-layers">custom control</a> menu that allows the user to switch between layers for easy comparison. In addition, I styled the popup to my liking, with green and red values to connote increase and decrease.</p>
<p>From there, I was able to add in the additional data sets of population change by race. For each demographic group, I created a corresponding <a href="http://leaflet.cloudmade.com/reference.html#layergroup">layer group</a>. Each layer group contained the data as well as the appropriate styles and colors. See the source code below:</p>
<pre name="code" class="javascript">var totalLayer = new L.LayerGroup();

citiesLayer.addLayer(geojsononeLayer)
           .addLayer(geojsontwoLayer)
           .addLayer(geojsonthreeLayer)
           .addLayer(geojsonfourLayer)
           .addLayer(geojsonfiveLayer)
           .addLayer(geojsonsevenLayer)
           .addLayer(geojsoneightLayer);

var whiteLayer = new L.LayerGroup();

whiteLayer.addLayer(geojsononewhiteLayer)
           .addLayer(geojsontwowhiteLayer)
           .addLayer(geojsonthreewhiteLayer)
           .addLayer(geojsonfourwhiteLayer)
           .addLayer(geojsonfivewhiteLayer)
           .addLayer(geojsonsevenwhiteLayer)
           .addLayer(geojsoneightwhiteLayer);
var blackLayer = new L.LayerGroup();

blackLayer.addLayer(geojsononeblackLayer)
           .addLayer(geojsontwoblackLayer)
           .addLayer(geojsonthreeblackLayer)
           .addLayer(geojsonfourblackLayer)
           .addLayer(geojsonfiveblackLayer)
           .addLayer(geojsonsevenblackLayer)
           .addLayer(geojsoneightblackLayer);

var asianLayer = new L.LayerGroup();

asianLayer.addLayer(geojsononeasianLayer)
           .addLayer(geojsontwoasianLayer)
           .addLayer(geojsonthreeasianLayer)
           .addLayer(geojsonfourasianLayer)
           .addLayer(geojsonfiveasianLayer)
           .addLayer(geojsonsevenasianLayer)
           .addLayer(geojsoneightasianLayer);

var otherLayer = new L.LayerGroup();

otherLayer.addLayer(geojsononeotherLayer)
           .addLayer(geojsontwootherLayer)
           .addLayer(geojsonthreeotherLayer)
           .addLayer(geojsonfourotherLayer)
           .addLayer(geojsonfiveotherLayer)
           .addLayer(geojsonsevenotherLayer)
           .addLayer(geojsoneightotherLayer);

map.addLayer(citiesLayer);

var overlayMaps = {
    "Total Population": citiesLayer,
    "White": whiteLayer,
    "Black": blackLayer,
    "Asian": asianLayer,
    "Other/Mixed": otherLayer
};

var layersControl = new L.Control.Layers(overlayMaps);

map.addControl(layersControl);</pre>
<p>All of this was accomplished using the built-in components of the Leaflet JavaScript API and only an hour or so of data analysis in Excel. Next goal? Adding <a href="http://palewi.re/posts/2012/03/26/leaflet-recipe-hover-events-features-and-polygons/">mouseover capabilities</a>.</p>
<p>&nbsp;</p>
