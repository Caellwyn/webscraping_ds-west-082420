
# Python Web Scraping Tutorial using BeautifulSoup

![soup](https://media.giphy.com/media/jeAJZwhBwyPja/giphy.gif)

## Agenda:  
    1. Describe webscraping in the context of other data structures
    2. Identify the basic structure of a webpage  
    3. Become familiar with the essential object and critical methods of BeautifulSoup  
    4. Be able to scrape data from a live website  

# Activation
In order to inspect the structure of a web page, your browser provides a developer tools window.
You can access it with **opt-cmd-i**.
Then, by clicking on the elements button, the user can inspect individual elements, and even change attributes such as text.

Take a minute and visit a website, change some text, take a screen shot, and Slack it out.


## Context

We have already developed many ways of interacting with data.  We are able to:
* import csv's and csv-like objects
* gather data from API's and interact with JSON objects
* query SQL databases

While the above data structures may be the only source of data for any given project, often you will desire *suplemental* data.  There is publically available data all over the internet ripe for scraping whether that be artist information data from wikipedia, song lyrics from songlyrics.com, or text of famous books from Project Gutenberg.  Below, we will learn how to navigate HTML and CSS to gather data onto our local computer and turn it into our friendly dataframe objects.



![scraping](https://media.giphy.com/media/l2JdZJis5RKQSqXFm/giphy.gif)

## The components of a web page


When we visit a web page, our web browser makes a GET request to a web server. The server then sends back files that tell our browser how to render the page for us. The files fall into a few main types:

- HTML — contain the main content of the page.
- CSS — add styling to make the page look nicer.
- JS — Javascript files add interactivity to web pages.
- Images — image formats, such as JPG and PNG allow web pages to show pictures.

After our browser receives all the files, it renders the page and displays it to us. There’s a lot that happens behind the scenes to render a page nicely, but we don’t need to worry about most of it when we’re web scraping.

### HTML


HyperText Markup Language (HTML) is a language that web pages are created in. HTML isn’t a programming language, like Python — instead, it’s a markup language that tells a browser how to layout content. 

Let’s take a quick tour through HTML so we know enough to scrape effectively. HTML consists of elements called tags. The most basic tag is the `<html>` tag. This tag tells the web browser that everything inside of it is HTML. We can make a simple HTML document just using this tag:

~~~
<html>
</html>
~~~
&#x2B07; Example cell with html in Markdown!

<html>
</html>

Right inside an html tag, we put two other tags, the head tag, and the body tag. The main content of the web page goes into the body tag. The head tag contains data about the title of the page, and other information that generally isn’t useful in web scraping:

~~~
<html>
    <head>
    </head>
    <body>
    </body>
</html>
~~~

We’ll now add our first content to the page, in the form of the p tag. The p tag defines a paragraph, and any text inside the tag is shown as a separate paragraph:
~~~
<html>
    <head>
    </head>
    <body>
          <p>
            Here's a paragraph of text!
        </p>
        <p>
            Here's a second paragraph of text!
        </p>
    </body>
</html>
~~~

<html>
    <head>
    </head>
    <body>
          <p>
            Here's a paragraph of text!
        </p>
        <p>
            Here's a second paragraph of text!
        </p>
    </body>
</html>

Tags have commonly used names that depend on their position in relation to other tags:

- **child** — a child is a tag inside another tag. So the two p tags above are both children of the body tag.
- **parent** — a parent is the tag another tag is inside. Above, the html tag is the parent of the body tag.
- **sibiling** — a sibiling is a tag that is nested inside the same parent as another tag. For example, head and body are siblings, since they’re both inside html. Both p tags are siblings, since they’re both inside body.

We can also add properties to HTML tags that change their behavior:

~~~
<html>
  <head></head>
  <body>
    <p>
      Here's a paragraph of text!
      <a href="https://www.dataquest.io">Learn Data Science Online</a>
    </p>
    <p>
      Here's a second paragraph of text!
      <a href="https://www.python.org">Python</a>        
    </p>
  </body>
</html>
~~~

<html>
    <head>
    </head>
    <body>
        <p>
            Here's a paragraph of text!
            <a href="https://www.dataquest.io">Learn Data Science Online</a>
        </p>
        <p>
            Here's a second paragraph of text!
            <a href="https://www.python.org">Python</a>        </p>
    </body></html>

In the above example, we added two a tags. a tags are links, and tell the browser to render a link to another web page. The href property of the tag determines where the link goes.

a and p are extremely common html tags. Here are a few others:

- *div* — indicates a division, or area, of the page.
- *b* — bolds any text inside.
- *i* — italicizes any text inside.
- *table* — creates a table.
- *form* — creates an input form.


For a full list of tags, look [here](https://developer.mozilla.org/en-US/docs/Web/HTML/Element).

There are two special properties give HTML elements names, and make them easier to interact with when we’re scraping, **class** and **id** properties. 

- One element can have multiple classes, and a class can be shared between elements. 
- Each element can only have one id, and an id can only be used once on a page. 
- Classes and ids are optional, and not all elements will have them.

We can add classes and ids to our example:

~~~
<html>
    <head>
    </head>
    <body>
        <p class="bold-paragraph">
            Here's a paragraph of text!
            <a href="https://www.dataquest.io" id="learn-link">Learn Data Science Online</a>
        </p>
        <p class="bold-paragraph extra-large">
            Here's a second paragraph of text!
            <a href="https://www.python.org" class="extra-large">Python</a>
        </p>
    </body>
</html>
~~~

<html>
    <head>
    </head>
    <body>
        <p class="bold-paragraph">
            Here's a paragraph of text!
            <a href="https://www.dataquest.io" id="learn-link">Learn Data Science Online</a>
        </p>
        <p class="bold-paragraph extra-large">
            Here's a second paragraph of text!
            <a href="https://www.python.org" class="extra-large">Python</a>
        </p>
    </body>
</html>

### Let's take a look at a sample web page from our local computer
- Open up index.html in your browser.
- Open up the inspector using ctrl-shft-j
- click on the elements tab, and click on an element

Notice the syntax in the main.css file.  A period # refers to an id, and a . refers to a class.

# 5 minute break

![break](https://media.giphy.com/media/bqgtlC34AiawQZkNSF/giphy.gif)

## Webscraping with Python

### The requests library
The first thing we’ll need to do to scrape a web page is to download the page. We can download pages using the Python requests library (similar to interacting with APIs!).

After running our request, we get a Response object. This object has a status_code property, which indicates if the page was downloaded successfully:

We can print out the HTML content of the page using the content property:

### Parsing a page with BeautifulSoup

We can use the BeautifulSoup library to parse this document, and extract the text from the `<p>` tag. 

As all the tags are nested, we can move through the structure one level at a time. We can first select all the elements at the top level of the page using the `children` property of `soup`.

The above tells us that there are two tags at the top level of the page — the initial `<!DOCTYPE html>` tag, and the `<html>` tag. Let’s see what the type of each element in the list is:

The `Tag` object allows us to navigate through an HTML document, and extract other tags and text. You can learn more about the various `BeautifulSoup` objects [here](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#kinds-of-objects).

We can now select the html tag and its children by taking the second item in the list:



Each item in the list returned by the `children` property is also a `BeautifulSoup` object, so we can also call the `children` method on `html`.

Now, we can find the `children` inside the `html` tag:

As you can see above, there are two tags here, `head`, and `body`. We want to extract the text inside the `p` tag, so we’ll dive into the body:

We can now isolate the `p` tag:



Once we’ve isolated the tag, we can use the `get_text` method to extract all of the text inside the tag:

### Finding all instances of a tag at once

If we want to extract a single tag, we can instead use the find_all method, which will find all the instances of a tag on a page.

Note that `find_all` returns a list, so we’ll have to loop through, or use list indexing, it to extract text:

If you instead only want to find the first instance of a tag, you can use the `find` method, which will return a single `BeautifulSoup` object:

### Searching for tags by class and id


We introduced classes and ids earlier, but it probably wasn’t clear why they were useful. Classes and ids are used by CSS to determine which HTML elements to apply certain styles to. We can also use them when scraping to specify specific elements we want to scrape. Let's look at the following page:

~~~
<html>
    <head>
        <title>A simple example page</title>
    </head>
    <body>
        <div>
            <p class="inner-text first-item" id="first">
                First paragraph.
            </p>
            <p class="inner-text">
                Second paragraph.
            </p>
        </div>
        <p class="outer-text first-item" id="second">
            <b>
                First outer paragraph.
            </b>
        </p>
        <p class="outer-text">
            <b>
                Second outer paragraph.
            </b>
        </p>
    </body>
</html>
~~~

Now, we can use the `find_all` method to search for items by class or by id. In the below example, we’ll search for any `p` tag that has the class `outer-text`:

In the below example, we’ll look for any tag that has the class `outer-text`:



We can also search for elements by `id`:


### Using CSS Selectors


You can also search for items using CSS selectors. These selectors are how the CSS language allows developers to specify HTML tags to style. Here are some examples:

- *p a* — finds all a tags inside of a p tag.
- *body p a* — finds all a tags inside of a p tag inside of a body tag.
- *html body* — finds all body tags inside of an html tag.
- *p.outer-text* — finds all p tags with a class of outer-text.
- *p#first* — finds all p tags with an id of first.
- *body p.outer-text* — finds any p tags with a class of outer-text inside of a body tag.

`BeautifulSoup` objects support searching a page via CSS selectors using the `select` method. We can use CSS selectors to find all the `p` tags in our page that are inside of a `div` like this:

Note that the `select` method above returns a list of `BeautifulSoup` objects, just like `find` and `find_all`.

## Your Turn: Downloading weather data


We now know enough to proceed with extracting information about the local weather from the National Weather Service website. The first step is to find the page we want to scrape. We’ll extract weather information about Chicago from this [page](https://forecast.weather.gov/MapClick.php?lat=41.8843&lon=-87.6324#.XdPlJUVKg6g).

We can then scroll up in the elements panel to find the “outermost” element that contains all of the text that corresponds to the extended forecasts. In this case, it’s a `div` tag with the id `seven-day-forecast`:

We now know enough to download the page and start parsing it. Below let's implement the following:

1. Download the web page containing the forecast.
2. Create a `BeautifulSoup` class to parse the page.
3. Find the `div` with id `seven-day-forecast`, and assign to `seven_day`
4. Inside `seven_day`, find each individual forecast item.
5. Extract and print the first forecast item.

### Extracting information from the page
Let's look at `today`<br>
There are `4` pieces of information we can extract:

- The name of the forecast item — in this case, **Today**.
- The description of the conditions — this is stored in the `title` property of `img`.
- A short description of the conditions — in this case, **Chance rain**.
- The temperature high — in this case, **39 degrees**.

We’ll extract the name of the forecast item, the short description, and the temperature first, since they’re all similar:

Now, we can extract the `title` attribute from the `img` tag. To do this, we just treat the `BeautifulSoup` object like a dictionary, and pass in the attribute we want as a key:


```python
tombstones[0].select("p.period-name")
```




    [<p class="period-name">Today<br/><br/></p>,
     <p class="period-name">Tonight<br/><br/></p>,
     <p class="period-name">Friday<br/><br/></p>,
     <p class="period-name">Friday<br/>Night</p>,
     <p class="period-name">Saturday<br/><br/></p>,
     <p class="period-name">Saturday<br/>Night</p>,
     <p class="period-name">Sunday<br/><br/></p>,
     <p class="period-name">Sunday<br/>Night</p>,
     <p class="period-name">Labor<br/>Day</p>]



### Extracting all the information from the page
Now that we know how to extract each individual piece of information, we can combine our knowledge with css selectors and list comprehensions to extract everything at once.

In the below code, we:

- Select all items with the class period-name inside an item with the class tombstone-container in seven_day.
- Use a list comprehension to call the get_text method on each BeautifulSoup object.


```python
periods = [period.get_text() for period in tombstones[0].select("p.period-name")]
```

As you can see above, our technique gets us each of the period names, in order. Now you can apply the same technique to get the other 3 fields:


```python
short_descs = [desc.get_text() for desc in tombstones[0].select('p.short-desc')]
temps =  [temp.get_text() for temp in tombstones[0].select('p.temp')]
descs =   [desc['alt'] for desc in tombstones[0].select('img.forecast-icon')]
print(short_descs)
print(temps)
print(descs)
```

    ['Sunny andBreezy', 'Clear andBreezy thenClear', 'Sunny', 'Mostly Clear', 'Sunny', 'Mostly Clearthen ChanceT-storms', 'ChanceT-storms', 'ChanceT-storms', 'Partly Sunnythen SlightChanceT-storms']
    ['High: 87 °F', 'Low: 59 °F', 'High: 79 °F', 'Low: 62 °F', 'High: 83 °F', 'Low: 65 °F', 'High: 81 °F', 'Low: 66 °F', 'High: 79 °F']
    ['Today: Sunny, with a high near 87. Breezy, with a southwest wind 10 to 15 mph becoming west southwest 15 to 20 mph in the afternoon. Winds could gust as high as 35 mph. ', 'Tonight: Clear, with a low around 59. Breezy, with a northwest wind 10 to 20 mph, with gusts as high as 25 mph. ', 'Friday: Sunny, with a high near 79. West northwest wind 10 to 15 mph, with gusts as high as 20 mph. ', 'Friday Night: Mostly clear, with a low around 62. West southwest wind 5 to 15 mph. ', 'Saturday: Sunny, with a high near 83. Southwest wind 5 to 10 mph, with gusts as high as 20 mph. ', 'Saturday Night: A 30 percent chance of showers and thunderstorms after 2am.  Partly cloudy, with a low around 65.', 'Sunday: A 40 percent chance of showers and thunderstorms.  Mostly cloudy, with a high near 81.', 'Sunday Night: A chance of showers and thunderstorms, mainly before 8pm.  Mostly cloudy, with a low around 66.', 'Labor Day: A slight chance of showers and thunderstorms after 2pm.  Partly sunny, with a high near 79.']


### Combining our data into a Pandas Dataframe

We can now combine the data into a Pandas DataFrame and analyze it.

In order to do this, we’ll call the DataFrame class, and pass in each list of items that we have. We pass them in as part of a dictionary. Each dictionary key will become a column in the DataFrame, and each list will become the values in the column:

We can now do some analysis on the data. For example, we can use a regular expression and the Series.str.extract method to pull out the numeric temperature values:

We could then find the mean of all the high and low temperatures:
