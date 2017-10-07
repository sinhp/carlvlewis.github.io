---
title: 'Tutorial: Building a hover-enabled map using Tilemill'
date: '2013-08-01T19:35:48.000+00:00'
---

<p><em>This is the first of two tutorials on adding hover interactions to interactive maps<em> using free, open-source tools.</em></em></p>
<p>Today I wanted to break down the step-by-step process of how I created <a href="http://carlvlewis.net/?portfolio=interactive-statewide-population-change-between-2010-and-2011">this interactive, chloropleth map</a> of Georgia population change from 2010 to 2011. <!--more-->While today's tutorial doesn't cover how to add hover states or moving tooltips to your maps, such as those you see in <a href="http://carlvlewis.net/cartodbleaflet/examples/statewidepop.html">this iteration</a> of the same map made using <a href="https://github.com/Vizzuality/cartodb-leaflet">CartoDB+Leaflet</a>, I'll cover how to add those more advanced features in a later post (they require using a few more tools and doing a bit more programming). For now, though, we're going to stick to the basics of simple hover interactions. Here's what you'll need to follow along:</p>
<ul>
<li>Mac OS X</li>
<li><a href="http://www.qgis.org/">QGis</a> (free)</li>
<li><a href="http://tilemill.com">Tilemill</a> (free)</li>
<li>A free <a href="http://mapbox.com">MapBox</a> account where you will host your MBTiles</li>
<li><a href="http://docs.google.com">Google spreadsheets</a> (or Microsoft Excel, if you're more comfortable with that)</li>
</ul>
<h2><strong>1. Gathering and preparing the data</strong></h2>
<p>The first thing you'll need to do is locate your data and parse it down to a format in which it can be easily visualized. For this example, we're using population estimates from the U.S. Census Bureau, which, luckily for us, has already done most of the heavy lifting analysis-wise. Download the data for Georgia or whatever other state you wish to visualize as a comma separated value (.csv) file from <a href="http://www.census.gov/popest/data/counties/totals/2011/CO-EST2011-03.html">this page</a>. Now you'll want to turn that .csv file into a Google spreadsheet. You can do this from the Google Drive dashboard by selecting Create&gt;Spreadsheet, then choosing File&gt;Import once your new spreadsheet opens. Locate and upload the .csv file. Select "Replace current spreadsheet" and set "Comma" as the separator character. Voila. Your data should then appear for each county. Here's what the correct options for that will look like:</p>
<p><a href="http://carlvlewis.net/wp-content/uploads/2012/08/Screen-Shot-2012-08-07-at-6.55.50-PM.png"><img class="aligncenter size-full wp-image-3098" title="Screen Shot 2012-08-07 at 6.55.50 PM" src="{{ site.baseurl }}/assets/Screen-Shot-2012-08-07-at-6.55.50-PM.png" alt="" width="600" height="450" /></a></p>
<p>Given that each county has a different base population, the only standar way to compare en masse how many residents each county gained or lost is by calculating the percent change (see <a href="http://www.mathgoodies.com/lessons/percent/change.html">here</a> to figure out how to calculate that in Excel or Google Spreadsheets). In this example, however, the Census Bureau has already calculated out the percent change for us, making our job that much easier. Delete all the unnecessary columns from the spreadsheet, leaving only the county name in the first column, the 2010 and 2011 population totals in the second and third columns, and the "Percent" value in the fourth column (the "Percent" value refers to the percent change between the two years). Also make sure to delete any extra rows at the top or bottom of the spreadsheet, so that the first row contains the column titles, with the first county beginning on the second row and ending on the last row. Highlight the column containing the county names and select “Data&gt;Sort sheet by column, A-Z." This will put the entries in alphabetical order.</p>
<p>If you happen to get a weird period ('.') preceding each county name as I did, you can get rid of it pretty easily by performing the following steps:</p>
<ol>
<li>Insert a new column to the right and, assuming your first county name begins in cell A2, enter the following formula into B2: <em>=MID(A2,2,LEN(A2))</em>. This function deletes the first character of the county name –– the unwanted period –– automatically.</li>
<li>Copy and paste the new B2 cell into the rest of the rows to apply the same formula throughout the entire spreadsheet.</li>
<li>Copy the new period-free column you just created and paste it as unformatted text into another new column to the right by selecting "Paste special&gt;Paste values only." This formats the data in new column as values only so that it they won't include a formula that depends on the incorrectly formatted column to work.</li>
<li>Delete the first two columns so that the first column now becomes the county names only, free of the preceding periods.</li>
</ol>
<p>We're almost finished getting the data ready. All we have left to do is add in a column containing the official county codes for each county so that we'll have a common attribute with which we can merge the spreadsheet with the geometric data later. Download <a href="http://www.census.gov/popest/data/counties/totals/2011/files/CO-EST2011-Alldata.csv">this .csv</a> from the Census Bureau, which contains the county codes for all 50 states. Open it as a Google spreadsheet and delete all the rows except for the ones for the state you're visualizing. With only the rows for the state at hand remaining, highlight the "ctyname" column and select "Data&gt;Sort values from A-Z." This should reorder the spreadsheet to be exactly the same alphabetic order as the spreadsheet with the population totals. Copy the "county" column containing the numeric county codes, which should now also be arranged in numeric order, and paste it into a new column in the spreadsheet with the population totals, which should also be sorted alphabetically. This should now give you the correct county codes for each county in a new column. Title that column simply "COUNTY" (all-caps). For an idea of what things should look like at this point, check out my Google spreadsheet <a href="https://docs.google.com/spreadsheet/ccc?key=0Au4D8Alccn4xdGpUTGQwZUNUMnR4MUtRY2FNS0pBaEE">here</a>, or see the following screenshot:</p>
<p><a href="http://carlvlewis.net/wp-content/uploads/2012/08/Screen-Shot-2012-08-06-at-1.41.32-AM.png"><img class="aligncenter size-full wp-image-3091" title="Screen Shot 2012-08-06 at 1.41.32 AM" src="{{ site.baseurl }}/assets/Screen-Shot-2012-08-06-at-1.41.32-AM.png" alt="" width="589" height="495" /></a></p>
<p>One last thing to keep in mind: If your county codes include values less than three digits, which they probably will, make sure any values less than three-digits long have 0s preceding them to force them to be three digits long (i.e. '001'). That way, it will match up with the three digit values in the geometric data later on in the process.</p>
<p>Now that we have our population data ready, let's download the corresponding geometric county polygons from the Census Bureau <a href="http://www.census.gov/geo/www/cob/co2000.html">here</a>. For this tutorial we'll be using the shapefiles (.shp) format, so make sure to select the shp.zip option on the download page. I obviously used Georgia in this example; feel free to choose whatever other state you desire, so long as you follow the exact same instructions. Unzip the archive to your computer. You should see three files in the new unzipped folder: a .dbf, a .shp and a .shx. All we'll be need for this tutorial is the .shp file.</p>
<p>At this point, you should have <strong>two different files</strong>: a Google spreadsheet of data formatted something like <a href="https://docs.google.com/spreadsheet/ccc?key=0Au4D8Alccn4xdGpUTGQwZUNUMnR4MUtRY2FNS0pBaEE">this</a>, and a shapefile of corresponding county polygons. The next step will be to take the population data and bind it to the shapefile so that the two match up.</p>
<p>2. Binding the data to the shapefile using QGis</p>
<p>Fire up QGis. Select the "Add Vector Layer" option from the top of the window and locate the .shp file you downloaded earlier. Open it in QGis and you should see a nice outline of your state that looks something like this:</p>
<p><a href="http://carlvlewis.net/wp-content/uploads/2012/08/Screen-Shot-2012-08-05-at-10.20.45-PM.png"><img class="wp-image-3058 aligncenter" title="Screen Shot 2012-08-05 at 10.20.45 PM" src="{{ site.baseurl }}/assets/Screen-Shot-2012-08-05-at-10.20.45-PM.png" alt="" width="526" height="350" /></a>Now go back to your newly created Google spreadsheet and export the data as a .csv by selecting "File&gt;Download data as." After downloading the .csv, rename it to something simple like "georgia.csv." The next thing we need to do is import the .csv into QGis. But before we can do that, we need to create a new '.csvt' file by the same name and in the same directory as the .csv that will tell QGis what type of data each column is (string, number, real, etc.). For this example, your '.csvt' file will look something <a href="https://docs.google.com/open?id=0B-4D8Alccn4xbnVVU2JyX3l1S0E">like this</a>, with a data type defining each column of the .csv:</p>
<p><a href="http://carlvlewis.net/wp-content/uploads/2012/08/Screen-Shot-2012-08-05-at-10.39.32-PM.png"><img class="wp-image-3061 aligncenter" title="Screen Shot 2012-08-05 at 10.39.32 PM" src="{{ site.baseurl }}/assets/Screen-Shot-2012-08-05-at-10.39.32-PM.png" alt="" width="611" height="454" /></a></p>
<p>If you're having trouble with this part, download my .csvt <a href="https://docs.google.com/open?id=0B-4D8Alccn4xbnVVU2JyX3l1S0E">here</a>. Should you need to adjust the data types, just force open it in TextEdit and change them. The main two things to make sure is that the COUNTY column is defined as a string and the PERCENT column is defined as a value.</p>
<p>Once your .csvt file has been placed in the same directory as your .csv, go back to QGis and add a new vector layer just as you did before with the shapefile. This time, locate the .csv file and open it. QGis should then automatically detect the .csvt file in the background and assign the appropriate data types to each column in the .csv. To make sure this worked correctly, you can control-click the new .csv vector layer and select "Properties&gt;Fields" to check that each field has the appropriate data type.</p>
<p>Now you can get down to the business of binding the data from the .csv to the shapefile. Select the shapefile layer and go to "Properties&gt;Joins." Add a new vector join, setting the .csv as the join layer and both the join field and the target field as COUNTY, like this:</p>
<p><a href="http://carlvlewis.net/wp-content/uploads/2012/08/Screen-Shot-2012-08-05-at-10.57.51-PM.png"><img class="wp-image-3062 aligncenter" title="Screen Shot 2012-08-05 at 10.57.51 PM" src="{{ site.baseurl }}/assets/Screen-Shot-2012-08-05-at-10.57.51-PM.png" alt="" width="532" height="355" /></a></p>
<p>Applying the join should merge your spreadsheet and shapefile, binding the population data to the polygons using the shared "COUNTY"attribute that contains the matching county codes. To check and make sure everything worked correctly, control-click the shapefile vector layer in QGis and select "Open attribute table." You should see the population data attached as columns to the end of the attribute table.</p>
<p>Once you've ensured that the data has been attached to the polygon vector layer, you can now export the new shapefile by control-clicking the shapefile vector layer, selecting "Save as," and saving the layer in the ESRI Shapefile format. Now you're ready to compress the shapefile package into a .zip and import it into Tilemill. where you can style it, add interactivity and more.</p>
<p>3. Styling the map in Tilemill</p>
<div>Open <a href="http://tilemill.com">Tilemill</a>. Create a new project. Under the layers panel, add a new layer and locate the .zip of the ESRI shapefile you just exported from QGis. Upload the package. Upon doing so, you should immediately see the polygons for your state. Now you'll need to style the map in the style.mss panel using the Carto language. Because the numbers at hand for this map represent either a positive or a negative percent change, it makes sense to create a chloropleth map where red represents negative values and green represents positive values. You might try using <a href="http://colorbrewer2.org">ColorBrewer</a> or <a href="http://0to255.com">0to255</a> to find the right color ramp for your data. For this example, I used the following style parameters:</div>
<div></div>
<div></div>
<p>&nbsp;</p>
<pre class="css">#georgia {
  line-color:#565A5D;
  line-width:0.5;
  polygon-opacity:1;
  polygon-fill:#ae8;
}

#georgia[PERCENT&lt;=5.5] {
   polygon-fill:#558740
}
#georgia[PERCENT&lt;=4] {
   polygon-fill:#609948
}
#georgia[PERCENT&lt;=3] {
   polygon-fill:#6BAA50
}
#georgia[PERCENT&lt;=2] {
   polygon-fill:#7DB863
}
#georgia[PERCENT&lt;=1.5] {
   polygon-fill:#9FBA80
}
#georgia[PERCENT&lt;=0.75] {
   polygon-fill:#B8CCA1
}
#georgia[PERCENT&lt;=0.3] {
   polygon-fill:#D9DCC4
}
#georgia[PERCENT&lt;=0] {
   polygon-fill:#F4E5E0
}
#georgia[PERCENT&lt;=-0.5] {
   polygon-fill:#E6C5BB
}
#georgia[PERCENT&lt;=-1] {
   polygon-fill:#D8A696
}
#georgia[PERCENT&lt;=-1.75] {
   polygon-fill:#CC8472
}
#georgia[PERCENT&lt;=-2.5] {
   polygon-fill:#BC5942
}
#georgia[PERCENT&lt;=-3.9] {
   polygon-fill:#B34027
}
#georgia[PERCENT&lt;=-5] {
   polygon-fill:#A1280F
}
</pre>
<p>As you can see, the first set of paramaters defines the style attributes of the entire polygon, including the line width and the opacity of the #georgia layer. The following sets of paramaters all set the fill color of individual 'buckets,' sorted by the value of the PERCENT field.</p>
<p>Now, add layers for the surrounding geographical and cultural features, including roads, populated places, rivers, and surrounding states. When selecting the file source for your new layer, click "Browse&gt;MapBox" to add predefined features from MapBox. Use the same method to style those layers as you did with the primary shapefile layer, pressing Command + S to save the map and refresh the view. Here's what my styled map ended up looking like:</p>
<p><a href="http://carlvlewis.net/wp-content/uploads/2012/08/Screen-Shot-2012-08-05-at-11.31.40-PM.png"><img class="aligncenter size-full wp-image-3066" title="Screen Shot 2012-08-05 at 11.31.40 PM" src="{{ site.baseurl }}/assets/Screen-Shot-2012-08-05-at-11.31.40-PM.png" alt="" width="528" height="312" /></a></p>
<p>4. Adding the mouseover tooltips<br />
In Tilemill, navigate to the Templates panel. Select "Teaser." This is the field that defines what appears in the tooltip that appears when the user moves the mouse over each polygon. Basically, all you have to do is format the field to correspond to the values in your data.</p>
<p>Here's the formatting I used:</p>
<pre class="html"><b>{{{CountyName}}}</b></pre>
<div>Percent Population Change, 2010-2011: {{{PERCENT}}}%</div>
<hr />
<div>2010 Population: {{{2010}}}</div>
<div>2011 Population: {{{2011}}}</div>
<p>That produced the following tooltip:<br />
<a href="http://carlvlewis.net/wp-content/uploads/2012/08/Screen-Shot-2012-08-06-at-12.32.36-AM.png"><img class="aligncenter size-full wp-image-3071" title="Screen Shot 2012-08-06 at 12.32.36 AM" src="{{ site.baseurl }}/assets/Screen-Shot-2012-08-06-at-12.32.36-AM.png" alt="" width="318" height="127" /></a></p>
<p>As you can see, I used simple HTML markup, including the bold and horizontal line tags to add style elements. Each instance of {{{TEXTHERE}}} corresponds to a column name from the shapefile, and calls it up in the tooltip. So, to call up the percent change, I typed {{{PERCENT}}}, which refers to the PERCENT column from our data. Upon saving this, you'll see that the tooltip appears however you format it. The possibilities here really abound.</p>
<p>5. Adding a legend</p>
<p>You can add a custom legend to map in much the same way. While you're still in the Templates panel, select "Legend." This allows you to use basic HTML/CSS both to define the structure and style of your legend, as well as the position of both the legend and the tooltip. Here's a look at how I formatted the "Legend" field to produce my map:</p>
<div class="my-legend">
<div class="legend-title">Estimated Population Change, 2010-2011</div>
<div class="legend-scale">
<ul class="legend-labels">
<li>&lt;-5%</li>
<li>-5--4%</li>
<li>-4--3%</li>
<li>-3--2%</li>
<li>-2--1%</li>
<li>-1--0.5%</li>
<li>-0.5-0%</li>
<li>0-0.3%</li>
<li>0.3-0.8%</li>
<li>0.8-1.5%</li>
<li>1.5-2%</li>
<li>2-3%</li>
<li>3-4.5%</li>
<li>&gt;4.5%</li>
</ul>
</div>
<div class="legend-source">Source: <a href="http://www.census.gov/popest/">U.S. Census Bureau, 5/17/2012.</a></div>
</div>
<pre class="html"></pre>
<p>If you know rudimentary HTML/CSS, it's pretty easy to tell what's going on above. The background attribute corresponds to the color ramp I defined earlier in the style.mss field of Tilemill, and the HTML sets up the structure of the legend. After the HTML, I set some basic CSS styles, including the positioning of the legend and tooltips using the "margin" attribute and the font-size of the legend text. Save all that, and here's a glimpse of what you get:</p>
<p><a href="http://carlvlewis.net/wp-content/uploads/2012/08/Screen-Shot-2012-08-06-at-12.49.58-AM.png"><img class="aligncenter size-full wp-image-3078" title="Screen Shot 2012-08-06 at 12.49.58 AM" src="{{ site.baseurl }}/assets/Screen-Shot-2012-08-06-at-12.49.58-AM.png" alt="" width="599" height="699" /></a></p>
<p>6. Exporting your map as MBTiles</p>
<p>After you have the map styled to your liking in Tilemill, it's a fairly straightforward process to get the map on the Web. First, in the top right of Tilemill, select "Export" and choose "MBTiles." This is the format for the tiles that you'll need to host the map on MapBox. Once the export panel appears, set the starting center point (for my map, it was -83.562,32.9349,6), as well as the bounds. Keep in mind that the smaller the bounds, the less tiles your map needs to render and, thus, the faster it will load. Only include the bounds you need in the map so as to minimize load time. Give the map a name, a brief description, and be sure to attribute the data source. Once you're finished, click "Export" and save your map in the directory of your choice. The resulting .MBTiles file will now go directly into MapBox, where your map will be hosted.</p>
<p>7. Uploading your map to MapBox</p>
<p>The final step in making your map viewable by the world involves uploading the .MBTiles file you just exported to MapBox. Create a MapBox account and select the option to upload a new map. Locate your .MBTiles file and upload it to MapBox. Once it's finshed you'll see a preview of the map that looks like this:</p>
<p><a href="http://carlvlewis.net/wp-content/uploads/2012/08/Screen-Shot-2012-08-06-at-12.59.06-AM.png"><img class="aligncenter size-full wp-image-3080" title="Screen Shot 2012-08-06 at 12.59.06 AM" src="{{ site.baseurl }}/assets/Screen-Shot-2012-08-06-at-12.59.06-AM.png" alt="" width="515" height="393" /></a></p>
<p>Enable the "Map Details" view. Set the map to "Public," and make sure the Zoom, Legend, Scroll zoom and Tooltip options are selected. Deselect the "Share links" option. Then click embed, and copy the embed code to use the map wherever you please.</p>
<p>Just to give you a live view of how the finished map looks and behaves:</p>
<p><iframe src="http://a.tiles.mapbox.com/v3/carlvlewis.315301.html#7/33.514/-82.573" width="600" height="700" frameborder="0"></iframe></p>
<p>And there you go. Using Tilemill to create hover-enabled maps is easy and flexible, but it has its limitations. Because the program renders data using the UTF grid-system, it doesn't send vector data to your browser, meaning you can't use JavaScript or CSS3 to spice things up. The next step? Adding hover states and moving tooltips to create maps like <a href="http://carlvlewis.net/cartodbleaflet/examples/countiesbytsplost.html">this</a> for polygons and <a href="http://carlvlewis.net/cartodbleaflet/examples/nycIEP.html">this</a> for markers. Until then, feel free to ask any questions or point out any problems you may run into. Happy mapping!</p>
