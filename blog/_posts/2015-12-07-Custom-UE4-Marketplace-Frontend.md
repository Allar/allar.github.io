---
layout: post
title: "Custom UE4 Marketplace Frontend"
tags: [Allar, ue4, marketplace, launcher]
thumbnail: "UE4-Annoying-Hat.gif"
---

After finding out accessing UE4 Marketplace data was possible, I made a pretty cool 'custom UE4 Marketplace frontend' that mimics the launcher version of the UE4 Marketplace but with some additions I think many people would like such as searching, sorting, and seller YouTube embedding. If you would like to download it and try it for yourself, grab it from [my repo's releases page](#). [Full source code is available here on my GitHub](#). Read below to find out everything that it does (and doesn't) do! <!-- more -->

# What Is This And How Do I Get It?

This is a standalone desktop app made with [nw.js](http://nwjs.io/) and is not meant as a replacement or patch to the UE4 launcher's marketplace.

You can download it and try it for yourself [here on my repo's releases page](#).

Its full source is available [here on my GitHub](#) so it can serve as a great beginning or reference if you wanted to create your own custom UE4 Marketplace frontend.

# Why Was This Made?

Doing things like this on a Sunday and tackling projects I have no business in is what I call 'relaxing'. This project has no purpose other than I wanted to see if I can do it.

There are also a lot of requests for things to be added to the marketplace launcher, such as the infamous request for adding 'search' behavior. I wanted to focus on knowing what it would take to add these features, so I've built them.

# New Features

## Price and Rating Overlays

The first thing that is immediately obvious is asset thumbnails now have their prices and rating information overlayed. Overlaying the price saves space, makes it match the web marketplace, and is easier to read. The rating information has never been shown in the launcher before and for 'power buyers', this is really important information.

[![Asset Thumbnails](/images/blog/creating-marketplace/AssetThumbnails.png)](/images/blog/creating-marketplace/AssetThumbnails.png) 

## Asset Sorting

You can now choose different ways to sort assets in the marketplace within their categories! The Epic launcher only does sorting by "Most Recent" and nothing else. I've added "Price, Name, and Ratings".

[![Asset Sorting](/images/blog/creating-marketplace/sorting.gif)](/images/blog/creating-marketplace/sorting.gif)

## Ownership Filtering

The Epic launcher and web marketplaces have no way of filtering owned vs. unowned items, aside from 'the vault'. I've added a really easy way to filter this information. You can show all assets, assets you do not own, or the assets you do own. Note: This feature requires logging in, otherwise nothing will be listed as owned.

[![Ownership Filtering](/images/blog/creating-marketplace/ownership.gif)](/images/blog/creating-marketplace/ownership.gif)

## Searching

That's right folks, searching! I've added 'real-time interactive' searching, which means what is shown reflects what you are typing in the search box. This is faster than using the search on the web marketplace, and non-existent on the launcher despite the large request for it.

You can search asset names, seller names, or both.

[![Searching](/images/blog/creating-marketplace/ownership.gif)](/images/blog/creating-marketplace/searching.gif)

## Sticky Header

If you've ever tried moving from one category to another using the launcher marketplace, you'll know that it can be pretty annoying to go to a specific category without scrolling back to the top. I added a sticky header that puts your navigation, search, and filters within reach no matter where on the list you are.

[![Sticky Header](/images/blog/creating-marketplace/stickyheader.gif)](/images/blog/creating-marketplace/stickyheader.gif)

## On Sale Category

The web marketplace has this but the launcher doesn't. During the most recent sale (Cyber Monday at the time of this writing), 10 of the best selling assets were discounted but the launcher didn't show any sign that anything special was happening. If there ever is a sale and assets are marked as On Sale, they'll now show up in this very obvious category. Currently nothing is on sale so this category doesn't show up.

[![On Sale](/images/blog/creating-marketplace/OnSale.png)](/images/blog/creating-marketplace/OnSale.png)

## Seller YouTube Embedding

If you pull up details for any asset where a YouTube video link is mentioned in the asset's description, all mentioned YouTube videos will be added to the screenshot gallery. This gives you the ability to see what an asset is truly about instantly!

[![On Sale](/images/blog/creating-marketplace/youtube.gif)](/images/blog/creating-marketplace/youtube.gif)

## Instant Seller Searching

Often times when looking at assets on the marketplace, one of my considerations for purchasing is "what else have they made?". You can now get an instant view of what a seller has made by clicking on their name.

[![Seller Searching](/images/blog/creating-marketplace/sellersearch.gif)](/images/blog/creating-marketplace/sellersearch.gif)

## Asset Ratings in Detailed View

When you view the details of an asset in the launcher marketplace, no review data is listed at all. I've added this data to the overlays mentioned above as well as in the asset brief when viewing an asset's details.

[![Rating In Details](/images/blog/creating-marketplace/RatingInDetails.png)](/images/blog/creating-marketplace/RatingInDetails.png)

## Contact and Support Now Has a Line

Not the biggest feature, but important to note nonetheless. Have you ever noticed that the Contact and Support sections of asset descriptions are kind of inconsistent and are prone to errors? This is because the Contact and Support data is actually a hacky addition to the same data property that holds 'Technical Details' instead of it being treated as a proper data point for an asset. I'd really like to see 'Contact and Support' data folded into more proper, easier to support data properties.

All I really did here was make sure that there is a horizontal line after the 'Contact and Support' heading to make it more consistent, but I can't fix every issue with this information. On some assets, such as [Crumbling Ruins](https://www.unrealengine.com/marketplace/crumbling-ruins) at the time of this writing have extra or duplicate data regarding 'Contact and Support', which is even visible in the launcher and web marketplaces.

[![Contact and Support](/images/blog/creating-marketplace/ContactAndSupportIssue.png)](/images/blog/creating-marketplace/ContactAndSupportIssue.png)

# Missing Features

There are few features I am missing in my custom frontend. Some are feasible to add in a custom frontend, some aren't.

## Asset Comments

I saw enough parts of the API on reading, posting, editing, deleting, and voting of comments in the web JavaScript to know that full commenting functionality should definitely be feasible in a custom frontend.

## Asset Purchasing

From all the marketplace API I hunted through, it looks like being able to buy assets using a custom frontend *might* be possible. There are various ajax calls on the web marketplace that facilitate it, but I haven't looked into if there are any hidden requirements. I'm confident that this would be feasible though.

## Using Assets

I believe the downloading of assets can only be done with a successful OAuth authentication among other data that only the Launcher has. I could be wrong, and I hope I am, but I didn't come across anything that would facilitate downloading assets on the web marketplace at all.

# Community Involvement

If you believe some of these features should make it into the launcher version of the marketplace, please let Epic know! If you want to try adding a feature to my custom frontend yourself, please fork and do so!