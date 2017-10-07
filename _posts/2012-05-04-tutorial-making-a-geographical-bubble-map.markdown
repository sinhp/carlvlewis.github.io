---
title: Overlaying a bubble chart onto a Google map
date: '2012-05-04T15:59:08.000+00:00'
categories:
- Blog
- Data Visualization
- Data Visualization Thoughts
tags:
- bubble charts
- data visualization
- google maps javascript api v3
- mapping
---

<p>Others may <a href="http://junkcharts.typepad.com/junk_charts/2009/01/popping-the-bubble-so-to-speak.html">hate</a>, but I'm a big fan of using bubbles to display data. When implemented correctly (i.e. scaled in terms of area instead of diameter), bubbles can be an aesthetically appealing and concise way to represent the value of data points in an inherently visual format. Bubbles are even more useful when they include interactivity, with events like mouseover and zoom allowing users to drill down and compare similar-sized bubbles more easily than they can in static graphics. So, when I was recently working on a <a href="http://carlvlewis.net/autismdataviz/WebAppHome.html">class project</a> on autism diagnoses in New York City, I decided to use bubbles to represent the percentage of students with individualized education plans at all 1250 or so K-8 New York City schools.<!--more--></p>
<p>Almost by default, I turned to Google Maps JavaScript API V3, mainly because I'm quasi-familiar with its basic functions and event handlers (as I point out later in  this post, I didn't realize that a nifty new service called <a href="http://cartodb.com">CartoDB</a> would have automated most of what I was trying to do, albeit without nearly the level of customization). Nonetheless, based on a <a href="http://karlagius.com/2011/03/26/visualizing-data-with-google-maps/">tutorial</a> from Karl Agius, as well as some infoWindow help from my data viz professor, <a href="http://susanemcgregor.com">Susan McGregor</a>, I created the following interactive bubble map of NYC schools based upon the number of special needs, or IEP, students at each school. The larger the bubble, the greater percentage of special needs students a school has. Click <a href="http://carlvlewis.net/mapTest/">here</a> to see the map full-screen, or <a href="http://carlvlewis.net/bubbleMap.zip">here</a> to download a .zip of my source files for your own customization purposes.</p>
<p><iframe src="http://carlvlewis.net/mapTest/" frameborder="0" width="550" height="330"></iframe></p>
<p><em>Each bubble on this map represents one of New York City's approximately 1,250  K-8 public schools, including charters. The larger the bubble, the higher the percentage of students with individualized education plans (IEP). Click on a bubble to find out more about the school, or click anywhere within a district boundary to see an overall average IEP rate for the district. Zoom and pan to see other parts of the city.</em></p>
<p>You'll notice the opacity for the bubbles is set to 40 percent. This allows us to get a quick visual of the locations with the highest density of special needs students, given that those areas on the map will naturally be darker because they have multiple semi-opaque layers that overlap. Setting a low opacity also prevents overlapping bubbles from covering up one another. You'll also notice that the map includes polygons for each school district, which you can click on to get an average IEP rate for the entire district. I decided against setting gradated fill colors for the school district shapes so as to avoid implying causation, as well as to lessen the visual clutter.*</p>
<h1>Preparing the data</h1>
<p>To create the map, I first had to download the underlying data from the New York City Department of Education <a href="http://schools.nyc.gov/AboutUs/data/default.htm">database</a> as a .csv, then import it into Excel to clean it up and leave only the relevant information. Although the dataset I had only included street addresses split into multiple columns, I was able to use the <a href="http://office.microsoft.com/en-us/excel-help/concatenate-HP005209020.aspx">concatenate</a> function in Excel to merge the street, city, state and zip columns to get a full street address. From there, I used my <a href="http://www.findlatitudeandlongitude.com/batch-geocode/#.T6V0Op9Yvk0">favorite batch geocoding service</a> to convert the addresses into geographic coordinates that the Google Maps API can read. Check out my resulting .csv file <a href="http://carlvlewis.net/nycpubschools.csv">here</a> for an example. Then I imported the .csv into a Google Spreadsheet, and pasted the resulting spreadsheet's URL into dataSourceURL field in the JavaScript of my main index.html file. Here's how that looked in my code:</p>
<pre name="code" class="javascript">
var dataSourceUrl = "https://docs.google.com/spreadsheet/ccc?key=0Au4D8Alccn4xdHNJdXJmeTdYcEtpRXE1QXRucWtEN3c";
var onlyInfoWindow;

google.load("visualization", "1");
google.load("maps", "3", {other_params:"sensor=false"});

google.setOnLoadCallback(getData);
</pre>
<h1>Drawing the bubbles</h1>
<p>Using the 'Circle' marker style from the <a href="https://developers.google.com/maps/documentation/javascript/">Google Maps JavaScript V3 API</a>, I set each row in the spreadsheet to draw a bubble whose size corresponded to its value. It works by calling up the 'size' column in my spreadsheet, which contains the IEP data I want to display, and then drawing an overlay of bubbles based upon those values. Credit again to Karl Agius for helping me figure out this part of the script.</p>
<pre name="code" class="javascript">
// This first array is where you can change the size and styling of the bubbles.
function Bubble(data, options) {
    this.data = data;
    if (options)
        this.options = options
    else
        this.options = {
            radiusForPercentage:1000,
            text:{
                visible:false,
                minimumZoom:13,
                maximumZoom:15
            },
            bubble:{
                fill:{color:"#709ED9", opacity:0.6},
                stroke:{color:"#709ED9", weight:1, opacity:0.3}
            }
        };
    this.totalSize = this.getTotalSize(this.data);
}

Bubble.prototype = new google.maps.OverlayView;

Bubble.prototype.onAdd = function() {
    for (var row = 0; row < this.data.getNumberOfRows(); row++)
        this.drawBubble(this.data, this.options, row);
}

Bubble.prototype.draw = function() {
    if (this.options.text.visible)
        for (var row = 0; row < this.data.getNumberOfRows(); row++)
            this.drawText(this.data, this.options, row);
}

Bubble.prototype.getTotalSize = function(data) {
    var totalSize = 0;

    for (var row = 0; row < data.getNumberOfRows(); row++)
        totalSize += data.getValue(row, 1);

    return totalSize;
}

Bubble.prototype.drawBubble = function(data, options, row) {

    var sizeOfLocation = data.getValue(row, 1);
    var percentageOfTotal = (sizeOfLocation / this.totalSize) * 100;

    var radiusForLocation = options.radiusForPercentage * percentageOfTotal;

    var marker = new google.maps.Circle({
        center: new google.maps.LatLng(data.getValue(row, 2), data.getValue(row, 3)),
        fillColor:options.bubble.fill.color,
        fillOpacity:options.bubble.fill.opacity,
        strokeColour:options.bubble.stroke.color,
        strokeWeight:options.bubble.stroke.weight,
        strokeOpacity:options.bubble.stroke.opacity,
        radius:radiusForLocation
 });
    // This last command actually places the Circle markers we just made on the map.
    marker.setMap(this.getMap());
</pre>
<p>You can change the default size of the bubbles with the radiusForPercentage value, and set the colors using the fill and stroke fields. You can also set a minimum and maximum zoom so that users can only see the part of the map you want them to see.</p>
<h1>Adding interactivity</h1>
<p>Next, to make the bubbles clickable, I added an on-click event listener that set each bubble marker to fetch the corresponding data from my Google spreadsheet and add it to an infoWindow. Here's what that looked like:</p>
<pre name="code" class="javascript">var contentString = '
<div id="content">School Name' + data.getValue(row, 0) + 'Percent IEP students:'+sizeOfLocation+'Percent free-lunch:' + data.getValue(row, 4) + '</div>
';

var infowindow = new google.maps.InfoWindow({
content: contentString,
position: new google.maps.LatLng(data.getValue(row, 2), data.getValue(row, 3))

});

google.maps.event.addListener(marker, 'click', function() {
if(onlyInfoWindow != null){
onlyInfoWindow.close();
}

infowindow.open(this.getMap());
onlyInfoWindow = infowindow;

});</pre>
<p>As you can see, I referenced each value in my spreadsheet by adding in its row number. Later, I went back in and added another row of data that displayed free-lunch statistics as well, which is why you see a reference to row 4.</p>
<h1>Overlaying the school district polygons</h1>
<p>To place the school polygons onto the map, I added a new overlay that pointed to a Google Fusions table containing the KML geographic boundaries. This is what that looked like:</p>
<pre name="code" class="javascript">
var schoolDistricts = new google.maps.LatLng(41.850033, -87.6500523);

var layer = new google.maps.FusionTablesLayer({
  query: {
    select: 'geometry',
    from: '3621394'
  },
  styles: [{
  polygonOptions: {
    fillColor: "#FAFBFF",
    fillOpacity: 0
    }
  }
  ]}
  );

layer.setMap(map);
  }
</pre>
<p>The 'from' field should correspond to your Fusions Table's six-digit ID, and the 'select' field should reference the column in your table that contains the KML geographic data.</p>
<h1>A simpler new tool to do roughly the same thing</h1>
<p>Ultimately, however, it may have been easier to use the WYSIWYG editor from <a href="http://cartodb.com">CartoDB</a>, a premium mapping service with a front-end interface similar to Google Fusions Tables except that it allows you to draw bubbles without having to dig into the API. See what happened when I uploaded my spreadsheet to CartoDB and set similar style attributes:</p>
<p><iframe src="https://carlvlewis.cartodb.com/tables/nycpubschools/embed_map" width="550" height="330"></iframe></p>
<p>Which map is better? Personally I think the hand-coded Google Maps API version is better in terms of aesthetics and usability. But was it worth the extra effort? You tell me.</p>
<p><i>*<a href="http://www.propublica.org/site/author/jennifer_lafleur">Jennifer LaFleur</a>, ProPublica's director of computer-assisted reporting, later pointed out to me that I could easily add a gradated fill for each of the school district polygons based upon poverty levels, provided that I pick the right color-scheme to complement the blue bubbles. I guess I was just a bit leery from a design standpoint about what one color on top of another would look like.</i></p>
