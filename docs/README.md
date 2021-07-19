# Home

ScrapingMonkey - RESTful web scraping API.

In this doc you will find both API endpoints structure and some practical examples.

ScrapingMonkey v2 will be available really soon. v1 won't be available anymore.

Note: For v1 documentation, [see here](https://github.com/masc-it/ScrapingMonkey/tree/v1)

ScrapingMonkey Task Builder (soon): build your task pipelines with a simple GUI; automatically and interactively build selectors for your dom targets.

![Task Builder](https://github.com/masc-it/ScrapingMonkey/raw/v2/imgs/sm_taskbuilder.png)


## Get ScrapingMonkey

You can find [ScrapingMonkey on RapidAPI](https://rapidapi.com/onipot/api/scrapingmonkey), the biggest API marketplace!

ScrapingMonkey will be soon available as a standalone package too, which you can easily deploy on your own machine! (with more endpoints and fine-grained control on the system).

## Plans pricing

There are 4 different plans, including a free one to let you taste the features! Check them out on RapidAPI!

## Plans features

Each plan has its own set of custom features, better talking about them first!

### Unlimited task size
- BASIC: up to 10 targets/actions.
- PRO: up to 20 targets/actions. 
- ULTRA and MEGA: unlimited size.


### Unlimited task duration

- BASIC and PRO: up to 20 seconds.
- ULTRA and MEGA: indefinite. 

### High quality proxies

ULTRA and MEGA accounts have priority.

### FullHD screenshots
- BASIC and PRO: 1280x720px. 
- ULTRA and MEGA: 1920x1080px

### Max number of screenshots

- BASIC and PRO: 2
- ULTRA: 5
- MEGA: up to 20

### Concurrent requests

- BASIC and PRO: no
- ULTRA and MEGA: up to 50.

# API endpoints

All the endpoints require POST and "content-type": "application/json" (See [Example](https://masc-it.github.io/ScrapingMonkey/#/?id=examples)).

Task also a look at RapidAPI *Code Snippets* section! They show how to build requests in almost all programming languages (Node.js, php, C, C#, Java, Go, etc).

ScrapingMonkey uses low-latency, high-quality and periodically updated proxies, in order to let you scrape without any problem. Proxies are located in UK, US and DE. More will be added according to necessity.

# /getPageHtml

Retrieve page source code. The html will include both statically and dinamically-loaded dom elements.

## Parameters

- url (string)

## Response

The html code.
	

# /run

Run a scraping task. (See [Plans Features](https://masc-it.github.io/ScrapingMonkey/#/?id=plans-features))

## Parameters

- url (string)
- pipeline (json array)

The pipeline parameter is a json array where each object either represents a **target** (a page element) to scrape or an **action** to perform on the requested web page. 

A **pipeline target** is a json object made in this way:

- name: A meaningful alias for the target. This is a user defined constant, you are going to use it to parse the response.
- operation (string)
	- XPATH
	- CSS
- selector
	- an xpath or css selector

A **pipeline action** is a json object made in this way:

- name: A meaningful alias for the action. This is a user defined constant, useful to retrieve data in the response.
- operation (string)
	- CLICK
	- RUN_SCRIPT
	- PREV_PAGE
	- NEXT_PAGE
	- SEND_KEYS
	- GO_TO
	- SCREENSHOT
- selector (string)
	- an xpath selector
	- It is *mandatory* for CLICK and SEND_KEYS
- data (string)
	- This parameter assumes a different meaning according to the type of action.
	- It is *mandatory* for RUN_SCRIPT, GO_TO and SEND_KEYS
	
Follows a description of all the supported **operations**.

## Operations

### CLICK

Click an element in the currently loaded page. 

- selector: xpath selector to find the element to be clicked.

### SEND_KEYS

Send characters to an input field.

- selector: xpath selector to the input element 
- data: the string to send

### GO_TO

Redirect to a page.

- data: the URL to load.

### PREV_PAGE, NEXT_PAGE

Navigate to the previous or next page (if any).

### RUN_SCRIPT

Run a javascript code snippet. You can either pass a dom element to the script, in that case you need to specify it using the selector parameter and access it using *arguments[0]*.

- data: javascript code to run.
- selector (optional): xpath selector; the target element can then be used in your code using the local variable  *arguments[0]*.

An example of task with a RUN_SCRIPT action (passing a parameter too):

	{ 
		"url" : "https://stackoverflow.com/questions/58787864/changing-primary-palette-color-when-using-kivymd-has-no-effect-on-buttons",
		"pipeline": 
			[
				{
					"name": "last_activity", 
					"operation": "XPATH", 
					"selector": "//a[contains(@href, 'lastact')]"
				}, 
				{
					"name": "scroll_to", 
					"operation": "RUN_SCRIPT", 
					"selector": "//a[contains(., 'An issue filtering related models')]", 
					"data" : "arguments[0].scrollIntoView()"
				}, 
				{
					"name": "screen", 
					"operation": "SCREENSHOT"
				}
			]
	}

In the example above, the dom element located using the XPath selector *//a[contains(., 'An issue filtering related models')]* is then used in the script and accessed using *arguments[0]*. 

### SCREENSHOT

Take a screenshot of the current page, encoded in base64. (See [Plans Features](https://masc-it.github.io/ScrapingMonkey/#/?id=plans-features))


# A generic Response

ScrapingMonkey replies with a json object structured in the following way, i.e:

	{
		"target1": // this is the target alias, that you specify using the *name* parameter
			{
				"elements":[
					{	"class":"s-link s-link__inherit",
						"href":"? lastactivity",
						"title":"2021-06-30 15:45:40Z",
						"text":"12 days ago",
						"tag":"a",
						"pos":{"x":729,"y":119,"w":71,"h":15}
					},
					...
				],
				"target_status": "OK" or "NOT_FOUND"
			}
	}

In short, each field of the response corresponds to the *target* name you have specified in the pipeline request. 

Each field (i.e. target1), is a json object made of:
- elements (json array)
	- all the scraped elements matching the target selector
	- each object contains all the html attributes of the target, also its absolute position in the page
- target_status (string)
	- OK
	- NOT_FOUND

A good practice, before parsing elements, would be to check for each target, the *target_status* field and assure it is set as *OK*.

# Examples

Consider the following task:

- Load  the web page, a stackoverflow post
- Get all the dom elements matching the CSS selector .question-hyperlink; refer this target as Target #1
- Take a screenshot
- Run a js code snippet to scroll down the page
- Take another screenshot

In json, it is so defined:

    {
	    "url":"https://stackoverflow.com/questions/9942594/unicodeencodeerror-ascii-codec-cant-encode-character-u-xa0-in-position-20",
	    "pipeline":[
		    {
			    "name":"Target #1",
			    "operation":"CSS",
			    "selector":".question-hyperlink"
		    },
			{	"name":"screen0",
				"operation":"SCREENSHOT"
			},
			{
				"name":"scroll down",
				"operation":"RUN_SCRIPT",
				"data":"window.scrollTo(0,1600);"
			},
			{
				"name":"screen",
				"operation":"SCREENSHOT"
			}]
		}


In python, you can parse a generic json from file using the code below:

    def parse_task(task_file):
    	with  open(task_file, "r") as  f:
    		return json.loads(f.read())

Now, let's build and run the actual request. For this, I am using the *requests* package.


    import requests

	url = "https://bottom.p.rapidapi.com/run"

	payload = parse_task("your-task-file.json")
	headers = {
		'content-type': "application/json",
		'x-rapidapi-key': "your-scrapingmonkey-key",
		'x-rapidapi-host': "bottom.p.rapidapi.com"
		}

	response = requests.post(url, data=payload, headers=headers)

	print(response.text)

In our particular example, the response will look like this:

    {  
      "Target #1": {  
        "elements": [  
          {  
            "class": "question-hyperlink",  
            "href": "/questions/9942594/unicodeencodeerror-ascii-codec-cant-encode-character-u-xa0-in-position-20",
            "text": "UnicodeEncodeError: \u0027ascii\u0027 codec can\u0027t encode character u\u0027\\xa0\u0027 in position 20: ordinal not in range(128)",  
            "pos": {  
              "x": 509,  
              "y": 77,  
              "w": 902,  
              "h": 66  
              }  
          },  
	      {  
		      "class": "question-hyperlink",
		      "href": "https://stackoverflow.com/questions/31137552/unicodeencodeerror-ascii-codec-cant-encode-character-at-special-name?noredirect\u003d1\u0026lq\u003d1",
		      "text": "UnicodeEncodeError: \u0027ascii\u0027 codec can\u0027t encode character at special name",
		      "tag": "a", 
		      "pos": {  
			      "x": 1308,  
			      "y": 1699,  
			      "w": 252,  
			      "h": 34  
			   }  
          },
          ...
          ]
      },
      	"screen0": "screenshot-in-base64",
      	"screen": "screenshot-in-base64"
      
      }

If you'd like to actually read the images, in python you'd do something like this:
	
	import base64
	imgdata = base64.b64decode(json_response["screen"]) # replace "screen" with your own alias for the screenshot
	filename = 'image.png'
	with open(filename, 'wb') as f:
		f.write(imgdata)

