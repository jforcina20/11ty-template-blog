---
title: Using the Wikipedia API via the wikipedia-query tag
description: Second lab post for IST 402 on using an API.
date: 2022-01-30
tags:
  - labs
layout: layouts/post.njk
---
While familiar with them, prior to this week in class I had never used an API. APIs are useful tools that enable developers to connect to another service, Wikipedia in the case of this blog post, and communicate data. 

## Fetch
`fetch` is a JavaScript interface for making HTTP requests and receiving responses. It is a common way to retrieve data over the internet via APIs. In this lab, using `fetch` enables me to make an HTTP GET request for information from the Wikipedia API and send the response to be rendered by a web component. Take a look at how `fetch` is used in the wikipedia-query.js file:

```js
updateArticle(search, headers, language) {
    fetch(
      `https://${language}.wikipedia.org/w/api.php?origin=*&action=query&titles=${search}&prop=extracts&format=json`,
      headers
    )
      .then((response) => {
        if (response.ok) return response.json();
      })
      .then((json) => {
        this.handleResponse(json);
      });
  }
```
After sending the request to the Wikipedia API (which I'll go over next) the resulting JSON response is sent to a LitElement to be rendered in HTML. 

The DOM is updated when variables are updated and may require the page to be re-rendered with new data. `fetch` can be used to assign variables data from an API. When the API is called and the variables are updated, the DOM is updated and the page is re-rendered. 

## Wikipedia API
The Wikipedia API allows developers to connect to Wikipedia and search or manage wiki entries. While the API has several actions, in this lab specifically I am using the query function. According to the [API documentation](https://www.mediawiki.org/wiki/API:Query): "The `action=query` module allows you to fetch information about a wiki and the data stored in it, such as the wikitext of a particular page, the links and categories of a set of pages, or the token you need to change wiki content." This lab is specifically querying for extracts of the specified pages. 

To use the API in this lab, I placed the `wikipedia-query` tag in the LocationFromIP.js file that is responsible for retrieving the location of the user who opens the page and rendering it on an embedded Google Map. I took the city and state data from the location API and stored it in two objects. 

I placed three tags at the bottom of the render function for the map iframe and link. 

```js
render() {
    const url = `https://maps.google.com/maps?q=${this.lat},${this.long}&t=&z=15&ie=UTF8&iwloc=&output=embed`;
    return html`
      <iframe title="Where you are" src="${url}"></iframe>
      <p>
        <a href="https://www.google.com/maps/@${this.lat},${this.long},14z"
          >https://www.google.com/maps/@${this.lat},${this.long},14z</a
        >
      </p>
      <wikipedia-query search="${this.city}, ${this.state}"></wikipedia-query>
      <wikipedia-query search="${this.city}"></wikipedia-query>
      <wikipedia-query search="${this.state}"></wikipedia-query>
    `;
  }
}
```
The way the Wikipedia API works in this lab starts with the `wikipedia-query` tag. The location value is sent to the API as the search value for the `fetch`. After receiving the request, the API searches for a wiki page that matches the title sent. If a match is found, a JSON response is sent containing HTML extracts for that wiki page. An example JSON response for State College, Pennsylvania is shown below:

```json
batchcomplete": "",
"warnings": {
"extracts": {
"*": "HTML may be malformed and/or unbalanced and may omit 
inline images. Use at your own risk. Known problems are 
listed at 
https://www.mediawiki.org/wiki/Special:MyLanguage/Extension:TextExtracts#Caveats."
}
},
"query": {
"pages": {
"131800": {
"pageid": 131800,
"ns": 0,
"title": "State College, Pennsylvania",
"extract": "<link rel=\"mw-deduplicated-inline-style\" href=\"mw-data:TemplateStyles:r1033289096\">\n<p class=\"mw-empty-elt\">\n</p>\n<p><b>State College</b> is a home rule municipality in Centre County in the Commonwealth of Pennsylvania.  It is a college town, dominated economically and demographically by the presence of the University Park campus of the Pennsylvania State University (Penn State).\n</p><p>State College is the largest designated borough in Pennsylvania. It is the principal borough of the six municipalities that make up the State College area, the largest settlement in Centre County and one of the principal cities of the greater State College-DuBois Combined Statistical Area with a combined population of 236,577 as of the 2010 United States Census. In the 2010 census, the borough population was 42,034 with approximately 105,000 living in the borough plus the surrounding townships often referred to locally as the \"Centre Region.\" Many of these Centre Region communities also carry a \"State College, PA\" address although they are not part of the borough of State College.  \"Happy Valley\" and \"Lion Country\" are other terms used to identify the State College area including the borough as well as the townships of College, Harris, Patton, and Ferguson.\n</p>\n\n\n<h2><span id=\"History\">History</span></h2>\n<p>State College evolved from a village to a town to serve the needs of the Pennsylvania State College, founded as the Farmers' High School of Pennsylvania in 1855. State College was incorporated as a borough on August 29, 1896, and has grown with the college, which was renamed The Pennsylvania State University in 1953.\n</p><p>In 1973 State College adopted a home rule charter which took effect in 1976; since then, it has not been governed by the state's Borough Code, although it retains \"Borough of State College\" as its official name.\n</p><p>The university has a post office address of University Park, Pennsylvania. When Penn State changed its name from College to University in 1953, its president, Milton S. Eisenhower, sought to persuade the town to change its name as well. A referendum failed to yield a majority for any of the choices for a new name, and so the town remains State College. After this, Penn State requested a new name for its on-campus post office in the HUB-Robeson Center from the U.S. Post Office Department. The post office, which has since moved across an alley to the McAllister Building, is the official home of ZIP code 16802 (University Park)...
```
The response is rendered in HTML to be displayed on the page.

This API is a useful tool for people trying to access information about anything Wikipedia has a wiki on. You can check out the API documentation [here](https://en.wikipedia.org/w/api.php). Take a look at the source code for this lab in my [github repository](https://github.com/jforcina20/ip-project/blob/master/src/LocationFromIP.js) to learn more about how I completed it.