---
title: 'Tutorial: Creating an interactive, drill-down treemap of spending data with
  d3.js'
date: 2017-05-21 10:44:00 Z
---

<iframe src="https://player.vimeo.com/video/191347190" width="640" height="400" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
<em>View the screencast above and see below for downloadable source code and a working example.</em>

The <a href="https://en.wikipedia.org/wiki/Treemapping">treemap</a> is one of my favorite types of data visualizations – largely because of the natural ease with which one can understand and process what the data says visually without requiring a heavy cognitive load or any expert knowledge of the subject matter at hand. It's pretty straightforward to understand that the size of each rectangle on a treemap corresponds to its proportion of the total rectangle as a whole. <a href="https://upload.wikimedia.org/wikipedia/commons/c/c1/Lebanon_Export_Treemap.jpg">Static treemaps</a> certainly have value, but what I like the most about treemaps displayed digitally is the added ability to drill down and see different levels of hierarchical data and to interact with and explore the data as a user.

In the accompanying screencast, we're going to take things a step further and create a dynamic treemap that will allow us to visualize multiple layers or subsets of data in each main bucket. We'll use an open-source, responsive <a href="https://d3js.org/">D3</a> charting library called <a href="http://d3plus.org">D3Plus</a> as a base template to create an interactive treemap of newly released budget data for the state of Georgia for fiscal year 2017. You can apply the same basic approach with any hierarchical dataset that you are working with.
<h3>What you'll be creating:</h3>
<iframe src="http://carlvlewis.net/georgia_budget_2017.html" width="640" height="480" frameborder="no" scrolling="no"></iframe>

*The interactive treemap above displays the breakdown in projected total state and local government costs for Georgia for fiscal year 2017, according to <a href="http://www.usgovernmentspending.com/Georgia_state_spending_2017">projections</a> based upon data from the U.S. Census Bureau. The area of each rectangle represents the sector's share of the entire total estimated expenditures of $79.7  billion statewide. Hover over each rectangle to see specific numbers, and click or touch each rectangle to drill down to see a detailed breakdown of spending in each sector.*

Data Source: <a href="http://www.census.gov/govs/local/"><em>U.S. Census Bureau Annual Survey of State and Local Government Finances</em></a>.
<h3>What you'll need to create this visualization:</h3>
<ul>
<li>The latest version of D3Plus, which is available for download <a href="https://github.com/alexandersimoes/d3plus">here</a> on Github.</li>
<li>A text editor (I recommend <a href="http://sublimetext.org">Sublime Text</a> or <a href="https://panic.com/coda/">Coda</a>, but TextEdit for Mac or Text Editor for Windows will get the job done).</li>
<li>A spreadsheet application such as Excel or Google Sheets to format the data.</li>
<li><a href="https://shancarter.github.io/mr-data-converter/">Mr. Data Converter </a>to convert the .csv data into JSON (JavaScript Object Notation) that D3 can easily read and bind to the visualization.</li>
</ul>
<h3>Source Code for Treemap</h3>

