# Home

ScrapingMonkey v2 - RESTful API to scrape website content.

In this doc you will find both API calls syntax and some practical examples.

Doc status: WIP

## Get ScrapingMonkey

You can find [ScrapingMonkey on RapidAPI](https://rapidapi.com/onipot/api/scrapingmonkey), the biggest API marketplace!

## Pricing plans

There are 4 different plans, including a free one to let you taste the features! Check them out on RapidAPI!

# API Calls

All the requests have to be called using POST, with a "content-type": "application/x-www-form-urlencoded".
See RapidAPI examples to have an overview according to the programming language you use.


## /getLinks

Get all links in the page. For each link it returns both associated url and text.
You can filter links too.

### Parameters

- url: The target website; (Required)
- selector: css selector that describes where to search for the links (optional)
- href: Filter links by href; (optional)
- text: Filter links by text (optional)

### Example

In this example I am going to get all the links in the right corner list.

_Parameters_
- url: https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet
- selector: ul.m-0 .Box-row

_Response_

`{"response":"OK","result":[{"href":"\/adam-p\/markdown-here\/wiki","text":"Home"},{"href":"\/adam-p\/markdown-here\/wiki\/Compatibility","text":"Compatibility"},{"href":"\/adam-p\/markdown-here\/wiki\/Development-Notes","text":"Development Notes"},{"href":"\/adam-p\/markdown-here\/wiki\/Markdown-Cheatsheet","text":"Markdown Cheatsheet"},{"href":"\/adam-p\/markdown-here\/wiki\/Markdown-Here-Cheatsheet","text":"Markdown Here Cheatsheet"},{"href":"\/adam-p\/markdown-here\/wiki\/Other-Markdown-Tools","text":"Other Markdown Tools"},{"href":"\/adam-p\/markdown-here\/wiki\/Press,-Posts,-Reviews,-Etc.","text":"Press, Posts, Reviews, Etc."},{"href":"\/adam-p\/markdown-here\/wiki\/Reviews","text":"Reviews"},{"href":"\/adam-p\/markdown-here\/wiki\/Tips-and-Tricks","text":"Tips and Tricks"},{"href":"\/adam-p\/markdown-here\/wiki\/Troubleshooting","text":"Troubleshooting"}]}`

## /bySelector

Get elements in the page using cssSelector syntax.


### Parameters

- url: The target website; (Required)
- selector: css selector that describes where to search for the elements; the searching context; (Required)
- sub_elements: sub elements you want to scrape individually; json specified. (optional)

sub_elements format: `[{"name":"some_personal_label", "sub_element": "sub element to search for in the given context, using css selector", "attr" : ["first attribute to scrape",.., "nth attribute"]},  {..}]`

As you can see, in a given context you can scrape multiple sub elements and for each sub element, you can get multiple attributes.

### Example

In this example I am going to get all the links in the right corner list, as the previous example.

In this case the base searching context is _ul.m-0 .Box-row_ and I want to get href and text for each link in _\<strong\>_ tag.

_Parameters_
- url: https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet
- selector: ul.m-0 .Box-row
- sub_elements: `[{"name":"table_header", "sub_element": "strong a", "attr" : ["href", "text"]}]`

_Response_

`{"response":"OK","result":[{"table_header":{"href":"\/adam-p\/markdown-here\/wiki","text":"Home"}},{"table_header":{"href":"\/adam-p\/markdown-here\/wiki\/Compatibility","text":"Compatibility"}},{"table_header":{"href":"\/adam-p\/markdown-here\/wiki\/Development-Notes","text":"Development Notes"}},{"table_header":{"href":"\/adam-p\/markdown-here\/wiki\/Markdown-Cheatsheet","text":"Markdown Cheatsheet"}},{"table_header":{"href":"\/adam-p\/markdown-here\/wiki\/Markdown-Here-Cheatsheet","text":"Markdown Here Cheatsheet"}},{"table_header":{"href":"\/adam-p\/markdown-here\/wiki\/Other-Markdown-Tools","text":"Other Markdown Tools"}},{"table_header":{"href":"\/adam-p\/markdown-here\/wiki\/Press,-Posts,-Reviews,-Etc.","text":"Press, Posts, Reviews, Etc."}},{"table_header":{"href":"\/adam-p\/markdown-here\/wiki\/Reviews","text":"Reviews"}},{"table_header":{"href":"\/adam-p\/markdown-here\/wiki\/Tips-and-Tricks","text":"Tips and Tricks"}},{"table_header":{"href":"\/adam-p\/markdown-here\/wiki\/Troubleshooting","text":"Troubleshooting"}}]}`

# /byId

# /byTag

# /byClass

# /getRawHtml

# /scrapeHTML

# /submitForm

# /getLinkByName
