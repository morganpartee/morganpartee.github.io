---
layout: post
title: "Reverse Engineering a Cat Feeder to Boost Productivity"
date: 2022-10-08
description: "After some late-night Amazon'ing I got a PetKit FreshElement Solo, which I turned into a positive reinforcement machine."
author: "John"
original_slug: cat-feeder
---

![overview](https://web.archive.org/web/2023/https://www.sensibledefaults.io/static/images/feeder/idea.jpg)

I've been laughing about it this weekend and had to write it up - After some late-night Amazon'ing I got a PetKit FreshElement Solo. I had two problems it could solve: low side project motivation, and loving [Target's Dark Chocolate Sea Salt Almonds](https://www.target.com/p/himalayan-salted-dark-chocolate-almonds-37oz-good-38-gather-8482/-/A-81418159) way too much. I'm a codemonkey - Why not feed my monkey brain when I push code?

I couldn't find a USB candy dispenser (I even checked thinkgeek, nothing close!), so I figured I'd try a cat feeder. This thing rocks, and I'll show you how to replicate what I did.

Edit: Holy crap, this blew up. Thanks everybody for reading! Like I said on Reddit, if I motivated even one person to write some code to do something dumb, it was worth the time. If you're learning Python (Or work in ML/Cloud stuff):

- [Follow Me On Twitter!](https://twitter.com/_JohnPartee?s=20&t=hVajnrlA4otHIAyu81VRjQ)
- join us [Here on LinkedIn](https://www.linkedin.com/groups/12623715/), where I've got a few folks working through a lesson plan I developed from how I learned!

## Teaser

Code: [`https://github.com/morganpartee/pyPetKit/blob/master/run.py`](https://github.com/morganpartee/pyPetKit/blob/master/run.py)

To run yourself, you'll have to set your username and password here: [`https://github.com/morganpartee/pyPetKit/blob/master/settings.py`](https://github.com/morganpartee/pyPetKit/blob/master/settings.py)

## An Overview

REST API's are basically the language of the internet. Most products use them to talk from devices to servers. I saw some old Python code that had the authentication flow worked out already, so I had somewhere to start!

I snooped for the API request that did a manual feeding, replicated that in Python with Requests, and tied that script to a button on my streamdeck, which also sends a `ctl+enter` to send the commit!

There are other (better) ways to do this - AWS, webhooks, git hooks (git hooks probably being the best bet), but I wanted this to work locally only, so I'm not dispensing candy while I'm out of town. No need to overcomplicate! Worst case I forget to use the button and eat less candy.

## The Hardware

I really can't overstate - This thing is perfect for dispensing snacks. The food is portioned by this neat little silicone auger system, with silicone fingers that limit the amount that can pass into the dispenser. It's clever, and gentle on the food. And food safe!

![Auger Top View](https://web.archive.org/web/2023/https://www.sensibledefaults.io/static/images/feeder/auger-down.jpg)

And this was just cute:

![Cute](https://web.archive.org/web/2023/https://www.sensibledefaults.io/static/images/feeder/neat.jpg)

They include a sizing chart for food that says it should be under 12mm:

![Sizing](https://web.archive.org/web/2023/https://www.sensibledefaults.io/static/images/feeder/sizer.jpg)

But it filters and portions food like:

[via GIPHY](https://giphy.com/gifs/77wE44ZtsIhZAT2ma0)

So if it is 12mm tall sideways, it'll work fine. I plan to test jerky chunks and cheez balls in the future. It includes a desiccant packet in the lid to keep stuff fresh... So I expect it's pretty safe to get wild.

As of writing they're about $70, but they work great and are built well.

## App

![Breed Picker](https://web.archive.org/web/2023/https://www.sensibledefaults.io/static/images/feeder/app.png)

But after that I was able to use the API. After testing the serving size with the app a few times! 1/10th of a cup is one half of the rotor - Which averages out to about five almonds.

## Snooping

I used [Packet Capture for Android](https://play.google.com/store/apps/details?id=app.greyshirts.sslcapture&hl=en_US&gl=US) while I ran a manual feeding:

![Packet](https://web.archive.org/web/2023/https://www.sensibledefaults.io/static/images/feeder/feed.png)

And found this request:

![Packet](https://web.archive.org/web/2023/https://www.sensibledefaults.io/static/images/feeder/packet.png)

There's no real silver bullet here, I had to look through a few groups of requests to find the right one. But we can see it hits the `/latest/d4/saveDailyFeed` endpoint, which gives us a big hint! The `URLENCODED` section tells us that `amount`, `time`, and `deviceId` are included in the URL... So we can replicate it locally!

Andy and I have covered how to do this on desktop [here]({% post_url 2021-03-10-dont-scrape %}).

## Coding

Code is available [here!](https://github.com/morganpartee/pyPetKit)

I started with [`geeks4hire`'s fork of PyPetKit](https://github.com/geeks4hire/pyPetKit). It had the auth flow worked out for me, I just had to add a way to hit the API. I knew the endpoint I had to hit from the packet above, so I added a generic function to send requests to the API to my fork of the package:

```python
 def send_api_request(self, path, method="POST", params=None, json=None):
        """
        Sends an API request.
        """
        custom_headers = {
            "X-Session": self._access_token,
            "User-Agent": "PETKIT/7.26.1 (iPhone; iOS 14.7.1; Scale/3.00)",
            "X-Timezone": f"{round(self._tzone._utcoffset.seconds/60/60)}.0",
            "X-Api-Version": "7.26.1",
            "X-Img-Version": "1",
            "X-TimezoneId": self._tzone.zone,
            "X-Client": "ios(14.7.1;iPhone13,4)",
            "X-Locale": self._locale.replace("-", "_"),
        }

        return requests.request(
            method,
            self._apiServerBaseURL + path,
            headers=custom_headers,
            params=params,
            json=json,
        ).json()
```

This looks complicated, but the headers were copied from someone doing exactly what I did from an iphone. This uses requests (`requests.request`) to make an HTTP request, with whatever method. This is exactly what a browser does, but with Python! We cover this in detail in the scraping talk mentioned above - If you can copy the headers, you can do a lot!

Then getting those sweet sweet almonds is as easy as:

```python
from pypetkit import PetKitAPI
from settings import (
    API_USERNAME,
    API_PASSWORD,
    API_COUNTRY_CODE,
    API_LOCALE_CODE,
    API_TIMEZONE,
)
from pprint import pprint

petkit_api = PetKitAPI(
    API_USERNAME, API_PASSWORD, API_COUNTRY_CODE, API_LOCALE_CODE, API_TIMEZONE
)

#! Sign in
petkit_api.request_token()

print(f"Authorized: {petkit_api.is_authorized}")

#! Send the actual feeding request
pprint(
    petkit_api.send_api_request(
        "d4/saveDailyFeed", params={"deviceId": 10019856, "amount": 10, "time": -1}
    )
)
```

We leverage the existing code's authentication flow, then send our request. The parameters match the request we found when we did a manual feeding from the app! I did try a smaller serving size, but 10 seems to be the smallest amount it can dispense. The `-1` seems to make it feed immediately. You can schedule feedings in the app, I'd bet you can do that with dates too.

If it works, you'll see an output like this from `run.py`:

![It works!](https://web.archive.org/web/2023/https://www.sensibledefaults.io/static/images/feeder/works.png)

And a little beep, followed by the beautiful sound of a reward hitting your *(don't think about it too much, it's only slightly degrading)* bowl:

[via GIPHY](https://giphy.com/gifs/ZhB7JUPMlTsdWnqIi0)

## "Deploying"

Deploying is a strong word, but that's what I'd call it. I'm running this locally so I don't dispense food when I'm away from home. I have an Elgato Stream Deck that I love, that I used to make the hotkey.

![Hotkey](https://web.archive.org/web/2023/https://www.sensibledefaults.io/static/images/feeder/hotkey.png)

The `System:Hotkey` sends `Ctl+Enter` to send the commit when I'm done writing the message. The `System:Open` key opens `python c:/<path>/run.py`, which feeds me sweet chocolatey reinforcement for my good coding habits!

You could 100% do this "better", but there's no need for cloud stuff or whatever for a goofy little project. It's just part of the fun!

## Conclusion

Huge thanks to everyone that had reverse-engineered this gear before me. All that was left was finding the endpoint and making the request, which was too easy! Who knows if it'll be a real coding reinforcement, but I hadn't seen someone do it before, and I'm glad I gave it a shot. Troubleshooting CI problems has gotten a lot sweeter.

## Security Note

You'll see that the data for these products is sent in plaintext to and from their servers... No HTTPS... That's just sketchy. They send your complete location too, which is further sketchy. I'd avoid using the app as much as you can, and use a one time password for when it gets breached... Because it really might happen at some point.

## Resources

[PETKIT Fresh Element Solo](https://amzn.to/3ecgCF7)

[Github Repo](https://github.com/morganpartee/pyPetKit)

[Packet Capture for Android](https://play.google.com/store/apps/details?id=app.greyshirts.sslcapture&hl=en_US&gl=US)

Elgato Stream Deck

[Target's Dark Chocolate Sea Salt Almonds](https://www.target.com/p/himalayan-salted-dark-chocolate-almonds-37oz-good-38-gather-8482/-/A-81418159) (Seriously, they rock)

## Follow Me

[On Twitter!](https://twitter.com/_JohnPartee?s=20&t=hVajnrlA4otHIAyu81VRjQ)

[On LinkedIn](https://www.linkedin.com/in/johnpartee)

[On Github](https://github.com/morganpartee/)
