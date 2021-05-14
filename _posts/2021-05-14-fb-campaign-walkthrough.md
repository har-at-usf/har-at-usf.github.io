---
layout: post
title: 'She\'s just not into you... or real: Analyzing a Facebook-based cyber campaign'
date: 2021-05-14 19:08:00 -0400
categories: utm social engineering media facebook 
---

The phenomenon of "men who interact with fake, sexualized content" on
the internet very prevalent. [The techniques used in spam are often used
in
cyberattacks](https://www.newsweek.com/hackers-use-fake-profiles-attractive-women-facebook-spread-viruses-814293).
So, this guide will treat this campaign as though it were an actual
cyberattack. Note that terms like "attacker" refer to the "spammer," and
"victim" refers to "the user who is talking to a fake." (Do not let the
user be *you.*)

Ever wanted to know what happens when you *do* interact with one of
these fakes? This post will walk you through a fake profile, its
suspicious links, and problems that occur along the way. The analysis
will trace the behaviors (the "campaign") which starts with a fake
profile. This is just "one way" to prove that a profile is a fake.

Because the profile sports sexually explicit content of a model, the
page has over *one-thousand followers* despite it being completely fake.
At nearly every step of the campaign, the attacker will gain information
about their victim. Their goal is to get the user to willingly give away
their own information.

By the end of this, you should have some idea of how to trace a fake
profile. Note that you should *seldom* (if ever) need to do this on your
own device. There are many "red flags" that signify a fake profile. The
reader is encouraged to learn those and identify them on their own. This
analysis takes place in a controlled, temporary environment, one
designed exclusively for purposes like this.

Note that this analysis will focus on a "spam" page, not a known
malicious one (like the one that took down the East Coast gas pipeline).
Still, do not perform these in an insecure environment or with little
knowledge of what you are doing. That is a very easy way to fall victim
to a real attack.

# Setup

My setup is a Virtual Machine running a lightweight, security-mindful
distro. It is a throwaway environment and will be deleted after this
demo.

![](/assets/2021-05-14/test-setup.PNG)

*Author's note: This guide shows the domain names for several websites.
Please do not follow those directly. I cannot guarantee that they will
not mess up your system. I have done this work so you don't have to.*

# Tactics (the goal)

In short, the attacker wants the user to give up their own information.
They will use a variety of techniques to try and accomplish this.

# Techniques (how they do it)

This fake profile campaign uses a variety of techniques to get user
engagement. The most dominant technique used is "[social
engineering](https://usa.kaspersky.com/resource-center/definitions/what-is-social-engineering)."
In short, this is getting users to act against their own best interests.
This campaign specifically uses the [promise of
sex](https://www.bleepingcomputer.com/news/security/malware-spread-as-nude-extortion-pics-of-friends-girlfriend/)
to gain a following of users (friends or followers) on Facebook. This
following will become their pool of victims later on.

The attacker who manages the profile then encourages users to click
links that arrive at a spam website (one that, again, promises sex).
Once there, users are prompted to provide personal information, like
their name and email address. Presumably, it would also ask for payment
information later on. (This walkthrough does not cover this because that
is a bad idea.) In a cyberattack, this could link to malicious content,
including a [phishing
attack](https://www.consumer.ftc.gov/articles/how-recognize-and-avoid-phishing-scams)
that steals information or downloads
[malware](https://www.mcafee.com/en-us/antivirus/malware.html).

To perform its initial social engineering, the attacker also uses a
gallery of provocative pictures from the same model. This should raise
some concerns for one of two reasons. Either the model is working with
the spam/scam company. Or, she is completely unaware that her photos are
being used. (If the person photographed finds this guide, please reach
out for more information about this fake or this guide.)

"Middle pages" are used to hide the eventual target: a spam site, which
compels users to enter their information. This technique of hiding the
eventual target is dangerous; if this were malware (virus, Trojan, or
phishing scam), a user would not be able to know that the target is
malicious. They would only see, for example, a shortened link.

[UTM data](https://support.google.com/analytics/answer/1033863?hl=en) is
used later to notify the attacker that the target website was visited
through a Facebook link. This lets the attacker know that their Facebook
campaign is working. In the future, they are more likely to use Facebook
for other exploits.

Finally, the login page attempts to get the victim's personal
information. A full name and email address will allow the attacker to
spam their inbox. In addition, nothing will stop the attacker from
giving away the victim's information to other organizations, websites,
or even *real attackers.*

With these techniques in mind, the attacker's campaign can begin.

# Procedures (the campaign)

The procedures used to accomplish their attack can be summarized like
so:

1.  Use a fake Facebook profile for reconnaissance on users' wants and
    information.
2.  Entice a victim to follow a series of links and intermediary pages.
3.  Prompt the user to give contact information (and, presumably,
    payment information).

## Fake profile

The purpose of the a profile (or fake email) is for the attacker to
appear legitimate and gain either a specific victim or a group of
victims. Later, the attacker will use this false legitimacy to trick
their victims into acting against their own interest. In this case, the
attacker behind the account wants the user to give up their personal
information.

Fake profiles show up in Facebook's "People you may know" feed all the
time. This happens when a friend "likes" or "friend-requests" such a
page. This one is recent, and the inspiration for writing this post:

![](/assets/2021-05-14/dana-profile.PNG)

Dana has *over one-thousand followers* (1,331 at the time of writing)
and 2,022 friends. Most of her photographs violate Facebook's own
position on content that includes nudity. However, she has been active
since December of 2020. Her followers, it seems, absolutely love her.

Sadly for them, Dana likely isn't the woman in the photograph.

One unsettling aside: the same woman appears in each post. Often, fake
profiles use only one photo, or they use a collection of different
women.

![](/assets/2021-05-14/dana-likes.PNG)

If this were a public persona (for example, a celebrity or a well-known
pornographic actress), it might make sense why. A public person's photos
are readily available. However, these photographs show up neither in a
reverse-image Tineye or Google Image search. (The reader can find this
account and try it themselves.) This means that the actress is either
directly involved in the operation, or has no idea that her photographs
have been compromised. The important takeaway is that "no image results"
on a reverse-search engine does *not* mean this account is authentic.

## Malicious links

Dana uses two different sets of links. She wants her followers to click
either one. Both links lead to the same page.

The **v.ht** link is used in older posts. This service is a link
shortener. This is a common tactic for an attacker to hide a malicious
link (for example, to a page that contains a virus, or a phishing
attack).

![](/assets/2021-05-14/dana-link-shortened.PNG)

The other link is for a service called **msto.me**. There isn't much
information available about this site. This fact, in itself, should give
pause for concern.

One thing to note is that, on some websites, the link you "see" will
*not* match the target URL. You can check a link's properties by
right-clicking it and selecting **Inspect** or **Inspect Element**. A
popup like the one below appears with the link's text and target (`href`
property):

![](/assets/2021-05-14/dana-inspect-msto.PNG)

In this case, both links match "what we see." Here's how to prove it.

Using a URL decoder, we can observe that both links are actually valid.
Facebook encodes the URL in order to use with its "external
link-handling service." We can view the target URL using any URL
decoder.

![](/assets/2021-05-14/urldecode-vht-encoded.PNG)

The **v.ht** URL is the value of the **u** variable:

![](/assets/2021-05-14/urldecode-vht.PNG)

Likewise, the **msto.me** link:

![](/assets/2021-05-14/urldecode-msto.PNG)

The problem is, of course, that the link itself might *lead* to a
malicious page. On a normal setup, this is where I say, "DON'T CLICK THE
LINK!" Since this guide is in a virtual machine, we can follow the link
without too much fear about the main system suffering. (As a note,
virtual machines are not necessarily secure for tasks like this. There
are well-known ways to detect and exploit a VM. This is why the VM will
be deleted afterward.)

At this point, copy and paste the URLs into a text editor (like
notepad), log out of Facebook, and clear the browser's cache. This one
is set to delete all information on close, like a Private/Incognito
window. This is to ensure that, if the websites have unwanted behaviors,
they cannot lead in any way to the Facebook account.

## "Middle" pages

A malicious link might use a landing page between the link and its
target. This can sometimes make the page "seem harmless" until it
finishes loading the target (malicious) page.

Dana uses two such pages, between the two links noted earlier, both of
which load the same website.

The **v.ht** page immediately tries to load the target. It does so
automatically, even with scripts enabled, and shows only a "loading"
message in the time it takes to process. The browser does not catch any
suspicious activity because **v.ht** is just a link-shortening service.

![](/assets/2021-05-14/vht-loading.PNG)

The **msto.me** link is different. As a side note, the NoScript addon
prevents any content from loading at first. This is a *good thing*. If a
virus or phishing scam relies on links to scripts to exploit a system,
it will not be able to work. (Blocking scripts does not always block
malware, though.)

With scripts enabled, the page loads. It shows two buttons, both of
which are somewhat misleading. The phone/WhatsApp icon seems to give the
page legitimacy because it is a familiar icon. The page even offers two
buttons for direct contact with the (fake) user:

![](/assets/2021-05-14/msto-landingpage.PNG)

Both buttons' URLS redirect to an external page. They are not real links
within the **msto.me** website. We can inspect them for more detail:

![](/assets/2021-05-14/msto-links-analyzed.PNG)

Finally, clicking either button directs to the same target page: another
"middle page" called **giveladieslove.com**. In order to keep this
appropriate for all audiences, the full webpage will not be displayed.
(Also, the author recommends not visiting this site.) Instead, we will
analyze some behaviors.

## UTM Campaign

First, the URL with scripts disabled reveals some information:
specifically, that we arrived on this site through the Facebook campaign
(`utm_campaign=FB2`).

![](/assets/2021-05-14/giveladieslove-msto-has_fb_campaign_utm.PNG)

This tells an attacker that their Facebook presence is *working.* So, in
the future, they are more likely to use Facebook for further exploits.

Once we enable scripts, the page fully loads, and the UTM data goes
away:

![](/assets/2021-05-14/giveladieslove-url-changes-with-script-no-utm.PNG)

The **giveladieslove.com** landing page is explicit and will not be
shown here. It asks a series of questions, all of which are designed to
entice a victim through the promise of sexual activities. (Recall that
this is exactly how Dana gained her followers as well.)

![](/assets/2021-05-14/giveladieslove-initquestion.PNG)

After all questions are clicked, a *final* page loads: **date4you.net**.

## Data-gathering... by YOU

The final page, **date4you.net**, is also very graphic, and it will not
be shown. This stage of the campaign is really the whole goal of the
campaign. Again, the goal is for the attacker to get your information.

A form asks for a username, password, and email address in order to
"register" for the website:

![](/assets/2021-05-14/date4u.PNG)

From this, the attacker gleans three data points. The **username**,
**password**, and **email** might be repeated information that a victim
uses on other websites.

If this were an *attack* instead of spam, the attacker could use the
**email** and **password** on another site, including (drumroll...)
Facebook.

And, as noted, they knew that the victim used a Facebook link to get
here. It was right there in the URL's UTM data earlier.

At this point, the attacker assumes that the user wants the promise of
sexual content enough to enter this information. This guide stops at
this screen alone. Presumably, the website might also ask for payment
information. Payment information is not just another data point about
the user, but is also used to, well, get the victim's money.

# Further evidence

You can use tools like [**ipvoid.com**](https://www.ipvoid.com/) to
validate that the IP addresses of these domains are blacklisted. The
following example traces the final page, **date4you.net**.

First, use a tool like `nslookup` or IPVoid to find which IPs are
associated with each domain.

![](/assets/2021-05-14/ipvoid-find-ip.PNG)

This yields the IP for the domain.

![](/assets/2021-05-14/ipvoid-ip-found.PNG)

Copy the IP address. Then, go back, and enter it in the "blacklist"
checker.

![](/assets/2021-05-14/ipvoid-blacklist-check.PNG)

As shown, the associated IP has already been flagged as associated with
spam.

![](/assets/2021-05-14/ipvoid-blacklist-confirm.PNG)

# How to avoid this

There are a few ways to prevent such an attack from occurring in the
first place:

-   Block suspicious Facebook profiles and emails. They can still gain
    data about you, even if you don't accept the request. Blocking them
    prevents them from seeing any of your information. Likewise, block
    suspicious emails, or report them as spam.
-   Never click suspicious links. If you feel you must, make sure to
    inspect the link's `href` (target) in advance. Anything that still
    seems suspicious should be avoided.
-   Never give your personal information, contact information, or
    payment information to an untrusted source. Even data that seems
    innocent can be used for an attacker to develop a profile about you.

# Summary

A spam campaign wants to get user data and, often, payment information.
This one started with a fake Facebook profile, one that gained a
following, and the trust of its followers. It then offers links, which
can be traced back to Facebook, to lead users to a registration page. If
a user fills out this page with real information, the attacker has
several data points about the victim. They can use this information to
continue correspondence, or as the beginning of an attack.