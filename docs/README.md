# Home

ScrapingMonkey - RESTful API for web scraping.

In this doc you will find both API endpoints structure and some practical examples.

ScrapingMonkey Task Builder (soon): build your task pipelines with a simple GUI; automatically and interactively build selectors for your dom targets.

Doc status: WIP

## Get ScrapingMonkey

You can find [ScrapingMonkey on RapidAPI](https://rapidapi.com/onipot/api/scrapingmonkey), the biggest API marketplace!

ScrapingMonkey will be soon available as a standalone package too, which you can easily deploy on your own machine! (with more endpoints and fine-grained control on the system).

## Pricing plans

There are 4 different plans, including a free one to let you taste the features! Check them out on RapidAPI!

# API endpoints

All the requests require POST, with "Content-Type": "application/x-www-form-urlencoded".

See RapidAPI examples to have an overview according to the programming language you use.

ScrapingMonkey uses low-latency, high-quality and periodically updated proxies, in order to let you scrape without any problem. Proxies are located in UK, US and DE. More will be added according to usage.

## /getSource

Retrieve page source code. The html will include both statically and dinamically loaded dom elements.

## Parameters

- url (string)

## Response

	{
		"status" : "OK" or "ERR",
		"html" : "html-source-code"
	
	}
	

## /run

Run a scraping task.

## Parameters

- url (string)
- pipeline (json array)

The pipeline parameter is a json array where each object either represents a **target** (a page element) to scrape or an **action** to perform on the requested web page. 

A **pipeline target** is a json object made in this way:

- name: A meaningful alias for the target. This is a user defined constant, you are going to use it to parse the response.
- type (string)
	- XPATH
	- CSS
- selector
	- an xpath or css selector

A **pipeline action** is a json object made in this way:

- name: A meaningful alias for an action. This is a user defined constant, useful to retrieve data in the response.
- type (string)
	- CLICK
	- RUN_SCRIPT
	- PREV_PAGE
	- NEXT_PAGE
	- SEND_KEYS
	- GO_TO
	- SCREENSHOT
- selector
	- an xpath or css selector
	- It is *mandatory* for CLICK and SEND_KEYS
	- It is optional for the others.
- data
	- This parameter assume different meaning according to the type of action. 
	
Follow a description of all the actions supported.

### CLICK

Click an element in the currently loaded page. 

- selector: xpath selector to find the element to be clicked.

### GO_TO

Redirect to a page.

- data: the URL to load.

### PREV_PAGE, NEXT_PAGE

Navigate to the previous or next page (if any).

### RUN_SCRIPT

Run a javascript code snippet. You can either pass a dom element to the script, in that case you need to specify it using the selector parameter and access it using *arguments[0]*.

- data: javascript code to run.
- selector (optional): xpath selector; the target element can then be used in your code using the local variable  *arguments[0]*.

A task with an action of type SCRIPT (passing a parameter too):

	{ 
		"url" : "https://stackoverflow.com/questions/58787864/changing-primary-palette-color-when-using-kivymd-has-no-effect-on-buttons"
		"pipeline": 
			[
				{
					"name": "last_activity", 
					"type": "XPATH", 
					"selector": "//a[contains(@href, 'lastact')]"
				}, 
				{
					"name": "scroll_to", 
					"type": "RUN_SCRIPT", 
					"selector": "//a[contains(., 'An issue filtering related models')]", 
					"data" : "arguments[0].scrollIntoView()"
				}, 
				{
					"name": "screen", 
					"type": "SCREENSHOT", 
					"data" : "screen0.png"
				}
			]
	}

### SCREENSHOT

Take a screenshot of the current page.

data: It defines the save path of the screenshot.


## Response

A json object structured in the following way, i.e:

		    {"target1": // this is the target alias, that you specify using the *name* parameter
			    {
					"elements":[
						{	"class":"s-link s-link__inherit",
							"href":"? lastactivity",
							"title":"2021-06-30 15:45:40Z",
							"text":"12 days ago",
							"tag":"a",
							"rect":{"x":729,"y":119,"w":71,"h":15}
						},
						...
				    ],
					"target_status": "OK" or "NOT_FOUND"
				}
		    }

In short, each field of the response corresponds to the *target* name you have spcified in the pipeline request. 

Each field (i.e. target1), is a json object made of:
- elements (json array)
	- all the scraped elements matching the target selector
	- each object contains all the html attributes of the target
- target_status (string)
	- OK
	- NOT_FOUND

# Examples

Consider the following task:

- Load  the web page, a stackoverflow post
- Get all the dom elements matching the CSS selector .question-hyperlink; refer this target as Target #1
- Take a screenshot and save it to screen0.png
- Run a js code snippet to scroll down the page
- Take another screenshot and save it to screen1.png

In json, it is so defined:

    {
	    "url":"https://stackoverflow.com/questions/9942594/unicodeencodeerror-ascii-codec-cant-encode-character-u-xa0-in-position-20",
	    "pipeline":[
		    {
			    "name":"Target #1",
			    "type":"CSS",
			    "selector":".question-hyperlink"
		    },
			{	"name":"Action #2",
				"type":"SCREENSHOT",
				"data":"screen0.png",
				"selector":""
			},
			{
				"name":"scroll down",
				"type":"RUN_SCRIPT",
				"data":"window.scrollTo(0,1600);",
				"selector":""
			},
			{
				"name":"screen",
				"type":"SCREENSHOT",
				"data":"screen1.png",
				"selector":""
			}]
		}


In python, you can parse a generic json from file using the code below:

    def parse_task(task_file):
    	with  open(task_file, "r") as  f:
    		return json.loads(f.read())

Then, you can build your request like this:

    def build_request_url(task_json):
    	return "https://scrapingmonkey.rapidapi.com/run?&url=%s&pipeline=%s" 
		    	% ( task_json["url"], task_json["pipeline"], )

Now, let's run the actual request. For this, I am using the *requests* package.


    task_json = parse_task("your-task-path")
    request_url = build_request_url(task_json)
    response = requests.get(request_url).text

In our particular example, the response will look like this:

    {  
      "Target #1": {  
        "elements": [  
          {  
            "class": "question-hyperlink",  
            "href": "/questions/9942594/unicodeencodeerror-ascii-codec-cant-encode-character-u-xa0-in-position-20",
            "text": "UnicodeEncodeError: \u0027ascii\u0027 codec can\u0027t encode character u\u0027\\xa0\u0027 in position 20: ordinal not in range(128)",  
            "rect": {  
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
		      "rect": {  
			      "x": 1308,  
			      "y": 1699,  
			      "w": 252,  
			      "h": 34  
			   }  
          },
          ...

And of course, as specified in the pipeline, you'll find two screenshots, named *screen0.png* and *screen1.png* :D

