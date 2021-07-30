---
layout: post
title: Investigating Telegram's Security Vulnerabilties (white paper)
date: 2021-07-01 15:57:00 -0400
---


<p><span> </span></p>
<h1 id="abstract">Abstract</h1>
<p>Telegram, a popular communication platform, should be avoided for any organization or business. This policy briefing discusses issues with Telegram’s default configuration, security implementations, and inability for an IT Administrator’s intervention. In lieu of Telegram, an organization should use a platform that uses secure protocols and enforces two-factor authentication. Policies should be written in such a way that rejects an application like Telegram from use in business purposes. Recommendations are provided for two applications — Signal and Microsoft Teams — which meet business needs and security standards.</p>
<h1 id="introduction">Introduction</h1>
<p>Telegram’s security settings leave too much room for attacks. No amount of user intervention or configuration fully solves this problem. Thus, Telegram is an inherently insecure platform.</p>
<p>The platform gained a lot of popularity after big-data companies (like Facebook’s Messenger or WhatsApp) started to dominate the market. Many people looked to Telegram as a secure, private alternative to data-collection and ad-injections.</p>
<p>Unfortunately, the security and privacy of Telegram is still very undesirable. It is untrustworthy, but for different reasons. In the last few years, its weak security model has been the focus of news headlines and peer-reviewed research (much of which is discussed in this paper).</p>
<p>Telegram’s insecurities become immediately clear when assessing them on a scale of “default settings” (worst) to “hardened settings” (best):</p>
<ul>
<li><p><strong>Default settings</strong>. Telegram’s default settings allow for a session hijack, assuming an adversary can get either the SIM card or cellular network stream.</p></li>
<li><p><strong>Hardened settings</strong>. A user has enabled the highest security and privacy settings in the app (thereby solving the issues noted with the “default settings”). Even then, an attacker could expose flaws in the security protocol: by “hacking” or “paying off” a backend administrator, and tampering with the encryption process. This would reveal the in-transit data between any two users’ chats, including secret chats.</p></li>
</ul>
<p>In short, even the best settings leave a ton of room for tampering and hacking.</p>
<h1 id="analysis-of-the-problem">Analysis of the problem</h1>
<p>This section analyzes several problems in Telegram’s security model:</p>
<ol>
<li><p>The issues with its default authentication model (SMS)</p></li>
<li><p>The vulnerabilities with its encryption protocol for data at-rest and in-transit (between chat clients)</p></li>
<li><p>The extent to which the user’s privacy settings can actually keep the user private</p></li>
</ol>
<h2 id="insecure-default-authentication">Insecure default authentication</h2>
<p>Any client that uses the default settings (security and privacy) is vulnerable to unauthorized access and, from there, a total account hijack. Telegram’s default authentication is just an SMS verification code, which it sends to the account’s associated phone number. Note that SMS authentication is considered one of the least secure multifactor authentication methods <span class="citation" data-cites="@indiana:multi"></span>.</p>
<h3 id="unauthorized-access">Unauthorized access</h3>
<p>First, an attacker needs to intercept the SMS message.<a href="#fn1" class="footnote-ref" id="fnref1" role="doc-noteref"><sup>1</sup></a> They can do this through a variety of means<a href="#fn2" class="footnote-ref" id="fnref2" role="doc-noteref"><sup>2</sup></a>:</p>
<ul>
<li><p><strong>A SIM-swap</strong>. This is when the attacker physically removes and places the victim’s SIM card into the attacker’s own device <span class="citation" data-cites="puig:simswap"></span>. If a carrier PIN has not been set, the attacker can log in using the same number, and the SMS verification is sent to the attacker’s device.</p></li>
<li><p><strong>Using a mock cellular access point</strong>. This could allow attacker to perform a “man in the middle” attack. (Note that SMS sends messages in plaintext across cellular networks.) In this case, the attacker triggers the SMS authentication from their own device. As the SMS is sent to the victim’s device, the attacker intercepts the message — and, thus, its contents. They can then use this code to log in on their own device.</p></li>
<li><p><strong>Gaining physical access to the phone itself</strong>. In this case, the attacker can trigger the SMS authentication through their own device, and read the authentication code directly on the victim’s device. (They could even take it a step further and log-out the victim, or even delete the application from the victim’s device.)</p></li>
<li><p><strong>Gaining access to the phone number</strong>. If the victim’s phone number is returned to the ISP (for example, if they stop paying for the line). If the attacker knows this, they could either ascertain the number for themselves, or find the number’s current owner and attempt to attack them before the (original) victim transfers their Telegram account to a new number.</p></li>
<li><p><strong>Non-technical workarounds</strong>. This includes social engineering. For example, a white-hat hacker was able to gain access to a victim’s entire text message stream by lying to a third-party vendor who works for T-Mobile <span class="citation" data-cites="brodkin:smsintercept"></span>.<a href="#fn3" class="footnote-ref" id="fnref3" role="doc-noteref"><sup>3</sup></a></p></li>
</ul>
<p>To access the account, an attacker simply follows the application’s login procedures: enters the victim’s phone number; requests an SMS or phone-call verification; and enters the code in the app. A successful login redirects the attacker to the victim’s chat history.</p>
<p>With any of these methods, the attacker is now in the victim’s account. They have full access to conversation history, saved contacts, and non-secret chats. They can now hijack the account.</p>
<h3 id="hijacking-the-account">Hijacking the account</h3>
<p>The initial access relies on the victim <em>not</em> having two-factor authentication. Unsurprisingly, the hijack relies on this, too.</p>
<p>To <em>maintain control</em> of the account, the attacker can follow the application’s steps to enable two-factor authentication <span class="citation" data-cites="telegram:2fa"></span>. They need only enter their own password and recovery email. Once these are enabled, the account will no longer provide SMS authentication. Further, if the victim tries to log in again, they will be asked for the attacker’s credentials. The attacker controls the account.<a href="#fn4" class="footnote-ref" id="fnref4" role="doc-noteref"><sup>4</sup></a></p>
<h2 id="weak-encryption-protocol-and-method">Weak encryption protocol and method</h2>
<p>The “security” and “privacy” of Telegram suffers from well-deserved scrutiny. This article highlights two pressing issues in particular. Telegram’s security model relies on a home-made protocol, MTProto(version 2.0 at the time of writing). This is responsible for encrypting chat conversations: end-to-end (“secret chats”) and device-to-server (other conversation types). Unfortunately, the protocol’s design makes the platform inherently insecure. By default, the servers act as a “man in the middle.” To make matters worse, the organization describes their platform in a way that underplays the amount of privileges given to their server-side processes. To further the issues, the protocol itself suffers a variety of security flaws.</p>
<p>The platform could alleviate many server-side concerns by open-sourcing the cryptographic processes which occur on their servers. Protocol concerns could be alleviated by fully migrating to a rigorously-tested protocol, like TLS. Until Telegram makes such changes, it should not be considered an “encrypted” platform <span class="citation" data-cites="albrecht:telesec"></span>. Likewise, one should avoid calling Telegram a “secure” messenger.</p>
<h3 id="server-side-security-and-privacy-issues">Server-side security and privacy issues</h3>
<p>This section will cover “cloud”(not end-to-end encrypted) and “secret” (end-to-end encrypted) conversations <span class="citation" data-cites="telegram:e2e"></span>. The MTProto implementation leaves a concerning amount of room for vulnerabilities in normal conversations — in particular, that it decrypts non-secret conversations on their servers.<a href="#fn5" class="footnote-ref" id="fnref5" role="doc-noteref"><sup>5</sup></a> On the other hand, secret conversations are not implemented in a way that is intuitive for users. Both conversation types suggest that end-to-end encryption is possible for cloud chats, but Telegram does not implement this.</p>
<h4 id="cloud-chats">Cloud chats</h4>
<p>Cloud chats are stored in Telegram’s servers. A user can access these conversations from any device. This type suffers from the greatest amount of security and privacy issues.</p>
<p>The backend servers can decrypt normal (cloud) conversations\footnote at any time. In fact, for cloud chats, the server converts “encrypted” messages between clients: from the sender to the server, and from the server to the recipient. This is a feature of their protocol. For any conversation, Telegram’s servers create at least three keys (one for the clients, and one for the server). In this way, Telegram’s in-transit security resembles a man-in-the-middle. The servers can read — and, therefore, intercept — all traffic. It should therefore be considered as an untrusted source <span class="citation" data-cites="mtproto-sec"></span>. Paradoxically, a user must trust Telegram’s servers for normal conversations, which are not end-to-end encrypted <span class="citation" data-cites="albrecht:telesec"></span>.</p>
<p>Telegram claims to <em>store</em> conversations and resources in a way that is, in their words, “heavily encrypted” <span class="citation" data-cites="telegram:privacy"></span>. This is true of how they store conversations <em>at rest</em>. Nonetheless, the servers decrypt the content <em>in-transit</em>. In other words, the way its stored is less important than the way it’s used: at some point, the server will see the conversation in the clear. To this end, “encryption” itself is less important than the <em>way</em> their encryption works.</p>
<p>Telegram tries to inspire confidence in their privacy model by noting that the server keys are kept in “other data centers in different jurisdictions.” However, as noted, the server converts the conversation content to plaintext as a feature of the conversation itself. An adversary could therefore get the conversation in the clear using any number of means: through a cyberattack, legal force, or financial incentives. The ideal server would <em>not</em> be able to decrypt a conversation — ever. For Telegram, this is a feature, and a highly vulnerable one under the wrong conditions.</p>
<h4 id="secret-chats">Secret chats</h4>
<p>Telegram’s “secret chats” are designed to alleviate the aforementioned concerns.However, these chat suffer their own issues. First, they are still bound to the flaws of the protocol. Second, they follow a different set of rules which affect the user experience. Secret chats are stored only on the device, not the server. A secret conversation can only be viewed on the mobile client, not the desktop client. When the application is uninstalled or reinstalled, the secret conversation disappears forever.</p>
<p>Although secret chats offer some privacy benefits, it nonetheless raises the question: if Telegram could store normal chats encrypted on their servers, without it hindering the protocol, why don’t they? Telegram has no formal comment to this end. Likewise, the author makes no speculation or assumptions about their reasoning. The question merely highlights the choice that Telegram imposes on its users: on one hand, you can access your chats from any device, but they follow a weak security model in-transit; or, you could use a more secure, private scheme, at the expense of never being able to access it outside of that device exclusively.</p>
<h3 id="protocol-related-issues">Protocol-related issues</h3>
<p>The <code>mptroto2.0</code> implementation is the subject of a wide breadth of critiques. Studies have highlighted its vulnerabilities to a <em>large variety</em> of attacks. The implementation for cloud chats is the most vulnerable. In short, MTproto should raise some alarms. Any security benefits are moot compared to the privacy and security concerns which it introduces.</p>
<h4 id="tested-attacks">Tested attacks</h4>
<p>The following attacks have been performed and documented by researchers.</p>
<ul>
<li><p><strong>Reordering messages</strong>. Telegram’s documentation clarifies that secret chats are protected against this, but it makes no mention as to whether cloud chats are also protected. This attack type would be most effective from client-to-server messages. The servers will accept messages sent prior to the timestamp of its most recent message in a given conversation. This could cause a reordering or a delay of messages <span class="citation" data-cites="albrecht:telesec"></span>.</p></li>
</ul>
<h4 id="theoretical-attacks">Theoretical attacks</h4>
<p>The following attacks are <em>plausible</em> attacks against MTProto, but they have not yet been tested.</p>
<ul>
<li><p><strong>Deleting messages</strong>. Messages sent from the sender or recipient can be “silently dropped.” This occurs when a client or server does not get acknowledgement of a message and tries to resend it. If an attacker can distinguish “repeated encryptions,” they can continuously drop the same payload <span class="citation" data-cites="albrecht:telesec"></span>.</p></li>
<li><p><strong>Re-encrypting messages</strong>. Messages not acknowledged within a certain timeframe are re-encrypted using the same header data and fresh random padding. The same statecan be used for two different encryptions.</p></li>
<li><p><strong>Unknown key-share</strong>.</p></li>
</ul>
<p>\footnote{In this context, “state” refers to the message ID and message sequence number (\texttt{msg_id} and \texttt{msg_seq_no}) from the header information <span class="citation" data-cites="albrecht:telesec"></span>.}</p>
<h2 id="it-admin-cannot-enforce-policies-or-settings">IT admin cannot enforce policies or settings</h2>
<p>With all this in mind, consider that the IT administrator has no control over Telegram’s security or privacy policies. This leaves the entire security and privacy administration under the purview of two groups of people: Telegram’s developers and server staff for the backend; and the user for the frontend (client-side settings) <span class="citation" data-cites="kaspersky-priv"></span>. This introduces serious questions about the integrity of data stored on Telegram’s servers.</p>
<h1 id="recommendations">Recommendations</h1>
<p>This section outlines mitigating strategies and alternative messaging platforms. All suggestions are founded on zero-trust policies and solve the security issues introduced by Telegram. These can be used by the individual for their personal devices; or, it can be adopted by security policymakers in creating or revising an organization’s policies.</p>
<h2 id="mitigations">Mitigations</h2>
<p>The organization’s security policies should reflect the following standards:</p>
<p><strong><em>Use a platform that enforces two-factor authentication.</em></strong></p>
<ul>
<li><p>Enforce the use of platforms that can use an “authenticator app” or a physical security key in addition to a strong password.</p></li>
<li><p>At the bare minimum, enforce email authentication codes.</p></li>
<li><p><em>Do not use an application that performs authentication with only SMS codes!</em></p></li>
</ul>
<p><strong><em>If the application requires a phone number for the username, ensure that the number is in your posession.</em></strong></p>
<ul>
<li><p>If a SIM card is required, set a carrier PIN to prevent SIM-swapping.</p></li>
<li><p>If the phone number is virtual, ensure that the virtual provider uses two-factor authentication (for example, to access the dashboard). Enable the strongest multifactor authentication settings for that account.</p></li>
<li><p>Monitor the status of each phone-account mapping. (That is, if a phone number has changed, update any apps which rely on this number as the username.)</p></li>
<li><p>Delete any unused accounts (for example, if a user leaves the organization) which are associated with an organization’s phone number.</p></li>
</ul>
<p><strong><em>Use platforms that secure user data.</em></strong></p>
<ul>
<li><p>When possible, use platforms which use end-to-end encryption and a secure protocol. Base these decisions off peer-reviewed research, which highlight objective findings about the platform’s security model.</p></li>
<li><p>Use platforms which do not present opportunities for someone else to view message contents. End-to-end encryption should prevent anyone except the conversation’s intended recipients from viewing chat resources or conversation history. For organizations, ensure that no one <em>outside the organization</em> can access this information.</p></li>
<li><p>For organizations, use a platform that leaves the user no opportunities to reduce their own security or privacy settings: for example, platforms that employ strong default settings, or ones which rely on an administrator’s policy and settings.</p></li>
</ul>
<h2 id="alternative-platforms">Alternative platforms</h2>
<p>The following platforms should be recommended to the organization’s users:</p>
<ul>
<li><p><strong>Signal</strong>. The actual content is end-to-end encrypted (not stored on servers)<span class="citation" data-cites="mora:e2e"></span>, the PIN provides a second authentication factor <span class="citation" data-cites="signal:pin"></span>, and the Signal protocol has succeeded rigorous security tests <span class="citation" data-cites="mtproto-sec"></span>. Use Signal if <em>the organization’s IT department does not need to view chat content.</em></p></li>
<li><p><strong>Microsoft Teams</strong>. IT administrators can review and audit message content. They can also enforce multi-factor authentication for users <span class="citation" data-cites="microsoft:mfa"></span>. Use Teams if <em>the organization’s IT department must view chat content.</em></p></li>
</ul>
<h1 id="references">References</h1>
<p>Albrecht, Martin R. et al. "Four Attacks and a Proof for Telegram." <em>Security Analysis of Telegram (Symmetric Part).</em> 16 Jul. 2021. <a href="https://mtpsym.github.io/paper.pdf">https://mtpsym.github.io/paper.pdf</a>, pp. 1.</p>
<p>Brodkin, Jon. "$16 attack shows how easy carriers make it to intercept text messages." <em>Arstechnica.</em> 16 Mar. 2021. <a href="https://arstechnica.com/information-technology/2021/03/16-attack-let-hacker-intercept-a-t-mobile-users-text-messages/">https://arstechnica.com/information-technology/2021/03/16-attack-let-hacker-intercept-a-t-mobile-users-text-messages/</a>.</p>
<p>"End-to-End Encryption, Secret Chats." <em>Telegram</em>. 29 Jul. 2021. <a href="http://web.archive.org/web/20210126013030/https://core.telegram.org/api/end-to-end">http://web.archive.org/web/20210126013030/https://core.telegram.org/api/end-to-end</a>.</p>
<p>Greenberg, Andy. "Fleeing WhatsApp for Better Privacy? Don’t Turn to Telegram." <em>Wired</em>. 27 Jan. 2021. <a href="https://www.wired.com/story/telegram-encryption-whatsapp-settings">https://www.wired.com/story/telegram-encryption-whatsapp-settings</a></p>
<p>Kaspersky Team. <em>Kaspersky Daily.</em> "Telegram security and privacy tips." 15 Jan. 2021. <a href="https://www.kaspersky.com/blog/telegram-privacy-security/38444/">https://www.kaspersky.com/blog/telegram-privacy-security/38444/</a>.</p>
<p>"Set up multifactor authentication." <em>Microsoft 365 Admin Help Center.</em> 12 Jul. 2021. <a href="https://docs.microsoft.com/en-us/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication?view=o365-worldwide">https://docs.microsoft.com/en-us/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication?view=o365-worldwide</a>.</p>
<p>Miculan, Marino and Nicola Vitacolonna. <em>Automated Symbolic Verification of Telegram’s MTProto 2.0.</em> Version 2, 30 Apr. 2021. <a href="https://arxiv.org/abs/2012.03141">https://arxiv.org/abs/2012.03141</a>, pp. 1-17.</p>
<p>Mora, Justino. "Demystifying the Signal Protocol for End-to-End Encryption (E2EE)." <em>Medium.</em> 17 Aug. 2017. <a href="https://medium.com/@justinomora/demystifying-the-signal-protocol-for-end-to-end-encryption-e2ee-ad6a567e6cb4">https://medium.com/@justinomora/demystifying-the-signal-protocol-for-end-to-end-encryption-e2ee-ad6a567e6cb4</a>.</p>
<p>"Multifactor Authentication". <em>University of Southern Indiana.</em> 2021. <a href="https://www.usi.edu/it/multifactor-authentication/">https://www.usi.edu/it/multifactor-authentication/</a>.</p>
<p>Puig, Alvaro. "SIM Swap Scams: How to Protect Yourself." <em>Federal Trade Commission: Consumer Information</em>. 23 Oct 2019. <a href="https://www.consumer.ftc.gov/blog/2019/10/sim-swap-scams-how-protect-yourself">https://www.consumer.ftc.gov/blog/2019/10/sim-swap-scams-how-protect-yourself</a>.</p>
<p>"Signal Pin." <em>Signal Support.</em> Accessed 21 Jul. 2021. <a href="https://support.signal.org/hc/en-us/articles/360007059792-Signal-PIN">https://support.signal.org/hc/en-us/articles/360007059792-Signal-PIN</a>.</p>
<p>"Mobile Protocol: Detailed Description." <em>Telegram</em>. 29 Jul. 2021. <a href="http://web.archive.org/web/20210126200309/https://core.telegram.org/mtproto/description">http://web.archive.org/web/20210126200309/https://core.telegram.org/mtproto/description</a>.</p>
<p>"Telegram Privacy Policy: Cloud Chats." <em>Telegram</em>. 14 Aug. 2018. <a href="https://telegram.org/privacy">https://telegram.org/privacy</a>.</p>
<p>"Two-factor authentication." <em>Telegram.</em> Accessed 24 Jul. 2021. <a href="https://core.telegram.org/api/srp">https://core.telegram.org/api/srp</a>.</p>
<section class="footnotes" role="doc-endnotes">
<hr />
<ol>
<li id="fn1" role="doc-endnote"><p>Attaining the SMS message requires reconnaissance on the attacker’s part. Specific information on how an attacker might gain this knowledge is beyond the scope of this paper.<a href="#fnref1" class="footnote-back" role="doc-backlink">↩︎</a></p></li>
<li id="fn2" role="doc-endnote"><p>This list is by no means exhaustive. The point is that cellular communication is inherently insecure. Thus, SMS-based authentication is also an insecure way to authenticate a user’s identity.<a href="#fnref2" class="footnote-back" role="doc-backlink">↩︎</a></p></li>
<li id="fn3" role="doc-endnote"><p>The vendor, Sakari, and T-Mobile have taken steps to rectify this issue. It should also be noted that this issue arose because of the differences in phone-number authorization between the US and other countries.<a href="#fnref3" class="footnote-back" role="doc-backlink">↩︎</a></p></li>
<li id="fn4" role="doc-endnote"><p>The victim could take any number of measures to try and regain access. For example, they could attempt a "counter-hack" the attacker to acquire the attacker’s password or email code. The effort required to do so is beyond the scope of this paper. The author advocates for users to set multifactor authentication so as to avoid the need to regain control of one’s account.<a href="#fnref4" class="footnote-back" role="doc-backlink">↩︎</a></p></li>
<li id="fn5" role="doc-endnote"><p>Some sources report that still stores information in the clear <span class="citation" data-cites="greenberg:flee"></span>. Telegram officially claims that it encrypts data on its servers: "All data is stored heavily encrypted" <span class="citation" data-cites="telegram:privacy"></span>. Determining whether Telegram encrypts regular chats is outside the scope of this briefing, but it is certainly worth clarification in further research.<a href="#fnref5" class="footnote-back" role="doc-backlink">↩︎</a></p></li>
</ol>
</section>

