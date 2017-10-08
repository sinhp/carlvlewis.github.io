---
layout: archive
title: "Predicted Ebola cases by 2030 by current trajectory, CDC"
excerpt: "Although perhaps not the most visually succinct way of displaying trend data, the use of an animated area graph in this d3.js chart"
collection: portfolio
tags: D3.JS DATA-VIZ
permalink: /portfolio/ebola/
date: 2015-01-31
header:
  teaser: /portfolio/ebola.png
sidebar:
  - title: "Role"
    image: /images/portfolio/ebola.png
    image_alt: "logo"
    text: "Designer, Front-End Developer"
  - title: "Responsibilities"
    text: "Reuters try PR stupid commenters should isn't a business model"
gallery:
  - url:
    image_path:
    alt:
  - url:
    image_path:
    alt:
  - url:
    image_path:
    alt:
---

<iframe src="http://carlvlewis.info/ebola_d3" width="100%" height="500px" scrolling="no" frameborder="no"></iframe>

Although perhaps not the most visually succinct way of displaying trend data, the use of an animated area graph in this d3.js chart I produced helps provide the viewer a visual cue not at what has already happened, but what is forecast to happen if current trends of infection keep pace. Rather than rely on dotted lines that may be hard to read, this d3.js visualization simply makes steps along the time-series y-axis for each month in the future, with motion indicating in a subtle manner that the data is projected, not past. In turn, the raw figures of the predictions themselves are rendered at the top of the chart using Crossfilter.js.

Data Source: [CDC](http://cdc.gov/)

{% include gallery caption="" %}
