---
layout: post
title: Analyzing a Facebook-based cyber campaign
date: 2021-05-14 19:08:00 -0400
categories: utm social engineering media facebook 
---

# Abstract

Fake Facebook profiles are the attack vector for many cyberattacks. Fake profiles which use real photographs of real people without their permission create two victim groups: those who interact with the page, and those whose photos are exploited for malicious gains. This walkthrough will explain the problems that arise when a user follows suspicious links on Facebook. It starts with an overview of a suspicious Facebook page, the origin of the attack. The structure of the link, and the resultant links that follow, are detailed. A reflection on the amount of data that an attacker can obtain from a user are noted at the end, along with techniques for avoiding such an attack.

# Background

Facebook contains a number of fake profiles which use inappropriate content to get a user's attention. Often, Facebook refuses to flag these profiles as suspicious. However, [Facebook users are very much aware of this issue](https://www.prod.facebook.com/help/community/question/?id=2253812781507202), and has written several publications on how to identify, report, and ignore these pages (along with the "why" for doing so). Facebook has posted [some responses](https://www.facebook.com/help/community/question/?id=2153305988220906) to these concerns. The company's own security posture is left to the reader to determine.

There is also a dangerous element with these pages. Consider the following from a [Sophos article about this exact topic](https://nakedsecurity.sophos.com/2018/03/23/beware-the-fake-facebook-sirens-that-flirt-you-into-sextortion/):

> Fake Facebook profiles of hot women who invite targets to join them in sexy webcam masturbation sessions – sessions that lead to image capture and [extortion](https://nakedsecurity.sophos.com/?s=sextortion) – are part of a “three-tiered, industrial process” that allows a sophisticated criminal network to “find, filter and defraud victims, all the while protecting itself,” according to an investigation done by [Radio Canada.](http://ici.radio-canada.ca/special/sextorsion/en/index.html)
>
> We’ve covered plenty of lone-wolf sextortionists: one who targeted underaged girls until he was [caught by investigators’ booby-trapped video;](https://nakedsecurity.sophos.com/2017/08/09/sextortionist-caught-by-investigators-booby-trapped-video/) the guy who [preyed on Miss Teen USA and 150 others](https://nakedsecurity.sophos.com/2014/03/19/sextortionist-who-preyed-on-miss-teen-usa-and-150-others-sentenced-to-18-months/); and a [former US Embassy worker](https://nakedsecurity.sophos.com/2016/03/23/sextortionist-government-worker-gets-nearly-5-years-in-the-slammer/) who sextorted, phished, broke into email accounts, stole explicit images and cyberstalked hundreds of women around the world from his London office. And there are many [others.](https://nakedsecurity.sophos.com/?s=sextortion)
>
> Not to downplay the suffering caused by such operators in any way – there have been multiple [suicides](https://nakedsecurity.sophos.com/2014/05/07/87-arrested-in-the-philippines-in-bust-of-sextortion-ring/) related to such cases – but those lone wolves are rank amateurs compared with the massive network of fraudulent accounts that catfish male victims using stolen photos of young women and adolescent girls.
>
> To find out how the networks spin their webs, Radio Canada journalists Marie-Eve Tremblay and Jeff Yates – an expert in online disinformation who’s found and mapped the connections between fake profiles to learn how they support each other – conducted a months-long investigation into what he believes is a “massive network.”
>
> They knew that the accounts were fake because the photos had been stolen from Instagram accounts or personal Facebook profiles. Some of the fake accounts are massive: they have 100,000, 200,000, or even 500,000 followers...

This is a real problem with a human cost. The tactics, techniques, procedures, and motivations of such page maintainers should be scrutinized so long as these pages continue to appear on social media platforms. This can inflict serious injury on any victims: the ones who follow the pages, *and the ones whose content is either stolen or exploited.* This walkthrough extends the problem noted in the article by exploring the technical details involved with such pages as they relate to a potential cyberattack.

*Note: The original content of these pages was not necessarily age-appropriate. Most of the image content is redacted. This is largely to protect the victims involved: the ones who liked the page under false pretenses, and the person featured on the page, whose photographs may be in use without her consent. Abstractions or snippets of the original content are used solely to explain the nature of common cyberattacks. Please be mature.* 

# Setup

The testing setup is a Virtual Machine running a lightweight, security-mindful distro. It is a throwaway environment and will be deleted after this demo. 

![](/assets/2021-05-14/test-setup.PNG)

This walkthrough is to illustrate some key concepts that cannot be meaningfully illustrated using an "app." The browser console gives everyone an advantage: the ability to analyze web content. Using these resources should encourage any user to remember that, behind the superficial experience offered by any app, there is an underlying structure of technical components that can be exploited under the right conditions.

*Author's note: This guide shows the domain names for several websites.
Please do not follow those directly. I cannot guarantee that they will
not mess up your system. I have done this work so you don't have to. Also, virtual machines are not necessarily secure from cyberattacks. In some cases, this would not be enough to protect the host operating system.*

# Tactics (the goal)

In short, the attacker wants the user to give up their own information. They will use a variety of techniques to try and accomplish this.

# Techniques (how they do it)

**Social engineering.** This fake profile campaign uses a variety of techniques to get user
engagement. The most dominant technique used is "[social engineering](https://usa.kaspersky.com/resource-center/definitions/what-is-social-engineering)." In short, this is getting users to act against their own best interests. This campaign specifically uses the [inappropriate material](https://www.bleepingcomputer.com/news/security/malware-spread-as-nude-extortion-pics-of-friends-girlfriend/) to gain a following of users (friends or followers) on Facebook. This following will become their pool of victims later on.

**Data collection.** The attacker who manages the profile then encourages users to click
links that arrive at a suspicious website.
Once there, users are prompted to provide personal information, like
their name and email address. Presumably, it would also ask for payment
information later on. (This walkthrough does not cover this because that
is a bad idea.) In a cyberattack, this could link to malicious content,
including a [phishing attack](https://www.consumer.ftc.gov/articles/how-recognize-and-avoid-phishing-scams) that steals information or downloads [malware](https://www.mcafee.com/en-us/antivirus/malware.html).

**Illusion of authenticity.** To perform its initial social engineering, the attacker also uses a
gallery of provocative pictures from the same model. This should raise
some concerns for one of two reasons. Either the model is working with
the spam/scam company. Or, she is completely unaware that her photos are
being used.

**Link obfuscation.** "Middle pages" are used to hide the eventual target: a spam site, which
compels users to enter their information. This technique of hiding the
eventual target is dangerous; if this were malware (virus, Trojan, or
phishing scam), a user would not be able to know that the target is
malicious. They would only see, for example, a shortened link.

**Malicious UTM campaign.** [UTM data](https://support.google.com/analytics/answer/1033863?hl=en) is
used later to notify the attacker that the target website was visited
through a Facebook link. This lets the attacker know that their Facebook
campaign is working. In the future, they are more likely to use Facebook
for other exploits.

**Suspicious website.** The goal is to get a user to subscribe for a website, which has some very suspicious qualities. It has even been flagged on blacklists. The page attempts to get the victim's personal
information. 

**Password attack.** A full name and email address will allow the attacker to
spam their inbox. If the website is suspicious *and malicious*, nothing will stop the attacker from
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

Fake profiles show up in Facebook's "People you may know" feed all the time. This happens when a friend "likes" or "friend-requests" such a page. Consider this one:

![](/assets/2021-05-14/dana-profile.PNG)

This user has *over one-thousand followers* (1,331 at the time of writing) and 2,022 friends. Most of her photographs violate Facebook's own position on content that includes nudity. However, she has been active since December of 2020. Her followers, it seems, absolutely love her.

Of course, the person in these photographs may not be the woman in the photograph. The rest of this walkthrough will illustrate why it is challenging to make this distinction. On one hand, the person in the photographs could be posting this malicious content --- that is, could themselves be the attacker. However, the idea that any user would associate their real self with a malicious profile seems unlikely. 

One takeaway to consider is this: if these images *do* belong to someone else, then all of the users on this page play a role in that person's exploitation by an adversary (or, in less technical jargon, a "jerk").

In short, the rest of this guide assumes that *someone* is behind the campaign. But, it calls into question whether or not it is the same person featured on this page. (It is the author's own belief that this page is fake, and that some model's photos are being used to drive this campaign, with or without her permission.)

## Suspicious links to suspicious pages

This page uses two different sets of links. The attacker wants their followers to click
either one. Both links eventually arrive at the same page.

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

In this case, both links match "what we see." Using a URL decoder, we can observe that both links are actually valid.
Facebook encodes the URL in order to use with its "external
link-handling service." We can view the target URL using any URL
decoder.

![](/assets/2021-05-14/urldecode-vht-encoded.PNG)

The **v.ht** URL is the value of the **u** variable:

![](/assets/2021-05-14/urldecode-vht.PNG)

Likewise, the **msto.me** link:

![](/assets/2021-05-14/urldecode-msto.PNG)

The problem is, of course, that the link itself might *lead* to a
malicious page. On a normal setup, this is where a prudent user might say, "DON'T CLICK THE
LINK!" Since this guide is in a virtual machine, we can *first log out of Facebook* (ie, clear your browser cache), and *then* follow the link, without too much fear about the main system suffering. 

*Note: It is crucial **never to follow a suspicious link in the same browser instance where you are currently logged in**. A phishing campaign might use this principle to harvest data from the browser session.*

At this point, copy and paste the URLs into a text editor (like
notepad), log out of Facebook, and clear the browser's cache. This browser
is configured to delete all information when closed. (A Private/Incognito
window accomplishes the same task.) 

## "Middle" pages

The suspicious URLs on the Facebook page direct the victim to two different pages "between" Facebook and the eventual target page. Both of these pages "in the middle" are discussed.

The **v.ht** page immediately tries to load the target, **giveladieslove.com**. It does so
automatically, even with scripts enabled, and shows only a "loading"
message in the time it takes to process. The browser does not catch any
suspicious activity because **v.ht** is just a link-shortening service.

![](/assets/2021-05-14/vht-loading.PNG)

The **msto.me** link is different. As a side note, the NoScript addon
prevents any content from loading at first. 

(This is a *good thing*. If a virus or phishing scam relies on links to scripts to exploit a system, it will not be able to work. Blocking scripts does not always block malware, but it can prevent scripts from performing unwanted behavior on your system.)

With scripts enabled, the page loads. The phone/WhatsApp icon seems to give the
page legitimacy because it is a familiar icon. The page even offers two
buttons for direct contact with the (fake) user:

![](/assets/2021-05-14/msto-landingpage.PNG)

Both buttons' URLS redirect to the target page, **giveladieslove.com**. They are not links to pages
within the **msto.me** website. This runs contrary to what the MSTO buttons would lead us to believe. We can inspect them for more detail:

![](/assets/2021-05-14/msto-links-analyzed.PNG)

To recap, the Facebook profile provides two different links. They both lead to different pages. However, both those pages lead to the same page, **giveladieslove.com**.

## UTM Campaign

Either of the two "middle pages" direct to a page called **giveladieslove.com**. In order to keep this appropriate for all audiences, the full webpage will not be displayed. Instead, this section will analyze some details regarding the URL.

First, the URL with scripts disabled reveals some information:
specifically, that we arrived on this site through the Facebook campaign
(`utm_campaign=FB2`).

![](/assets/2021-05-14/giveladieslove-msto-has_fb_campaign_utm.PNG)

This tells an attacker that their Facebook presence is *working.* So, in
the future, they are more likely to use Facebook for further exploits.

Once we enable scripts, the page fully loads, and the UTM data goes
away:

![](/assets/2021-05-14/giveladieslove-url-changes-with-script-no-utm.PNG)

However, nothing is stopping an attacker from storing that UTM information for use later. In the next section, we will observe how this is useful for targeting a clear, deliberate attack against the victim.

The **giveladieslove.com** landing page asks a series of lewd questions. (Recall that
this is exactly how the Facebook page gained their followers.) The author of this guide invites the reader to laugh at the exaggeration of the content displayed.

![](/assets/2021-05-14/giveladieslove-initquestion.PNG)

After all questions are clicked, a *final* page loads: **date4you.net**.

## Data-gathering... from you

The final page, **date4you.net**, is also very graphic, and it will not
be shown. This stage of the campaign is really the whole goal of the
campaign. To reiterate, the goal is for the attacker to get your information.

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
here. It was right there in the URL's UTM tags. If the two websites are linked in any meaningful way, anyone who manages **date4you** will have this data.

This guide stops at this screen alone. Presumably, the website might also ask for payment
information. Payment information is not just another data point about
the user. If this were an attack, payment information could be used simply to steal the victim's money.

## What information did they get?

By the end of the campaign, the victim leaked the following information to the attacker:

- Their Facebook photos and posts (if they friended them)
- The fact that they used a link that was posted to Facebook, and therefore likely has a Facebook account)
- Evidence that a sex-driven campaign worked, and is likely to work in the near future with others
- If they register, they willingly give up a username, email address, and password, all of which might be used to log in to other websites (including Facebook) tied to the same email or username, and possibly using the same password
- Any number of unknown information obtained by the webpage's scripts, beacons, and other unwanted resources

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
-   Use a content-blocker in your browser. Tools like UBlock Origin and NoScript are designed to mitigate your chances of falling victim to a cyberattack. Services like IVPN even block intrusive content (including known spam and malware domains) as a feature of the VPN itself.
-   Enable two-factor authentication whenever possible. This mitigates the chances that someone with only your password will be able to compromise your account.

# Further research

It is not clear whether the final website is written with malicious intent, or if it is just spam. Further research on profiles like these could explore and verify the intentions behind each component of the campaign. This article validates the untrustworthiness of these pages. Since they are untrustworthy, it observes what can happen if the group managing the websites chooses to act maliciously.

One could also explore Facebook's own trustworthiness as a platform. In the past decade, it has been a hotbed of lawsuits and critiques regarding its willingness to reveal user data, and its relaxed security posture with regards to leaking user data. Some would argue that Facebook itself is a scam site...

And, with that in mind, at the time of writing, Facebook does not believe that the page in question (the one we traced and found evidence of deceptive or fraudulent practices) violates any Terms of Service. The reader is thus invited to consider what this says about Facebook's own security posture, and how their views may one day affect them or the people whom they love.

![](/assets/2021-05-14/update-shes-ok.PNG)