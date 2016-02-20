---
layout: post
title: "Creating a Custom UE4 Marketplace Frontend Experiment"
tags: [Allar, ue4, marketplace, launcher]
thumbnail: "UE4-Annoying-Hat.gif"
---

This weekend I woke up late Saturday night and was working on an unrelated web project. This project was giving me some issues that required some HTTPS debugging using a tool called [Fiddler](http://www.telerik.com/fiddler){:target="_blank"}. Fiddler is a great tool for reading HTTPS traffic. During my debugging I caught glimpse of a UE4 ajax marketplace call that pulled down marketplace data as a JSON object. I immediately thought "I bet I could do something with this", and my great annoyance that is the marketplace launcher not having search functionality led me to start a jam session on "could I rebuild the marketplace launcher?" Turns out, you can replicate quite a lot of it easily. <!-- more -->

I hate web development and I thought this would be a fun break from things. Not only did it end up being fun, it ended up being quite educational!

# The Goal

My goal was simple. Create something that resembles the marketplace frontend found in the UE4 launcher but include a search feature. On this journey, search ended up actually being the last thing I implemented on top of many other features I thought would be nice to have.

# First Steps

## The Development Platform

I am a big fan of [Popcorn Time](http://popcorn-time.se/){:target="_blank"} and know that they're basically using a form of Chromium wrapped around node.js. I'm a big fan of node.js. Looking into how this type of development was done, I stumbled upon [nw.js](http://nwjs.io/){:target="_blank"} which turns out to be exactly what I wanted. I saw that others were using the `request` node module to do web requests with nw.js, so I started a blank project and popped that module in. This, combined with [cURL for Windows](http://curl.haxx.se/download.html){:target="_blank"}. I now had a base for sending arbitrary web requests.

## Figuring Out the Authentication Process

From the very beginning I wanted to access the marketplace using my credentials so I can get data for what assets I owned versus not owned. I underestimated how much I didn't know about this process and this step took most of my time.

My first approach was to use Fiddler to capture the login process using the web version of the marketplace and then simply recreate it. I'm not skilled at doing this sort of thing, so I spent a few hours and I gave up. I kept getting '400 Bad Requests' any time I attempted hitting the login endpoint using cURL.

I then tried various ways to sniff web traffic and tried sniffing the actual launcher's traffic. Here I learned the launcher goes through a full OAuth process, whereas logging in from the web only goes through what appears to be a partial OAuth process. I tried abusing my way through the OAuth chain and replicating what the launcher was doing but Fiddler wasn't providing me with the data the launcher was sending to Epic's MCP/webserver, and I'm not skilled enough in reverse engineering to dig deeper here. Setting up a more advanced HTTPS 'man-in-the-middle' proxy server would probably work but the Google results on how to do so were scary. I went back to capturing the web login process.

I found I had two major problems, cookie management and unknown api requirements.

### Battling Cookies

Storing the cookies needed during the login process was a big problem for me, and I still can't figure out how to get Epic's cookie requests to save in nw.js's built-in cookie support. I decided to say 'I don't know what I'm doing, lets accept the fact I have to write some bad code'. With this sin in mind, I simply turned off all cookie and redirect support in nw.js and decided to handle it myself. Any time a web request would want to set a cookie, I just set a key-value pair in a global object. Not the most elegant solution, but hey, I was finally storing cookie data.

Not knowing much about web security or the HTTP protocol, I thought sending these cookies back to Epic and having them accept them would be a pain in the ass if not impossible. Turns out 'cookie-access' rules that I know of, the ones that prevent various 'cookie exploits', are dictated almost solely by browsers. Nw.js isn't really a browser and you can get it to do really whatever you want. The `request` module and cURL allowed easy setting of your 'cookie string' header. Now that I was sending good cookie data back to Epic, its webservers were *much* happier with me.

### Fighting the API

Now that cookie management was solved, I just needed to figure out the right sequence of endpoints and what data goes where to facilitate the login process. Fiddler helped immensely with this and all I had to do was make proper use of what Fiddler was telling me.

I kept my web requests as small as possible and added data to each request one piece at a time, trying to establish all the parameters that Epic's API expects. In this process, I accidently clicked too fast on the Submit button when logging in to the web marketplace and was prompted saying I failed to log in because I have submitted the form twice. Looking at the form data, sure enough there are some hidden `SYNCRONIZE` tokens that got updated every time I loaded the login form. Instead of piecing all this form data together myself, I decided to simply just load in Epic's login form from their web server, rip out the unneeded bits like 'reset password' and 'register', and have that form run my own request instead of submitting to Epic.

### Successful Login

Javascript and node.js made this real easy to do, and once I got my first successful login on the first auth endpoint, the rest was easy. The web marketplace appears to go through an OAuth login process as well after you get your 'Single Sign On (SSO)' cookie and I'm not sure why. I decided to go through this process as well but the OAuth's token authorizing step doesn't return anything. Looking at Fiddler, this was the same during the 'real' web marketplace login, so I am assuming that OAuth logins for the web 'client_id' are blocked. This gives me hope that one day there will be a proper '3rd party' OAuth auth chain so people like me can script up safe and secure ways to use other people's data associated with Epic, such as "do they own my Marketplace asset?"

Here is what my fancy login UI looks like:

[![Login](/images/blog/creating-marketplace/Login.png)](/images/blog/creating-marketplace/Login.png)

# Grabbing the Marketplace Data

Now that I was logging in correctly, it was time to see if pulling down the marketplace data is even feasible. Admittedly this should have been the first thing I did but I sort of rushed into this without thinking and I was anxious to get a successful login working. At this point I added a button that would 'skip login' and go directly to data fetching and I kicked myself a bit. The ability to log in though allows for some great features later though.

## Fiddling with `ajax-get-categories`

If you look at the page source for any web marketplace page using a 'dumb' client such as cURL, you'll see that its extremely light and that most of the asset data is somehow being pulled in by JavaScript. I tried opening up the source JavaScript and uncompressing it to see if I can simply just tap into existing api functions. I spent a few hours looking through this as I found it fascinating and I learned a lot about all the different api calls Epic has set up, but it being compressed it was hard to find exactly what I wanted. During this I also found out that they are using [Handlebars](http://handlebarsjs.com/){:target="_blank"} to generate HTML from templates using javascript. This was a great sign as it means the data probably exists in a form where I can generate my own HTML using my own Handlebar templates.

Looking in Fiddler, there was only one web request that looked like it had anything to do with fetching data. As soon as I inspected it, it was *very* clear that there is definitely a way to get marketplace data as a JSON object. Now I just had to figure out the API.

[![Ajax JSON Jackpot](/images/blog/creating-marketplace/AjaxJackpot.png)](/images/blog/creating-marketplace/AjaxJackpot.png)

## Fetching All Marketplace Data

I tried navigating around the web marketplace a bit more to see if any more api endpoints would show up, but they didn't. It appears everything I could find is going through `ajax-get-categories`. The problem with this endpoint is that it only returns 25 assets at a time, and only for a given category. I tried adding all sorts of parameters I can think of such as `count`, `limit`, `end`, `num`, `length`, `all`, and just trying arbitrary endpoints such as `ajax-get-assets`, but I couldn't get anything to work.

I'm not that great in the javascript world and I knew that fetching all this data asyncronously would be a pain in the ass for me, so I decided that I'll just get all the data all at once. It isn't pretty but I ended up with some ugly api code and a whole lot of bad bad bad global variables that would do exactly what I needed it to do.

In a nutshell, this process is:

1. Call `ajax-get-categories` to get a list of all available categories and how many assets are in each category
1. Keep calling `ajax-get-categories` adjusting the `start` parameter as needed to get all assets for each category
1. Merge all this data together into one giant javascript object
1. Once the number of assets we have match the numbers in Step 1, we know we have all the available data

In the form of some nasty ass code:

``` javascript
api.prototype.getAllAssets = function() {
    global.fetching = true;
    // Grabbing environments will allow us to get a full list of categories
    module.exports.getAssetsInCategory('assets/environments', 0, false, function (json) {
        
        var categoriesLeft = json.categories.length;
        global.categories = json.categories;
                
        // Build Category List
        for (var i = 0; i < json.categories.length; ++i) {
            marketplace[json.categories[i].path] = { name: json.categories[i].name };
            module.exports.getAssetsInCategory(json.categories[i].path, 0, true, function (json, path, finished) { 
                if(finished) {
                    categoriesLeft--;
                    if (categoriesLeft == 0) {
                        global.fetching = false;
                    }
                }
            });
        }		
    });
}
```

You'll see a few bad practices in that snippet alone. The fact that I'm using `module.exports` to reference other functions in the same module is probably an absolute terrible thing. It is not so apparent here, but in `getAssetsInCategory`, I abuse the `global` space pretty badly. I am quite skilled in C++ and UE4 in general, but when it comes to javascript and me, *if it works, it works*. You can look at the full code for this [in api.js](#).

## Manipulating the Marketplace Data

The result of my `api` set of functions I wrote results in a global object that contains all marketplace data. To inspect it, I just logged it to Chrome's console any time it was complete. This turned out to be an extremely powerful way to analyze the marketplace data and pull out what I need and where.

[![Asset Data](/images/blog/creating-marketplace/AssetData.png)](/images/blog/creating-marketplace/AssetData.png)

Getting this into usable HTML form was trivial using Handlebars, which is probably why Epic also uses it. To render a category and all of its assets, I created this Handlebars template. Once the HTML layout was done, popping in the values from the marketplace data was as easy as... riding a bike? If I'm more witty, I could think of a better Handlebar pun.

``` html
	<!-- Template for showing a category and all the assets in said category -->
    <script id="category-template" type="text/x-handlebars-template">
      <div id="{{path}}" class="categorylist jumptarget">
        <h1>{{info.name}}</h1>
        <hr>
        <div class="wrapper">
          <ul>
            {{#each info.assets}}
            <li id="{{this.id}}" class="asset" data-effectivedate={{ISODateToUnix this.effectiveDate}} data-owned={{this.owned}} data-price={{this.priceValue}} data-rating={{#if this.rating}}{{this.rating.average}}{{else}}0{{/if}} data-raters={{#if this.rating}}{{this.rating.count}}{{else}}0{{/if}}>
              <a href="#" onclick="showDetailsFor('{{../path}}','{{this.id}}')"><img class="thumb" src={{this.thumbnail}}></a>
              <span class="title">{{this.title}}</span>
              <span class="author">{{this.seller.name}}</span>
              {{#if this.rating}}
              <span class="raters">{{this.rating.average}}/5 [{{this.rating.count}}]<span class="glyph glyph-star"></span></span>
              {{else}}
              <span class="raters">Unrated</span>
              {{/if}}
              <span class="price">{{#if this.owned}}Owned{{else if this.free}}FREE{{else}}{{this.price}}{{/if}}</span>
            </li>
            {{/each}}
          </ul>
          <br/>
        </div>
      </div><!-- {{category_path}} -->
    </script>
```

# Replicating the Frontend

I won't go into much detail here. Once you have the marketplace data, laying out the frontend is just your average HTML + CSS + javascript/jQuery development. For this project I used [LESS](http://lesscss.org/) to compile my CSS, but most of the work was just looking at Epic's launcher for reference and making CSS that matches. I could have possibly sniffed around and used Epic's actual stylesheets for this but learning to use their compressed versions would have been more work than just writing them from scratch. The design work was already done, aside from a few features I added, so all that remained was grunt work.

# Unexpected Snags

I ran into a few issues I did not expect when manipulating the marketplace data. The three worth mentioning are:

## Asset 'Categories' Data Is Wrong

The first step in getting assets is to ask for the assets in a `category`. These 'top level' `categories` are what all assets must fall into to be shown in the launcher or web marketplaces. Assets themselves have a `categories` property as well, perhaps so that an asset can identify itself as being in two `categories`. What ever the case, the `categories` property within assets is outdated and/or wrong. This means if you were to look up more information about the category an asset belongs to as opposed to looking for assets that belong in a category, you're going to be met with some challenges.

[![Asset Categories Mismatch](/images/blog/creating-marketplace/assetcategorymismatch.png)](/images/blog/creating-marketplace/assetcategorymismatch.png) 

## Asset Descriptions Are Malformed HTML

It seems like the majority, if not all, of marketplace asset descriptions have some basic HTML injected into them. This isn't a problem in itself, infact it is even helpful, but the problem is however these HTML tags are being created, they are being created wrong, which means some HTML fixup is required if you needed to display them properly. To put the issue clearly This malformation results in a good chance that an asset's description has an HTML closing tag that doesn't specifiy an HTML element.

Or simply put, often times `<a href="...">Text</>` is used instead of `<a href="...">Text</a>`. See the difference in closing tags? Trying to render this HTML directly could result in some problems if you don't do any pre-processing of it first.

I don't know the best way to fix this issue, but I wrote a dirty function that seems to do the job.

``` javascript
    // Fix Epic's broken ass malformed closing tags i.e. <a></> instead of <a></a>
    // I wrote this using some really hacky logic and the assumption that jQuery's ".parseHTML"
    // results in a 'good enough DOM' where I can extract the tags I need to close. I don't know if this
    // fixes every case, but it appears to be 'good enough' for now.
    // It also replaces new lines with <br> and adds <hr> to any </h1>
    // It then takes the fixed HTML and makes all links open in a new browser
    $('.fix-html').each(function(index) {
        var fixed = $(this).html();
        fixed = fixed.replace(/&lt;/g, '<');
        fixed = fixed.replace(/&gt;/g, '>');
        var badTagIndex = fixed.indexOf('</>');
        while (badTagIndex != -1) {
            var badDOM = $.parseHTML(fixed.substring(0, badTagIndex));
            var elementTag = $(badDOM).last().get(0).tagName.toLowerCase();
            fixed = fixed.replace('</>', '</' + elementTag + '>')
            badTagIndex = fixed.indexOf('</>');
        }
        fixed = fixed.replace(/(?:\r\n|\r|\n)/g, "<br>"); // Makes newlines pretty
        fixed = fixed.replace(/<\/h1>/g, "</h1><hr>"); // Adds <hr> to <h1> i.e. Contact and Support
        fixed = fixed.replace(/<br><br><h1>/g, "<br><h1>"); // Removes extra newline before <h1>'s
        fixed = fixed.replace(/<hr><br>/g, "<hr>"); // Removes extra newline after <h1>'s        
        $(this).html(fixed);
        
        // All links in these descriptions should open in a new browser
        $(this).find('a').on('click', function(){
            open(this.href);
            return false;
        });
        
    ...
```

## Contact and Support Not Treated As 'Proper' Data

Not the biggest feature, but important to note nonetheless. Have you ever noticed that the Contact and Support sections of asset descriptions are kind of inconsistent and are prone to errors? This is because the Contact and Support data is actually a hacky addition to the same data property that holds 'Technical Details' instead of it being treated as a proper data point for an asset. I'd really like to see 'Contact and Support' data folded into more proper, easier to maintain and read data properties.

On some assets, such as [Crumbling Ruins](https://www.unrealengine.com/marketplace/crumbling-ruins){:target="_blank"} at the time of this writing have extra or duplicate data regarding 'Contact and Support', which is unfortunately visible in the launcher, web, and my custom marketplace frontend.

## Seller Videos as 'Proper' Data

Similar to the Contact and Support snag, I wish seller's videos were stored as proper data instead of having to be parsed from the description texts. Videos are important.

# The Result

You can read more about the end product [here on this blog post](/2015-12-07-Custom-UE4-Marketplace-Frontend) if you're interested into exactly what features exist and how far I got. Most likely you would have read that post before coming to this one though. A binary release you can download and try right now is avaiable in my [repo's releases](https://github.com/Allar/custom-ue4-marketplace-frontend/releases){:target="_blank"}The source code for this project is also [available here on my github](https://github.com/Allar/custom-ue4-marketplace-frontend){:target="_blank"}.

Even if this project means nothing to anyone, it was definitely a fun and worthwhile Sunday for me.