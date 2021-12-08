# ScrapingMonkey (SM)

ScrapingMonkey it's a RESTful web automation API. It's meant to be general purpose, so you can exploit it according to your needs. 
Do not worry about setups, dependencies, etc.. you just need simple POST requests.

This tool is mainly built for *data scientists*, *students* and *web-app testers*, I hope to make you save some precious time!

In this doc you will find both API endpoints descriptions and some practical examples.

ScrapingMonkey v2 will be available really soon and v1 superseded.

Note: For v1 documentation, [see here](https://github.com/masc-it/ScrapingMonkey/tree/v1)

ScrapingMonkey Task Builder (soon): build your task pipelines with a simple GUI; automatically and interactively build selectors for your dom targets.

![Task Builder](https://github.com/masc-it/ScrapingMonkey/raw/v2/imgs/sm_taskbuilder.png)

Now go ahead and explore all the features!

## Get ScrapingMonkey API

You can find [ScrapingMonkey on RapidAPI](https://rapidapi.com/onipot/api/scrapingmonkey), the biggest API Hub out there!

## Get ScrapingMonkey Standalone

ScrapingMonkey will be soon available as a standalone package too, which you can easily configure and deploy on your own machine! (with more endpoints and fine-grained control on the system).

Estimated price: 150$ (lifetime license and updates).

[Contact me](mailto:onipot.contact@gmail.com) for more information.

## Get ScrapingMonkey source code
Source code? [Contact me](mailto:onipot.contact@gmail.com) for more information.

## Plans pricing

There are 4 different plans, including a free one to let you taste the features! Check them out on RapidAPI!
Custom plans are available too. You can [contact me](mailto:onipot.contact@gmail.com) for more information.

## Discounts for university members

If you are a university member (student, researcher, etc) [contact me](mailto:onipot.contact@gmail.com) with your academic email to have a 15% discount on all the plans.

Use *SM UNIVERSITY* as subject of the email.

## Plans features

Each plan has its own set of custom features, better talking about them first!

### Unlimited task size
- BASIC: up to 10 targets/actions.
- PRO: up to 20 targets/actions. 
- ULTRA and MEGA: unlimited size.

### High quality proxies

MEGA and ULTRA accounts have maximum priority. 

The Proxy choice follows this priority queue: MEGA > ULTRA > PRO > BASIC.

Of course, according to the load in a given instant, also low tier accounts have good chances to get high quality proxies!

Check the [ToDo/Wish list](https://masc-it.github.io/ScrapingMonkey/#/?id=todo-list ':target=_self') for more info about future developments and improvements.

### FullHD screenshots
- BASIC and PRO: 1280x720px. 
- ULTRA and MEGA: 1920x1080px

### Max number of screenshots (per task)

- BASIC and PRO: 2
- ULTRA: 5
- MEGA: up to 20

### Concurrent requests (per second)

- BASIC and PRO: 1
- ULTRA: up to 5
- MEGA: up to 10

These limits may vary (+/-) according to the load on the servers.

### Task Builder GUI (soon)

Available only for ULTRA and MEGA subscribers.

# API endpoints

All the endpoints require POST and "content-type": "application/json" (See [Example](https://masc-it.github.io/ScrapingMonkey/#/?id=examples ':target=_self')).

Task also a look at RapidAPI *Code Snippets* section! They show how to build requests in almost all programming languages (Node.js, php, C, C#, Java, Go, etc).

ScrapingMonkey uses low-latency, high-quality and periodically updated proxies, in order to let you scrape without any problem. Proxies are located in UK, US and DE. More will be added according to necessity.

# /getPageHtml

Retrieve page source code. The html will include both statically and dinamically-loaded dom elements.

## Parameters

- url (string)

## Response

The html code.
	

# /run

Run a scraping task. (See [Plans Features](https://masc-it.github.io/ScrapingMonkey/#/?id=plans-features ':target=_self'))

## Parameters

- url (string)
- pipeline (json array)

The pipeline parameter is a json array where each object either represents a **target** (a page element) to scrape or an **action** to perform on the requested web page. 

A **pipeline target** is a json object made in this way:

- name: A meaningful alias for the target. This is a user defined constant, you are going to use it to parse the response.
- operation (string)
	- SCRAPE
- selector
	- "selector": {"type": "XPATH", "selector": "your xpath selector"} or {"type": "CSS", "selector": "your css selector"}

A **pipeline action** is a json object made in this way:

- name: A meaningful alias for the action. This is a user defined constant, useful to retrieve data in the response.
- operation (string)
	- CLICK
	- RUN_SCRIPT
	- PRESS_KEY
	- SEND_KEYS
	- GO_TO
	- SCREENSHOT
- selector
	- "selector": {"type": "XPATH", "selector": "your xpath selector"} or {"type": "CSS", "selector": "your css selector"}
	- It is *mandatory* for CLICK and SEND_KEYS
- data (string)
	- This parameter assumes a different meaning according to the type of action.
	- It is *mandatory* for RUN_SCRIPT, GO_TO and SEND_KEYS
	
Follows a description of all the supported **operations**.

## Operations

### CLICK

Click an element in the currently loaded page. 

- selector:  {"type": "XPATH", "selector": "your xpath selector"} or {"type": "CSS", "selector": "your css selector"}

### SEND_KEYS

Send characters to an input field.

- selector: {"type": "XPATH", "selector": "your xpath selector"} or {"type": "CSS", "selector": "your css selector"}
- data: the string to send

### GO_TO

Redirect to a page.

- data: the URL to load.

### RUN_SCRIPT

Run a javascript code snippet.

- data: javascript code to run.

An example of task with a RUN_SCRIPT action (passing a parameter too):

	{
		"url": "https://stackoverflow.com/questions/58787864/changing-primary-palette-color-when-using-kivymd-has-no-effect-on-buttons",
		"pipeline": [
		    {
		        "name": "t1",
		        "operation": "SCRAPE",
		        "selector": {"type": "XPATH", "selector": "//a[contains(@href, 'lastact')]"}
		    },
		    {
		        "name": "a1",
		        "operation": "RUN_SCRIPT",
		        "data": "let el = document.evaluate(\"//a[contains(., 'An issue filtering related models')]\", document, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null ); el.singleNodeValue.scrollIntoView()"
		    }
		]
	}


### SCREENSHOT

Take a screenshot of the current page, encoded in base64. (See [Plans Features](https://masc-it.github.io/ScrapingMonkey/#/?id=plans-features ':target=_self'))


## Response

ScrapingMonkey replies with a json object structured in the following way, i.e:

	{
		"result": {
		    "t1": {
		        "status": "OK",
		        "elements": [
		            {
		                "href": {
		                    "attr": "href",
		                    "value": "?lastactivity"
		                },
		                "class": {
		                    "attr": "class",
		                    "value": "s-link s-link__inherit"
		                },
		                "title": {
		                    "attr": "title",
		                    "value": "2021-09-20 19:42:54Z"
		                },
		                "innerHTML": {
		                    "value": "2 months ago"
		                },
		                "textContent": {
		                    "value": "2 months ago"
		                }
		            }
		        ]
		    },
		    "a1": {
		        "status": "OK"
		    }
		}
	}

In short, each field of the response corresponds to the *target* name you have specified in the pipeline request. 

Each field (i.e. t1), is a json object made of:
- elements (json array)
	- all the scraped elements matching the target selector
	- each object contains all the html attributes of the target
- target_status (string)
	- OK
	- NOT_FOUND

A good practice, before parsing elements, would be to check for each target, the *target_status* field and assure it is set as *OK*.


# /by (coming soon)

The following endpoints allow you to select dom elements using different types of approaches.

## /byId
## /byClass
## /byTag
## /byCss
## /byXPath
## /byLinkText

## Parameters
- url (string)
- selector (string)
	- byId: the id of the target
	- byClass: the class name
	- byTag: the tag name of the target
	- ...

## Response

	{
		"elements":[
			{
				"id":"elementId",
				"text":"Today's a sunny day here in NYC",
				"tag":"span",
				"pos":{"x":367,"y":173,"w":414,"h":65},
				"another-attr": "value",
				..
			},
			...
		],
		"status":"OK"
	}


# ToDo list

- [x] High quality proxies for everybody
- [x] More/better servers to improve concurrency (and response time)
- [ ] For-loop action
- [ ] Possibility to use, in the same task, targets previously scraped in the pipeline
- [ ] JSON parsing
	

# Examples

Consider the following task:

- Load  the web page, a stackoverflow post
- Get all the dom elements matching the CSS selector .question-hyperlink; refer this target as Target #1
- Take a screenshot
- Run a js code snippet to scroll down the page
- Take another screenshot

In json, it is so defined:

    {
		"url": "https://stackoverflow.com/questions/58787864/changing-primary-palette-color-when-using-kivymd-has-no-effect-on-buttons",
		"pipeline": [
		    {
		        "name": "t1",
		        "operation": "SCRAPE",
		        "selector": {"type": "XPATH", "selector": "//a[contains(@href, 'lastact')]"}
		    },
		    {
		        "name": "a1",
		        "operation": "RUN_SCRIPT",
		        "data": "let el = document.evaluate(\"//a[contains(., 'An issue filtering related models')]\", document, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null ); el.singleNodeValue.scrollIntoView()"
		    },
		    {
                "name": "screen",
                "operation": "SCREENSHOT",
                "selector": {
                    "type": "XPATH",
                    "selector": "//body"
                },
                "data": {
                    "type": "base64"
                }
            }
		]
	}


In python, you can parse a generic json from file using the code below:

    def parse_task(task_file):
    	with  open(task_file, "r") as  f:
    		return json.loads(f.read())

Now, let's build and run the actual request. For this, I am using the *requests* package.


    import requests

	url = "https://scrapingmonkey.p.rapidapi.com/run"

	payload = parse_task("your-task-file.json")
	headers = {
		'content-type': "application/json",
		'x-rapidapi-key': "your-scrapingmonkey-key",
		'x-rapidapi-host': "scrapingmonkey.p.rapidapi.com"
		}

	response = requests.post(url, data=payload, headers=headers)

	print(response.text)

In our particular example, the response will look like this:

    {
		"result": {
		    "t1": {
		        "status": "OK",
		        "elements": [
		            {
		                "href": {
		                    "attr": "href",
		                    "value": "?lastactivity"
		                },
		                "class": {
		                    "attr": "class",
		                    "value": "s-link s-link__inherit"
		                },
		                "title": {
		                    "attr": "title",
		                    "value": "2021-09-20 19:42:54Z"
		                },
		                "innerHTML": {
		                    "value": "2 months ago"
		                },
		                "textContent": {
		                    "value": "2 months ago"
		                }
		            }
		        ]
		    },
		    "a1": {
		        "status": "OK"
		    },
		    "screen": your base64-encoded screenshot
		}
	}

If you'd like to actually read the images, in python you'd do something like this:
	
	import base64
	imgdata = base64.b64decode(json_response["screen"]) # replace "screen" with your own alias for the screenshot
	filename = 'image.png'
	with open(filename, 'wb') as f:
		f.write(imgdata)
		
# Disclaimers

ScrapingMonkey, as it is designed, is a GOOD BOT by definition, I am not responsible for how you use this simple yet powerful tool.


