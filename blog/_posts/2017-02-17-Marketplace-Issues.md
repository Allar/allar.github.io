---
layout: post
title: "Opinions on Marketplace Issues"
tags: [Allar, ue4, marketplace]
thumbnail: "UE4-Annoying-Hat.gif"
---

What Is This About?
========

On September 3rd, 2014, the UE4 Marketplace was launched. I released
my first asset on the marketplace on June 30th, 2015.

A lot of people come to me for advice about selling on the marketplace,
as well as my thoughts and opinions on the marketplace as a whole. I
have heavily experimented with the marketplace as a seller, a buyer, and
an overall annoying member of the Unreal community.

I decided to post my general concerns on my blog to hopefully spark some
discussion, as well as having a page to link to when someone asks me my
thoughts instead of reiterating my opinions.

I am an outsider of Epic and there could be things in this writing that
are just plain wrong or are terrible ideas. If so, please reach out to
me and I will edit accordingly.

I love the marketplace. I love being a seller. However, a big part of me
feels like the marketplace is ‘one of those random small teams’ at Epic
and that the marketplace is one of Epic’s lower priorities. The
marketplace staff is great, but appear to be resource deprived.

My issues and concerns with the marketplace are more geared toward the
backend, not the frontend. A better and more automated backend would
allow the support staff to help out sellers in better and faster ways. A
better backend would also greatly improve the marketplace seller portal.

An additional benefit of a better backend would be the support for third
party marketplace tools. I have already created a system that tracks
marketplace assets per user and reports the historical rating history of
all tracked assets, so they can be notified when their asset rating
changes. This was a proof of concept and much more can be learned about
my third party marketplace proposal here:

[Third-Party Marketplace Support Proposal](/assets/ThirdpartyMarketplaceSupportProposal.pdf)

It is also possible for a third party to create their own frontend for
the marketplace, which could potentially offer some very nice
integrations with seller’s own websites. Because a third party frontend
could be made, I believe this merits some consideration in allowing the
public to submit pull requests for the marketplace view of the launcher
without giving the public access to any critical or secure API
endpoints. You can read more about this at:

[Creating A Custom Marketplace Frontend](/2015/12/07/Creating-Custom-Marketplace-Frontend/)

[Custom Marketplace Frontend](/2015/12/07/Custom-UE4-Marketplace-Frontend)

And while on the subject of securing the marketplace API, I will link my
investigations into various holes of the API I wrote some time ago. This
document is now out of date and some issues have been fixed, but there
are other ways to circumvent/exploit some of these API calls further.

[Building a Free Marketplace Through Crowdsourcing](/assets/FreeMarketplace.pdf)

The following opinions are offered in no particular order:

Two Search Boxes
================

![Two Search Boxes](images/mp/double-search.png)

Currently on the UE4 Marketplace webpage, there exists two types of
search. The topmost one searches ‘the Unreal universe’ for everything
except marketplace info.

The bottom one searches only for marketplace info. It is fairly easy to
accidentally use the wrong search here. Instead, having just one search
box and folding in marketplace results into the ‘everything Unreal’
search would be preferred.

Adding an advanced search dropdown with filters would go a long way here
to not just improve the usefulness of the header search, but would allow
the marketplace page to default to a ‘marketplace filter’. This would
also improve the discoverability of marketplace assets when users search
for things as they might find a marketplace asset more useful than a
forum post of info.

Exact Searching Only
====================

All Marketplace searching is done by exact searching only.

This really should be done with [fuzzy searching or approximate string
matching](https://en.wikipedia.org/wiki/Approximate_string_matching). This is the technical term for what the community wants.

Searching for ‘particle’ and for ‘particles’ should not lead to
different and impartial results. For example, this pack:

[https://www.unrealengine.com/marketplace/ft-magic-pack-01](https://www.unrealengine.com/marketplace/ft-magic-pack-01)

Will not show up when using the search term ‘particles’ because its
description uses the words ‘particle effects’ instead of particles.

No Seller Searching
===================

Users have no way of searching directly for a marketplace seller.
Instead they have to find an asset sold by their desired seller, then go
to their seller’s page which isn’t accessible in any other way.

Seller Page Comment Boxes
=========================

Many sellers don’t even know people can leave comments on their seller
pages.

Asset Images – Sizing And Cropping
==================================

![Sizing and Cropping Issues](images/mp/cropping.jpg)

Even if a seller follows submission guidelines for images exactly as
written, it is not guaranteed the Marketplace will show the right image
for the right location, and it’s not guaranteed the image will be shown
at the required size either. The submission guidelines for images are
out of date and are meant for a much older feature and asset display
layout.

Image requirements and guidelines should change so that they properly
represent what customers will see. If the layout is going to stick to a
responsive fluid sizing for featured items, .PSD and .PNG templates
should be given to sellers to show exactly how to create their images to
avoid these issues. These templates should include things like title
safe areas, cropping guidelines, etc.

This issue makes the sellers look less professional and thus lowers the percieved quality of the marketplace as a whole.

Asset Images – More Sizing Issues
=================================

![More Sizing Issues](images/mp/bandwith.png)

The preview images on an asset’s page are also laid out in a responsive
way, causing image cut offs and other scaling issues. There is very
little respect for aspect ratios for these image previews.

The scrolling thumbnail list has all of its images at the wrong aspect
ratio, regardless of the size of the browser viewport. This can make
some seller art that is designed to look good as a thumbnail look
horrid.

The ‘big’ preview image is also not aspect ratio constrained, however
this is less of an issue as with this layout it would be much harder to
keep the original aspect ratio. However, one huge improvement is that
the whole ‘Buy’ button area can be more responsive than it currently is.
When the viewport shrinks to the point where the preview image far
exceeds the its original width aspect ratio, the Supported Platforms,
Supported Engine Versions, Share, and Buy button can become vertically
stacked in one column instead of two. This would potentially allow the
block to become less tall and to be moved up a bit, so that the ‘big’
preview image doesn’t need to be as tall, thus better preserving the
aspect ratio.

This issue also makes the sellers look less professional and thus lowers the percieved quality of the marketplace as a whole.

Asset Images – Crazy Bandwidth
==============================

![Bandwidth Issues](images/mp/bandwith.png)

Every single asset image here is actually the full 1920x1080 image being
scaled down. This greatly slows down page load speeds and puts more load
on the web servers. When a seller submits a marketplace image, your
backend should automatically generate thumbnails of those images for
displaying in situations like this. Users often download far more image
data than they ever need. This is even more significant in the already
resource hogging launcher, where simply browsing the marketplace or even
accidentally loading the launcher can be painfully slow or in some cases
incredibly expensive on metered connections.

After browsing 10 assets, without even flipping through any images, I
quickly went through 50MB of data. This is simply ridiculous.

No HTTP/2 Support
=================

Because the marketplace frontend serving web server does not support
HTTP/2, which currently every major browser including mobile supports,
even more bandwidth is being consumed as well as webserver resources.
The marketplace team removed real-time sales data with the reason of
‘the servers couldn’t keep up’, which is infuriating because it seems
that there is no effort to rectify this.

Moving to HTTP/2 would mean that users do not have to issue a request
for every single JavaScript file and web image. The number of requests
needed per marketplace page load is huge.

Depending on how the launcher was created, it could also be possible to
switch to HTTP/2 as well. This would be a factor in helping remove the
launcher’s ‘slugishness’.

Hide or At Least Obfuscate Seller Contact Info
==============================================

![Email Addresses](images/mp/plain-email.png)

Marketplace seller emails are listed in an easily parsed form. This
means marketplace seller emails are subject to spam bots that work by
bots that scrape webpages for email addresses.

Hiding them completely for users who do not own the item is one
suggestion from the community. Alternatively, another suggestion is
obfuscating them if you don’t own them. The simple act of writing the
shown email above as ‘tihomirvfx at gmail dot com’ cuts down on spam
bots massively.

Painful Navigation
==================

![Category Navigation](images/mp/categories.png)

There are a lot of marketplace assets, which is great. Having categories
is great. The UX problem with categories and marketplace navigation is
that it is hard to go back to them if you’re several pages deep within
the marketplace. A sticky header would be a very welcome addition.

![Launcher](images/mp/mp-launcher-header.png)

The launcher already has an excellent header on each of its marketplace
pages. It just needs to be ‘sticky’ and not scroll with the page.

The Vault can get easily out of hand with a large number of assets.
Categories for the vault would also be helpful.

![Library](images/mp/library.png)

The Most Annoying Back Button
=============================

![Back Button](images/mp/back.png)

When browsing the marketplace tab in the launcher, you are given this
‘Back’ button on any asset page. This is **the only time a Back button
is displayed** and it should **always** go back a page in the
marketplace. Instead, this ‘Back’ button provokes a launcher wide back
action and makes navigation infuriating. For example, say you are
looking at an asset but want to check out your library to see if you
already have something similar. You decide you do so you go back to the
marketplace section. Remember there is no ‘Back’ button unless you are
on the Marketplace tab, so you have to click the Marketplace tab on the
left. You then click the back button to go back to a category or search
page, but suddenly you end up right back in your library. This behavior
even exists across major tabs.

[Click here for a video example of this painful behavior.](images/mp/back-video.mp4)

No Shopping Cart
================

Currently there is no shopping cart feature of the marketplace. I’m
aware one is coming soon however after a year of constant requests.

Honestly I don’t care about having a shopping cart, but the community
would yell at me if I didn’t bring it up. Someone did bring up a good
point that is absolutely worth mentioning however:

If you are not American and are buying assets in something other than
USD, buying assets one at a time is more expensive due to currency
conversion fees. For example, if you’re buying two things for 5 EUR,
your bank / payment processor will charge you 5 EUR + fee + 5 EUR + fee,
as opposed to 10 EUR + fee.

Filters, Listings, Top Charts, All Missing
==========================================

Features like being able to filter for price, ratings and engine
versions, sub categories and more advanced listing views, and top charts
are all things that people expect from a marketplace.

These things do not inhibit discovery, they increase it. When a user has
to go through a bunch of assets they aren’t interested in for either
price constraints, low ratings, file size limits, or simply just too
old, it prevents the assets they would buy from being easily visible and
no one wins.

Top charts reward sellers with top tier content with more visibility,
promotes competition based on quality, and does not freeze out new
sellers if done properly. Top charts should be time constrained, i.e.
Past Week, so that a product with a really fantastic launch but zero
sustained sales doesn’t lock a place in the chart.

Top Paid and Top Grossing also improve the perceived quality of assets
across the entire marketplace. Higher quality assets naturally sell
better, and if they are listed on top lists, people can easily find the
more premium assets instead of rummaging, buying, regretting, and
telling others how they got a bad asset and no longer trust quality of
the marketplace.

Literally every marketplace that has achieved maturity has these
features. The Epic Marketplace has shown very little perceived growth in
terms of systems and infrastructure. This would be a huge correction to
that perception. Only a momentary glance at the forums is enough to know
how people are frustrated with the lack of marketplace development.

Guidelines Inconsistent And Sometimes Just Wrong
================================================

First, there are two separate set of guidelines:

1.  [The Main Web Portal Guidelines](https://publish.unrealengine.com/submission-guidelines)

2.  [Forum Submission Guidelines](https://forums.unrealengine.com/showthread.php?116797-Marketplace-Submission-Guidelines)

While similar, these guidelines are not identical. There should only be
one master set of guidelines.

The guidelines are often vague or very specific, and then their
enforcement is also variable. This leads the community to think the
marketplace staff is not a cohesive team but a group of people who clock
in and clock out and do what they want. Every bad asset that gets
through that clearly violates the guidelines and every vague rejection
notice that doesn’t cite anything specific amplifies this problem. Epic
staff often replies ‘thanks for your feedback’ and ‘we are making
changes to address concerns’ but here is an example of a guideline and
submission issue that’s been around for literally years now.

![Guideline Error](images/mp/error.png)
