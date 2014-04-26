There are an increasing number of projects working on next generation secure email or email-like communication. This is an initial draft report highlighting the projects and comparing the approaches. Please help us fill in the missing details and correct any inaccuracies. To contribute to this document, fork this repository and issue a pull request.

Contents:

1. [Executive Summary](#tldr)
1. [Common Problems](#common-problems)
  1. [Key Management](#key-management)
  1. [Protection of Transaction Data and the Social Graph](#metadata-protection)
  1. [Forward Secrecy](#forward-secrecy)
  1. [Data Availability](#data-availability)
  1. [Secure Authentication](#secure-authentication)
  1. [Scalability](#scalability)
  1. [Social Usability](#social-usability)
1. [Web Mail](#web-mail)
  1. [Mega](#mega)
  1. [PrivateSky](#privatesky)
  1. [Scramble](#scramble)
  1. [Startmail](#startmail)
  1. [Whiteout](#whiteout)
1. [Browser Extensions](#browser-extensions)
  1. [Mailvelope](#mailvelope)
1. [Mail Clients](#mail-clients)
  1. [Bitmail](#bitmail)
  1. [Mailpile](#mailpile)
  1. [Parley](#parley)
1. [Self-Hosted Email](#self-hosted-email)
  1. [Dark Mail Alliance](#self-hosted-dark-mail)
  1. [FreedomBox](#freedombox)
  1. [Mailpile](#self-hosted-mailpile)
  1. [kinko](#kinko)
1. [Email Infrastructure](#email-infrastructure)
  1. [Dark Mail Alliance](#dark-mail-alliance)
  1. [LEAP Encryption Access Project](#leap)
1. [Public-Key Routing Systems](#public-key-routing)
  1. [Briar](#briar)
  1. [Cables](#cables)
  1. [cjdns and net2o](#cjdns-net2o)
  1. [Goldbug](#goldbug)
  1. [I2PBote](#i2pbote)
  1. [Pond](#pond)
  1. [Retroshare](#retroshare)
  1. [secushare](#secushare)
  1. [Susimail](#susimail)
1. [Unclassified](#unclassified)
  1. [Bitmessage](#bitmessage)
  1. [Dark Mail Alliance](#p2p-dark-mail-alliance)
  1. [FlowingMail](#flowingmail)
1. [Related Works](#related-works)

<a name="tldr"></a>TL;DR: Executive Summary
===========================================================

Traditional email faces unsurmountable problems concerning trustworthiness, usability and protection of social information on who is participating in communications when it comes to provide end-to-end confidentiality. Even the best projects in the field accept dangerous trade-offs such as 1. allowing for man in the middle attacks at the moment of first use, 2. allowing people who disregard your privacy requirements to send you unencrypted messages, 3. allowing observing entities to map out the identities and the intensity of social interactions, 4. requiring a complete overhaul of the involved protocols if forward secrecy is to be achieved and 5. to tackle incongruencies in the architecture with user interface design, as if that hadn't been tried in the previous twenty years. Also, none of the improvements tackle the problem that people are flocking away to Facebook Mail, a cloud- and social-network-based alternative that offers 6. better usability, 7. user-expectation conformant scalability and 8. freedom from Spam and correlated nuisances.

It is not surprising that projects that started over a decade ago re-inventing the foundation of the Internet based on public-key routing and distributed hashtable technology, have in the meantime developed to a degree of solidity that they are presenting a true alternative way for many Internet applications including email. These technologies solve several of the problems of traditional email by design, other are in the process of being resolved, but since the science community has made very in-depth research on the issues involved with public-key based routing, the methods are known and already implemented in some projects.

It is reasonable to conclude that to improve and deploy such fundamentally new technology is lower hanging fruit than to further attempts of repairing the old broken architecture, but there is very emotional resistance in the systems administrators community to this kind of conclusion. If you think you need to make up your mind for yourself, this document is for you.

<a name="common-problems"></a>Common Problems
===========================================================

All of the technologies listed here face a common set of problems when trying to make email (or email-like communication) secure and easy to use. These problems are hard, and have defied easy solutions, because there are no quick technological fixes: at issue is the complex interaction between user experience, real world infrastructure, and security. Although no consensus has yet emerged on how best to tackle any of these problems, the diversity of projects listed in this report reflect an surge of interest in this area and an encouraging spirit of experimentation.

<a name="key-management"></a>Key Management
-----------------------------------------------------------

All the projects in this report use public-key encryption to allow a user to send a confidential message to the intended recipient, and for the recipient to verify the authorship of the message. Unfortunately, in traditional approaches to email, public-key encryption is notoriously difficult to use properly, even for advanced users. The very concepts are confusing for most users: public key versus private key, key signing, key revocation, signing keys versus encryption keys, bit length, and so on. Newer systems hide this complexity from the user, at the cost of being incompatible to the old methods.

Traditionally, public key cryptography for email has relied on either the X.509 Certificate Authority (CA) system or a decentralized "Web of Trust" (WoT) for key validation (authenticating that a particular person owns a particular key). Recently, both schemes have come under intense criticism. Repeated security lapses at many of the Certificate Authorities have revealed serious flaws in the CA system. On the other hand, in an age where we better understand the power of social network analysis and the sensitivity of the social graph, the exposure of metadata by a publicly available "Web of Trust" is no longer acceptable from a security standpoint. Such information must only be available to the people involved, which puts limitations to the acceptable depth of the view of the social graph.

This is where we are now: we have public key technology that is excessively difficult for the common user in the way it works with the email infrastructure, and our traditional methods of key validation have fallen into disrepute. The projects listed here have plunged into this void, attempting to simplify the usage of public-key cryptography. These efforts have four elements:

* Key discovery: There is no commonly used standard for discovering the public key attached to a particular email address. All the SMTP-based projects intend to initially use, as a stop-gap measure, the OpenPGP keyservers for key discovery, although the keyserver infrastructure was not designed to be used in this way. Newer strategies for discovery are the exchange of public keys 1. using a graphic depiction of the public key (usually QR codes) printed on business cards or brochures, 2. by bringing end devices into bluetooth range of each other, 3. by adopting new people from a trusted person's social vicinity.
* Key validation: If not Certificate Authorities or Web of Trust, what then? Nearly every SMTP-based project uses Trust On First Use (TOFU) in one way or another. With TOFU, a key is assumed to be the right key the first time it is used. TOFU can work well for long term associations and for people who are not being targeted for attack, but its security relies on the security of the discovery transport and the application's ability to retain a memory of discovered keys. TOFU can break down in many real-world situations where a user might need to generate new keys or securely communicate with a new contact. The projects here are experimenting with TOFU in different ways, and these problems can likely be mitigated by combining TOFU with other measures. Still there are ways to avoid TOFU as indicated by at least three methods mentioned above: 1. printed QR code, 2. bluetooth exchange, 3. social adoption. Additionally there are validation strategies based on shared secrets. It is however a good design choice to not separate key discovery from validation or otherwise validation becomes a bureaucratic extra transaction that people will be too lazy to execute. Both TOFU and shared secret should therefore be discontinued in favor of the three stronger authentication methods.
* Key availability: Almost every attempt to solve the key validation problem turns into a key availability problem, because once you have validated a public key, you need to make sure that this validation is available to the user on all the possible devices they might want to send or receive messages on. For systems that route by public key this is only a question of synchronizing address books, since there is no distinction between a person and her public key.
* Key revocation: What happens when a private key is lost, and a user wants to issue a new public key? Of all the projects in this report, only one has an answer for how to deal with this in a post-CA and post-WoT world: All users need to generate master keys that they will hardly ever use in everyday life and have all day-to-day use keys derived from those. secushare's strategy for storing the master key safely is to print it out on a QR code for the user to hide in a safe place in the house, then remove the key material from computer memory. This way the user can recover her identity by recovering her master key, issuing revocations for derived keys and bring new ones in circulation.

<a name="metadata-protection"></a>Protection of Transaction Data and the Social Graph
-----------------------------------------------------------

Traditional schemes for secure email have left transaction data and the social graph of people exposed. The NSA prefers to use the term metadata, as it sounds less dramatic. We now know that metadata is often more sensitive than message content: metadata is structured data, easily stored forever, and subject to powerful techniques of social network analysis that can can be incredibly revealing.

Metadata protection, however, is **hard**. In order to protect metadata, the message routing protocol must hide the sender and recipient from all the intermediaries responsible for relaying the message. This is not possible with the traditional protocol for email transport, although it will probably be possible to piggyback additional (non-backward compatible) protocols on top of traditional email transport in order to achieve metadata protection. Still a clean slate approach is probably going to be safer from a security standpoint.

In fact, many projects reject traditional email transport entirely and replace it with public-key based routing, frequently confused with peer-to-peer. These approaches to metadata protection generally fall into four camps: (1) directly pass the message from sender's device to recipients device by physical interaction; (2) relay messages through a network of socially interwoven nodes; (3) broadcast messages to everyone; (4) relay messages through an anonymization network such as Tor. The third solution faces serious problems of scalability.

All schemes that do not combine metadata protection with the implicit rejection of messages from socially unrelated strangers face the prospect of increasing Spam, since one of the primary methods used to prevent Spam is analysis of metadata. Some projects like Retroshare, Briar and in particular secushare compensate for this by offering social-graph-based discovery of contacts, thus allowing to introduce yourself to someone via first or possibly second degree of common contacts.

<a name="forward-secrecy"></a>Forward Secrecy
-----------------------------------------------------------

Forward secrecy is a security property that prevents an attacker from saving messages today and then later decrypting these messages once they have captured the user's private key. Traditional email encryption offers no forward secrecy.

All methods for forward secrecy involve a process where two parties negotiate an ephemeral key that is used for a short period of time to secure their communication. In many cases, the ephemeral key is advanced or generated anew for every single message. Ephemeral key generation requires a back and forth exchange between both parties to get started, which may be impractical at first if they aren't online at the same time. Once started the problem no longer exists.

Another possible approach is to use traditional encryption with no support for forward secrecy but instead rely on a scheme for automatic key discovery and validation in order to frequently rotate keys. This way, a user could throw away their private key every few days, achieving a very crude form of forward secrecy. That could work if the key discovery methods were safe and trustworthy.

<a name="data-availability"></a>Data Availability
-----------------------------------------------------------

Users today demand data availability: they want to be able to access their messages and send messages from any device they choose, wherever they choose, and whenever they choose. Most importantly, they don't want the loss of any particular device to result in a loss of all their data. For insecure communication, achieving data availability is dead simple: simply store everything in the cloud. For secure communication, however, we have no proven solutions to this problem. As noted above, the key management problem is also really a data availability problem.

Most of the projects here have postponed dealing with the data availability problem. A few have used IMAP to synchronize data or developed their own secure synchronization protocol. secushare uses anonymized distribution trees over relay nodes, thus enabling all devices to access the data safely from a socially powered kind of cloud.

<a name="secure-authentication"></a>Secure Authentication
-----------------------------------------------------------

For those projects that make use of a service provider, one of the key problems is how to authenticate securely with the service provider without revealing the password (since the password is probably also used to encrypt the private key and other secure storage, so it is important that the service provider does not have cleartext access as with typical password authentication schemes). The possible schemes include:

* Separate passwords. The application can use one password for authentication and a separate password for securing secrets.
* Pre-hash the password on the client before sending it to the server. This method can work, although it does not also authenticate the server (an impostor server can always reply with a success message), and is still vulnerable to brute force dictionary attacks.
* Use Secure Remote Password (SRP), a type of cryptographic zero-knowledge proof designed for password authentication in which the client and server mutually authenticate. SRP has been around a while, and is fairly well analyzed, but it is still vulnerable to brute force dictionary attacks (albeit much less than traditionally password schemes).
* Sign a challenge from the server with the user's private key. This has the advantage of being nearly impossible to brute force attack, but is vulnerable to impostor server providers and requires that the user's device has the private key.

No consensus or standard has yet emerged, although SRP has been around a while.

<a name="scalability"></a>Scalability
-----------------------------------------------------------

Limitations in scalability are traditionally accepted in e-mail. Should you need to send a message to a mailing list of ten thousands of users, it is accepted that your server will be busy all day doing just that. Should you instead own an account at Google Mail and happen to have a mailing list of a million other Google users, then it is accepted that the message will be distributed in a question of seconds.

This discrepancy is due to the federated architecture of SMTP known not to scale very well while a mail system which is fully internal to a commercial cloud can make use of advanced publish/subscribe distribution trees. Some advanced email replacements also take this aspect in consideration and try to improve on the scalability of email by introducing anonymized distributed publish/subscribe strategies. Frequently they also include the capability of delivering binary encoded data natively, thus reducing in encoding overhead compared to email's MIME formats.

<a name="social-usability"></a>Social Usability
-----------------------------------------------------------

Email is currently losing millions of users to social network offerings such as Facebook, simply because it is fully integrated into a social experience, easier to handle (requires no addresses), visually more appealing (it shows the communication partner's photograph), less asynchronous (if both are online, it turns into a real-time chat) and generally less formal.

Ignoring these large usability improvements provided by the integration into a social experience could result in new mail systems failing to reach for the large audience of people who don't put security first. Some projects address this by integrating the new mail experience in an advanced social networking experience, also allowing to simplify the adoption of public keys from the list of a friend's friends as an alternative to more cumbersome methods of key authentication.

<a name="web-mail"></a>Web Mail
===========================================================

Most users are familiar with web-based email and the incredible convenience it offers: you can access your email from any device, and you don't need to worry about data synchronization. Developers of web-based email faces several difficult challenges when attempting to make a truly secure application. These challenges can be overcome, but not easily.

First, because the web application is loaded from the web server each time you use it, the service provider could be targeting a version of the client to you that includes a backdoor. To overcome this vulnerability, it is possible to load the code for the web application from a third party. There are two ways of doing this:

1. App Store: Most web browsers support special extensions in the form of "Browser Applications". These are loaded from some kind of app store and installed on the user's device. In this case, the third party that provides the application is the app store. Therefore, the user is then relying on the app store to furnish them with a secure version of the app. For example, this is the approach taken by [cryptocat](https://crypto.cat).
2. Third Party: There are two advanced mechanisms to allow a web application to be loaded from one website and allow it to access data from another website. One is called CORS ([Cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)) and the other is HTML5's [window.postMessage command](https://developer.mozilla.org/en-US/docs/Web/API/window.postMessage). With either method, anyone can be the third party furnishing the application, or it can be self hosted. For example, this is the approach taken by [Unhosted](https://unhosted.org).

Second, even if the application is loaded from a trusted third party, web browsers are not an ideal environment for sensitive data: there are many ways for an in-browser application to leak data and web browsers are notoriously prone to security holes (it is a very difficult problem to be able to run untrusted code locally in a secure sandbox). To their credit, the browser developers are often vigilant about fixing these holes (depending on who you ask), but the browser environment is far from a secure computing environment. It continues to be, however, the most convenient environment.

Third, developers of web-based secure email face an additional challenge when dealing with offline data or data caching. Modern HTML5 apps typically store a lot of data locally on the user's device using the localStorage facility. Currently, however, no browser stores this encrypted. A secure web-based email application must either choose to not support any local storage, or develop a scheme for individually encrypting each object put in localStorage, a process which is very inefficient. Even storing keys temporarily in short lived session storage is problematic, since these can be easily read from disk later.

Some of the new mail systems and mail clients avoid all of the problems above by offering their web interface over a little web server installed on the same computer or device the web browser is running on. In that case all the sensitive data can stay on the native application even if the user interface seems to be a web site. Those are not considered actual "web mail" in this document and therefore appear in following chapters.

<a name="mega"></a>Mega
-----------------------------------------------------------

[mega.co.nz](https://mega.co.nz)

The relaunch of Mega has featured client-side encryption via a javascript application running in the browser. Mega has announced plans to extend their offerings to include email service with a similar design. No details are yet forthcoming. In interviews, Mega has said the javascript running in the browser will be open source, but the server component will be proprietary.

<a name="privatesky"></a>PrivateSky
-----------------------------------------------------------

PrivateSky was a secure web-based email service that chose to shut down because their design was not compatible with UK law. Many in the press have [said GCHQ forced the closure](http://www.ibtimes.co.uk/articles/529392/20131211/gchq-forced-privatesky-secure-email-service-offline.htm), which the [company refutes](http://www.certivox.com/blog/bid/359788/The-real-story-on-the-PrivateSky-takedown).

<a name="startmail"></a>StartMail
-----------------------------------------------------------

[startmail.com](http://startmail.com)

The makers of the secure search engine [startpage.com](https://startpage.com) have announced they will be providing secure email service.

Despite the tag line as the "world's most private email," StartMail is remarkably insecure. If offers regular IMAP service and a webmail interface that supports OpenPGP, but the user still must trust StartMail entirely. For example when you authenticate, your password string is sent to StartMail, and new OpenPGP keypairs are generated on the server, not the client. The website also makes some dubious statements, such as claiming to be more secure because their TLS server certificate supports extended validation.

Verdict: oil of snake

<a name="scramble"></a>Scramble
-----------------------------------------------------------

[scramble.io](https://scramble.io)

Scramble is an OpenPGP email application that can be loaded from a website (with plans to add app store support). Additionally, you can sign up for email service from scramble.io.

**Keys:** Private keys are generated in the browser app, encrypted with the user's passphrase, and then stored on the server. The server never sees the user's passphrase (password is hashed using scrypt before sent to the server during account creation and authentication). The master storage secret (symmetric key) used to encrypt keys is stored in the browser's sessionStorage, which is erased when the user logs out. Keys are validated using notaries.

**Infrastructure:** Scramble uses a system of network perspectives to discover and validate public keys. The client will come with a list of pre-blessed notaries that can be used to query for public keys. If the notaries agree, the client will consider the key to be validated.

**Application:** Currently, Scramble is a traditional HTML5 javascript application loaded from the website. In the future, Scramble will also be an installable browser app.

* Written in: Go, Javascript
* Source code: https://github.com/dcposch/scramble
* Design documentation: https://github.com/dcposch/scramble/wiki/Scramble-Protocol
* License: LGPL
* Platforms: Windows, Mac, Linux (with Android planned).

<a name="whiteout"></a>Whiteout
-----------------------------------------------------------

[whiteout.io](https://whiteout.io)

Whiteout is a commercial service featuring an HTML5-based OpenPGP email client that is loaded from the web.

* Written in: Javascript
* Source code: https://github.com/whiteout-io/mail-html5
* License: proprietary, but the code is available for inspection.

<a name="browser-extensions"></a>Browser Extensions
===========================================================

A browser extension modifies the behavior of the web browser (not to be confused with a browser application, which has far fewer permissions and consists of a self-contained application). Browser extensions are able to modify how the user interacts with a variety of websites. Browser extensions share many of the same advantages and disadvantages of [web mail approaches](#webmail).

<a name="mailvelope"></a>Mailvelope
-----------------------------------------------------------

[mailvelope.com](http://mailvelope.com)

Mailvelope is a browser extension that allows you to use OpenPGP email with traditional web-mail providers like Gmail, Yahoo, and Outlook.com.

**Keys:** The private key is generated for you, password protected, and stored in the browser's local storage (along with public keys). In the future, the plan is to support automatic discovery and validation of public keys using OpenPGP keyservers and message footers.

**Application:** When the extension detects you have opened a web page from a supported web-mail provider such as Gmail, it offers the user the opportunity to encrypt what you type in the compose window and decrypt messages you receive.

**Limitations:** Because of an inherent limitation in the way Mailvelope can interface with web-mail, it is not able to send OpenPGP/MIME (although it can read it fine). As mentioned before, browser storage is not a particular ideal place to be storing keys. When a web-mail provider changes their UI (or API if they happen to have one), the extension must be updated to handle the new format.

* Contact: info@mailvelope.com
* Written in: Javascript
* Source code: https://github.com/toberndo/mailvelope
* Design documentation: http://www.mailvelope.com/help
* License: AGPL
* Platforms: Windows, Mac, Linux (with Android planned).

<a name="mail-clients"></a>Mail Clients
===========================================================

An email client, or MUA (Mail User Agent), provides a user interface to access email from any service provider. Traditional examples of email clients include Thunderbird or Microsoft Outlook (although both these application include a lot of other functionality as well). Nearly all email clients communicate with the email service provider using IMAP or POP and SMTP, although some also support local mailboxes in mbox or Maildir format.

There are two primary advantages to the mail client approach:

1. Existing accounts: By using a custom secure mail client, a user can continue to use their existing email accounts, although that isn't really an advantage since a key discovery and validation procedure needs to be made for each interesting contact which is just as cumbersome as adding that person on a completely different software. Also, allowing contacts to send you unencrypted messages can harm your privacy.
1. Tailored UI: A custom client has the potential to rethink the email user experience in order to better convey security related details to the user - whithin the limits imposed by the traditional email architecture (having to convey the importance of addresses, for example).

The mail client approach, however, also has several disadvantages:

1. Insecure service providers: A mail client cannot address many of the core problems with email security when used with a traditional email provider. For example, metadata will not be protected in storage or transit, and the provider cannot aid in key discovery or validation. Most importantly, many existing mail providers are highly vulnerable, since few rely on DNSSEC for their MX records or validate their StartTLS connections for mail relay (when they even bother to enable StartTLS). A traditional email provider also requires authentication via password that is seen by the provider in clear text, and might be recorded by them. Finally, most service providers retain significant personally identifiable information, such as IP address of clients.
1. Install a new app: As with many of the other approaches, the custom mail client approach requires that users download and install a specialized application on their device before they can use it.

Ultimately, the level of email security that is possible with the custom mail client approach will always be limited. However, custom email clients may be an excellent strategy for gradually weening users away from email and to a different and more secure protocol. Most of the projects in this section see email support as a gateway to ease the transition to something that can replace email.

<a name="bitmail"></a>Bitmail
-----------------------------------------------------------

[bitmail.sf.net](http://bitmail.sf.net)

Bitmail is a desktop application that provides a user interface for traditional IMAP-based mail, but also supports a custom peer-to-peer protocol for relaying email through a network of friends. Bitmail will support both OpenPGP and S/MIME.

**Keys:** Keys are validated using a shared secret or fingerprint validation. Public keys are discovered over the P2P network. Keys are stored locally in an encrypted database.

**Routing:** Bitmail uses an oppurtunistic message distribution model where every message is sent to every neighbor. It is called "Echo" and it is very similar to the protocols used by Retroshare and Briar.

**Application:** Bitmail uses the Qt library for cross platform UI.

There are also plans to include a Bitmail MUA extension.

* Written in: C
* Source code: http://sourceforge.net/projects/bitmail/files
* Design documentation: http://sourceforge.net/p/bitmail/code/HEAD/tree/branches/BitMail.06.2088_2013-11-03/BitMail/branches/BitMail/Documentation/
* License: GPL v2
* Platforms: Windows (with Mac and Linux planned).

Note: I am unclear which of the previous features are planned and which are currently working.

<a name="mailpile"></a>Mailpile
-----------------------------------------------------------

[mailpile.is](http://mailpile.is)

Mailpile is an email client designed to quickly handle large amounts of email and also support user-friendly encryption. The initial focus is on email, with plans to eventually support post-email protocols like bitmessage, flowingmail, or darkmail. Also, the developers hope to add support for XMPP-based chat in the future. Since the Mozilla foundation has not committed the resources necessary to keep Thunderbird contemporary, the Mailpile initiative holds a lot of promise as a cross-platform mail client that seeks to redesign how we interact with email.

**Keys:** Mailpile email encryption is based on OpenPGP (it uses your GPG keyring). Key discovery will be handled using OpenPGP keyservers and including public keys as attachments to outgoing email. Public keys are trusted on first use, with plans for validation via DANE and manual fingerprint verification (future support for a P2P protocol might include additional methods, such as Certificate Transparency or Short Authentication Strings). Currently, keys are not backed up.

**Application:** Mailpile UI is written using HTML5 and Javascript, running against a self-hosted Python application (that typically lives locally on the device, but might be running on your own server).

**Limitations:** Mailpile does not currently have a scheme for recovery if your device is destroyed or a method for securely synchronizing keys among devices. Although the search index is stored encrypted on disk (if the user already has GPG installed and a key pair generated), it is encrypted in a way that requires the index to be loaded entirely into memory. Mailpile is under very active development, so these and other issues may change in the near future.

* Written in: Python, Javascript
* Source code: http://github.com/pagekite/mailpile
* Design documentation: http://github.com/pagekite/mailpile
* License: AGPL & Apache
* Platforms: Windows, Mac, Linux (with Android and iOS planned).
* Contact: team@mailpile.is

<a name="parley"></a>Parley
-----------------------------------------------------------

[parley.co](https://parley.co)

Parley is a desktop mail client with a UI written using HTML5 and Javascript, with a local backend written in Python.

**Keys:** Although Parley can be used with any service provider, the Parley servers are used to publish public keys and back up client-encrypted private keys. For key discovery, Parley uses a central repository and the OpenPGP keyservers. For key validation, Parley relies on trust on first use and Parley key endorsement.

**Infrastructure:** All users of the Parley client also sign up for the Parley service, but they use their existing email account. The Parley server acts as a proxy that uses [context.io](http://context.io) for email storage (context.io is a commercial service that provides a REST API for a traditional IMAP account). The Parley server also handles key discovery, validation, and backup. Both the client and server are released as free software.

**Application:** Parley is currently bundled into an executable using [Appcelerator](http://www.appcelerator.com/). The Parley client does not speak IMAP or SMTP directly. Rather, uses the email REST API of context.io.

**Limitations:** All user email is stored by context.io, albeit in OpenPGP format. Metadata is exposed to context.io, however (in addition to your service provider).

* Written in: Python, Javascript
* Source code: https://github.com/blackchair/parley
* Design documentation: https://parley.co/#how-it-works
* License: BSD (with proprietary server-side dependencies)
* Platforms: Windows, Mac, Linux (with Android and iOS planned).

<a name="self-hosted-email"></a>Self-Hosted Email
===========================================================

Traditionally, email is a federated protocol: when you send an email it travels from your computer, to the server of your email provider, to the server of the recipient's provider, and finally to the recipient's computer. The key idea with self-hosted email is to cut out the middleman and run your own email server.

In the United States, much of the interest in self-hosted email is driven by the Supreme Court's current (and particularly odd) interpretation of the 4th amendment called the ["Third-Party Doctrine"](https://en.wikipedia.org/wiki/Third-Party_Doctrine). Essentially, you have much weaker privacy protections in the US if you entrust any of your data to a third party. Additionally, the Court has so far afforded much greater protections to items physically inside your home. "Aha!" say the hackers and the lawyers, "we will just put email in the home."

Unfortunately, it is not so simple. There are some major challenges to putting email servers in everyone's home:

* **Delegated reputation**: The current email infrastructure is essentially a system of delegated reputation. In order to be able to send mail to most providers and not have a large percentage of it marked as Spam, a service provider must gradually build up a good reputation. Users are able to send mail because their provider has cultivated this reputation and maintained it by closing abusive accounts. It is certainly possible to run an email provider with a single user, but it is much harder to build up a good reputation. Also, many email providers block all relay attempts from IP addresses that have been flagged as "home" addresses, on the (probable) assumption that the message is coming from a virus and not a legitimate email server.
* **Servers are on a hostile network**: Because a server needs to have open ports that are publicly accessible from the internet at all times, running one is much trickier than a simple desktop computer. It is much more critical to make sure security upgrades are applied in a timely manner, and that you are able to respond to external attacks, such as "Spam Bombs". Any publicly addressable IP that is put on the open internet will be continually probed for vulnerabilities. Email is a protocol that is wide open and prone to abuse.
* **Sysadmins are not robots**: No one has yet figured out how to make self-healing servers that don't require a skilled sysadmin to keep them healthy. Once someone does, a lot of sysadmins will be out of work, but they are presently not very worried. There are many things that commonly go wrong with servers, such as upgrades failing, drives filling up, daemons crashing, memory leaks, hardware failures, and so on.
* **Does not address the important problems**: Moving the physical location of a device does nothing to solve the hard problems associated with easy-to-use email security (such as data availability and key validation). Some of the approaches to these problems rely on service provider infrastructure that would be infeasible to self host.
* **DNS is hard**: One of the important security problems with traditional email is the vulnerability MX DNS records. Doing DNS correctly is hard, and not something that can be expected of the common user.

All of the above problems however do not apply for public-key routing mail systems such as secushare, Retroshare, Briar, I2PBote and Pond. The latter actually needs a server to function, so a self-hosted home server is very recommendable. The others can be operated from all kinds of devices including home server boxes.

<a name="self-hosted-dark-mail"></a>Dark Mail Alliance
-----------------------------------------------------------

The Dark Mail Alliance has said they want to support self-hosting for the server component of the system. No details yet.

<a name="freedombox"></a>FreedomBox
-----------------------------------------------------------

[freedomboxfoundation.org](https://freedomboxfoundation.org)

From its early conception, part of FreedomBox was "email and telecommunications that protects privacy and resists eavesdropping". Email, however, is not currently being worked on as part of FreedomBox. (as far as I can tell).

<a name="self-hosted-mailpile"></a>Mailpile
-----------------------------------------------------------

Although Mailpile is primarily a mail client, the background Python component can read the Maildir format for email. This means you could install Mailpile on your own server running a Mail Transfer Agent (MTA) like postfix or qmail. You would then access your mail remotely by connecting to your server via a web browser.

<a name="kinko"></a>kinko
-----------------------------------------------------------

[kinko](https://kinko.me) implements an en/decrypting SMTP- and IMAP-proxy on ARM-class hardware, the kinko box. Emails are synced from the users' email accounts via IMAP to the box and are stored in plaintext in a secure storage area on the box. The kinko box also includes a webmailer to be able to use email with the browser.

Connections to the kinko box are secured by TLS using a private key only known to the box itself. Furthermore, the kinko box is tunnelled to a public internet location. Consequently, users can access secure email from everywhere, using IMAP compatible email clients and/or browsers, including mobile clients.

kinko uses GnuPG for encryption, with the addition of encrypting the email subject. Further additions should allow "Post-email alternatives" (a la bitmessage) to be used with the email clients that users are using today already. Other, privacy-related additions are planned as well.

**Key discovery and validation:** Users can upload existing PGP keyrings. PGP keys are discovered via email
addresses, email content, and PGP key servers. Keys are trusted on first use (but this policy can be changed
to explicit fingerprint validation.)

**Project status:** An alpha prototype exists. We are preparing for the release of a beta package in Q2/2014.

**Languages:** The kinko base system is implemented in ruby and shell, with minor portions in native code.
Applications can be implemented in more or less any language.

**Webmail:** The currently included webmail application is roundcube webmail. That might change in the future.

**Licenses:** All portions of the kinko system will be released under the AGPL license. (Included 3rd party
applications will use their respective open source licenses). The hardware is open sourced as
per [olimex](https://www.olimex.com/wiki/A10-OLinuXino-LIME).

<a name="email-infrastructure"></a>Email Infrastructure
===========================================================

The "infrastructure" projects give a service provider the opportunity to offer secure email accounts to end-users. By modifying how both email clients and email servers work, these projects have the potential to deploy greater security measures than are possible with a client-only approach. For example:

* Encrypted relay: A secure email provider is able to support, and enforce, encrypted transport when relaying mail to other providers. This is a mechanism for reducing likelihood of surveillance of metadata (which is otherwise not protected by OpenPGP client-side encryption of message contents), if the counterparts of your communication aren't using offerings affected by the PRISM surveillance program, possibly including cheap VPS hosted servers.
* Easier key management: A secure email provider can endorse the public keys of its users, and provide assistance to various schemes for automatic validation. Additionally, a secure email provider, coupled with a custom client, can make it easy to securely manage and back up the essential private keys which are otherwise cumbersome for most users to manage.
* Invisible upgrade to better protocols: A secure email provider has the potential to support multiple protocols bound to a single user, allowing automatic and invisible upgrades to more secure post-email protocols when both parties detect the capability.
* A hope for federation: The recent concentration of email to a few giant providers greatly reduces the health and resiliency of email as an open protocol, since now only a few players essentially monopolize the medium. Projects that seek to make it easier to offer secure email as a service have the potential to reverse this trend.
* Better DNS: A secure provider can support DNSSEC and DANE, while most other email providers are unlikely to anytime soon. This is important, if you really insist on using traditional email, because it is easy to hijack the MX records of a domain without DNSSEC.
* Minimal data retention: A service provider that follows "best practices" will choose to retain less personally identifiable information on their users, such as their home IP addresses.

The goal of both projects in this category is to build systems where the service provider is untrusted and cannot compromise the security of its users.

Despite the potential of this approach, there are several unknown factors that might limit its appeal:

* In order to benefit from a more secure provider, a user will need to switch their email account and email address, a very high barrier to adoption.
* Where once there were many ISPs that offered email service, it is no longer clear if there is either the demand to sustain many email providers or the supply of providers interested in offering email as a service.
* Given that more radical new mail systems solve all of the above problems, it may be a waste of time to stick to the old traditions.
* Users must download and install a custom application.

<a name="dark-mail-alliance"></a>Dark Mail Alliance
-----------------------------------------------------------

[darkmail.info](https://darkmail.info)

The Dark Mail Alliance will include both a client application and server software. The plan is to support traditional encrypted email (both OpenPGP and S/MIME), a new federated email-like protocol adapted from SilentCircle's instant message protocol (SCIMP), and a pure peer-to-peer messaging protocol. Both the client and server will be made available as free software.

**Keys:** Key pairs will be generated on the user's device and uploaded to the service provider. [Certificate Transparency](http://certificate-transparency.org) will be used to automatically validate the service provider's endorsement of these public keys. Dark Mail additionally plans to support fingerprint confirmation, short authentication strings, and shared secret for manual key validation. Automatic discovery of public keys will happen using DNS, HTTPS, and via the messages themselves.

**Routing:** The post-email messaging protocol promises to have forward secrecy and protection from metadata analysis (details have not yet been posted, and SCIMP does not currently support meta-data protection). Dark Mail Alliance plans to additionally support pure peer-to-peer messaging using a key fingerprint as the user identifier.

**Infrastructure:** Dark Mail plans to support three types of architectures: traditional client/server, self-hosted, and pure peer-to-peer. No details yet on how these will work.

**Application:** The client application will work with any existing MUA by exposing a local IMAP/SMTP server that the MUA can connect to.

**Limitations:** Dark Mail has not yet released any code or design documents. However, they certainly have the resources to carry out their plans.

* Written in: C
* Source code: none yet
* Design documentation: none yet
* License: planned to be OSI-compatible
* Platforms: initially Android and iOS, followed by Windows, OS X, Linux, and Windows Phone.
* Contact: press@darkmail.info

<a name="leap"></a>LEAP Encryption Access Project
-----------------------------------------------------------

[leap.se](https://leap.se)

LEAP includes both a client application and turn-key system to automate the process of running a secure service provider. Currently, this includes user registration and management, help tickets, billing, VPN service, and secure email service. The secure email service is based on OpenPGP.

**Keys:** Key pairs are generated on the user's device. Keys, and all user data, are stored in a client-encrypted database that is synchronized among the user's devices and backed up to the service provider. Keys are automatically validated using a combination of provider endorsement and network perspective (coming soon). Keys are discovered via the OpenPGP keyservers, the OpenPGP header, email footers, and a custom HTTP-based discovery protocol.

**Infrastructure:** LEAP follows a traditional federated client/server architecture. The client is designed to work with any LEAP-compatible service provider (with plans to support legacy IMAP providers in the future). For security reasons, users are encouraged to get the application from LEAP and not their service provider.

**Application:** The client application works with any existing MUA by exposing a local IMAP/SMTP server that the MUA can connect to. There is a Thunderbird extension to automate configuration of the account in Thunderbird. The client application communicates with the service provider using a custom protocol for synchronizing encrypted databases. The application is a very small C program that launches the Python code. The user interface is written using Qt.

**Limitations:** In the current implementation, security properties of forward secrecy and metadata protection are not end-to-end. Instead, the client relies on the service provider to ensure these properties. This limitation is due to some inherent limitations in the existing protocols for secure email. As with many of the other projects, LEAP's plan is to invisibly upgrade to a post-email protocol when possible in order to overcome these limitations.

* Written in: Python
* Source code: https://leap.se/source
* Design documentation: https://leap.se/docs
* License: mostly GPL v3, some MIT and AGPL.

<a name="public-key-routing"></a>Public-Key Routing Systems
===========================================================

Out of a tradition of peer-to-peer systems a new breed of Internet routing technologies is emerging which has quite interesting effects regarding possible solutions for email.
These technologies share some essential advantages:

1. **Public key as the identifier:** All of these projects use the user's public key as the unique routing identifier for a user, allowing for decentralized and unique names. This neatly solves the problem of validating public keys, because every identifier *is* the key, so there is no need to establish a mapping.
1. **Ease of use:** By having the cryptography an essential part of how the Internet works, applications hardly need to deal with it any longer. All communication is always encrypted to the recipients of the messages, allowing for dramatically simplified user interfaces that anyone can learn to use.
1. **Impossible to use without encryption:** Some researchers think the most awful problem about traditional email is that as long as it can accept unencrypted mail, people will send unencrypted mail and expose most private aspects of the recipient's life against her will. Public-key based routing systems make such profoundly human behaviour impossible and thus close the biggest of all loopholes in Internet technology.
1. **No account management:** Since public-key routing leads communications directly to the receiving endpoints, there is no need for any account registration on any servers. A business card containing your first contact's public key is sufficient to bootstrap your node and start using the new system. You just hold it in front of the webcam to access her profile, start a contact subscription or formulate a message.
1. **Trust no one:** These projects share an approach that treats the network, and all parties on the network, as potentially hostile and not to be trusted. With this approach, a user's security can only be betrayed if their own device is compromised or the software is flawed or tampered with, but the user is protected from attacks against any service provider (because there typically is not one).
1. **No authorities:** Since the cryptographic operations are self-validating, there is no need for neither certification nor domain naming authorities. The bureaucracy of having an identity on the Internet is gone.
1. **Potential for mesh networking:** If implemented properly, public-key based routing systems do not depend on the Internet's current routing methodology such as BGP, the Border Gateway Protocol. This means they can self-organize even if there was no IP-based routing underneath. These technologies can be deployed as an overlay over the existing Internet, but they can also find routes across a network of mesh nodes or an alternative Internet which models intercontinental underwater cabling as an unusually strong direct link between nodes that are actually very distant from each other. Many of these new tools have the ability to route by bluetooth or ad-hoc wireless proximity, allowing for independent email infrastructure in countries where the government has to be considered an opponent.

Disadvantages are:

1. You need to install custom new technology.
1. Several projects still have some specific drawbacks due to their youth.
1. Allowing for communication and data exchange even if sender and recipient aren't on the network at the same time takes special provisions in relay infrastructure that isn't yet implemented in any of the projects. This problem does not persist if at least one of the participants in a communication is always on (think of a smartphone flatrate or a router device at home).
1. To ensure safety in cryptographic communication it is required that you set up contacts in advance before you can communicate with them. This is a strength, but it may appear as a disadvantage at first.

When in their infancy, these projects usually start out with a pure peer-to-peer architecture, which has the drawbacks of being slow, CPU intensive and potentially less protective of the social graph than even a federated architecture. Projects that have come of age such as Tor and I2P however demonstrate how an infrastructure of collaborating relay nodes can provide for performance, metadata protection and avoid computational load on the endpoints. Strategies for organization of such relay infrastructure have been a hot topic of research for over a decade. Most of these infrastructures make use of distributed hashtable technology, which in its infancy was suffering of problems like lack of look-up privacy and [Sybil attacks](https://en.wikipedia.org/wiki/Sybil_attack). The research community has provided for viable strategies to solve these problems using reputation systems based on behavior or social graph and projects such as GNUnet and Tribler have implemented them.

It is important to understand that the way these new systems do not allow for traditional user@domain addressing is not a drawback, it is a strength since users are no longer required to possess a keyboard or even know how to read and write. It is true that you do not realistically want to add a contact by typing in their public key information. What you do is to use the methods described in Key Discovery.

<a name="briar"></a>Briar
-----------------------------------------------------------

Briar is a project working on a messaging and forum solution which distributes content opportunistically, that means whenever two nodes meet. It is designed to support the work of activists in oppressive countries by making use of wireless mesh networking between telephony devices. Briar actually doesn't use public keys for identification of users, each communication thread has its own ephemeral key which is advanced for each message, achieving an advanced kind of forward secrecy similar to Pond.

<a name="cables"></a>Cables
-----------------------------------------------------------

https://github.com/mkdesu/cables

* Written in: C, Bash
* License: GPL v2

<a name="cjdns-net2o"></a>cjdns and net2o
-----------------------------------------------------------

cjdns and net2o are public-key routing overlay networks for the Internet. They do not provide any custom email applications, so you have to combine them with running an email server on your personal device. They provide encryption for any kind of communication by default, but they do not deliver transaction data protection.

<a name="goldbug"></a>Goldbug
-----------------------------------------------------------

[Goldbug](http://goldbug.sf.net) uses opportunistic routing, too, but it currently offers no transaction data protection.

* Written in: C++, Qt
* License: BSD

<a name="i2pbote"></a>I2PBote
-----------------------------------------------------------
I2PBote is a messaging system on top of I2P. It has the appearance of a web mail interface but actually makes use of I2P's transaction data obfuscation capabilities embedded in its public-key based routing.

<a name="pond"></a>Pond
-----------------------------------------------------------

[pond.imperialviolet.org](https://pond.imperialviolet.org/)

Pond is an email-like messaging application with several unique architectural and cryptographic features that make it stand out in the field.

**Message Encryption**: Similarly to Briar, Pond uses [Axolotl](https://github.com/trevp/axolotl/wiki) for asynchronous forward secret messages where the key is frequently ratcheted (akin to OTR, but more robust).

**Routing**: Pond uses a unique architecture where every user relies on a service provider for receiving messages, but sent messages are delivered directly to the recipient's server using Tor's public-key routing capability better known as "hidden services." Transaction data protection is given not only by using Tor, but also in the way periodic meaningless transactions are performed. In order to prevent Spam under this scheme, Pond uses a clever system of group signatures to allow the server to check if a sender is authorized to deliver to a particular user without leaking any information to the server.

**Keys**: Pond uses PANDA, a method for secure peer discovery and validation using short authentication strings. This has the advantage of not appearing bureaucratic as shared secret exchanges on OTR do, since this method actually establishes the first contact. On the other hand it has the disadvantage of currently operating on a centralized service.

Pond's advantages include:

* Very high security: forward secrecy, metadata protection, resistance to traffic analysis.
* Pond hybrid federated and public-key routing approach is a reasonable interim solution until public-key routing provide for message availability themselves.
* Written in Go, and thus probably has many fewer security flaws than programs written in C or C++.
* Pond is written by Adam Langley, an extremely well respected crypto-engineer.
* The usability of Pond is imperfect, but already superior to most PGP interfaces.

Pond's disadvantages include:

* It maintains the old-fashioned dependency on servers, and since maintaining a Pond server is rather unusual, the architecture is prone to generating few very popular servers which then constitute honeypots and single points of failure.
* Pond is not architected for group communications and would scale worse than regular email at that.
* Pond's architecture does not allow to evolve into instant messaging or social networking, instead it imposes a deceleration of human interactions which can be considered a strength in the eyes of the 'slow tech' community. Some say the name stems from the time it gives you to ponder about what you will write.
* It does not provide support for generation or acquisition of QR codes, yet.
* Pond is currently rather difficult to install.

Pond is an exciting experiment in how you could build a very secure post-email protocol. Currently, Pond uses Tor to anonymize message routing, but the Tor network was designed for low-latency. Pond could potentially use a more secure anonymization network that was designed for higher-latency asynchronous messages.

* Written in: Go
* Source code: https://github.com/agl/pond
* License: BSD
* Platforms: anything you can compile Go on (for command line interface) or anything you can compile Go + Gtk (for GUI interface).

<a name="retroshare"></a>Retroshare
-----------------------------------------------------------

Retroshare is a complex and somewhat confusing application that provides forums, subscription channels, file sharing, chats and a mail interface that looks very familiar to traditional email users. In practice it is an impressive one stop shop for peer-to-peer group collaboration where everything is end-to-end encrypted and forward secret where applicable.

Retroshare has no relay network, so it operates its own distributed hashtable from each node. This means it has the typical deficits of P2P technology: slow and continously making use of power and bandwidth. It also deficits in metadata protection since nodes typically interact directly with other nodes, thus exposing the social graph. All of these problems can be solved by combining Retroshare with a Tor hidden service. In that case the built-in DHT is disabled and Retroshare operates on top of Tor's public-key routing infrastructure.

One annoying bug still persists: When connecting a Retroshare node using the TLS protocol, a certificate is returned that exposes the user name provided by its user. This means you should always use a pseudonym you have never used in any other context before to avoid getting socially graphed.

<a name="secushare"></a>secushare
-----------------------------------------------------------

[Secure Share](http://secushare.org) has been cited in a research paper as the most advanced and ambitious of projects aimed at providing a distributed social network, which naturally includes a mail system. secushare is architected on top of a publish/subscribe paradigm, which hides the complexity of multicast data store and forward distribution trees operating within the [GNUnet](https://gnunet.org) public-key routing framework. This means, that if everything goes well, resulting applications will have the scalability properties of cloud technology although they are actually operating on voluntarily contributed infrastructure.

The aim is to offer the full breadth of Facebook-like social services in a distributed manner, letting each endpoint have a view of its neighboring social graph and hosting profile and microblogging data. This data is then used to impede sybil attacks, using methods agreed upon in several research papers, to allow for social adoption of contacts, freeing the majority of users from the hassle with public key crypto, and could possibly help in the improvement of quality of obfuscation circuit construction, as also suggested by some research work. The integration of mail and social networking is therefore not only natural from a usability perspective, it also makes sense from a security standpoint.

The built-in mail system integrates synchronous and group messaging naturally. Encryption happens automatically using ephemeral ratchets for each pubsub channel. Channels can contain an unlimited number of recipients, but they can be as small as two people or just two devices of the same person, allowing for synchronization. Data availability is achieved by having relay nodes store messages until they can be delivered. Relay nodes are chosen in a strategically unpredictable manner akin to Tor's EntryNodes so that honeypots and single points of failure are avoided. A more flexible kind of onion routing is planned for transaction data protection, allowing the user to choose a trade-off between privacy and convenience themselves. But even if they choose convenience, they will experience better protection than what they get from email today, and they will provide cover traffic to those in need of better privacy.

GNUnet supports secushare with fully Internet-independent routing, capable of running over custom infrastructure and mesh networks, and a highly innovative look-up-privacy-protecting cryptographic name resolution mechanism on top of hardened DHT technology, called GNS. secushare uses PSYC as its higher level social messaging protocol syntax, drawing from a long history of experience in efficient and extensible design of decentralized communication systems.

The big problem with secushare is that since its prototype in 2012 the code has been disassembled and is being reconstructed within the framework of GNUnet, so it's currently not available. The prototype was done in form of a native Qt/QML application, but a Javascript API for web-based UIs is also in the planning. Another issue is that GNUnet does not provide onion routing or other method of obfuscation as yet, and that a back-end of relay nodes similar to Tor's isn't available yet - thus, running GNUnet currently necessitates more local resources than it should.

* Written in: C
* Source code: https://gnunet.org/svn/gnunet/src/
* License: Affero GPL
* Platforms: anything

<a name="susimail"></a>Susimail
-----------------------------------------------------------

<a name="unclassified"></a>Unclassified
===========================================================

These projects use unusual approaches or haven't been categorized by the authors properly yet.

<a name="bitmessage"></a>Bitmessage
-----------------------------------------------------------

[Bitmessage](https://bitmessage.org)

Bitmessage is a peer-to-peer email-like communication protocol. It is totally decentralized and places no trust on any organization for services or validation.

Advantages:

* resistant to metadata analysis
* relatively easy to use
* works and is actively used by many people.

Disadvantages:

* unsolved scaling issues: all messages are broadcast to everyone
* no forward secrecy
* because there is no forward secrecy, it is especially problematic that anyone can grab an encrypted copy of any message in the system. This means if the private key is ever compromised, then all the past messages can be decrypted easily by anyone using the system.
* relies on proof of work for spam prevention, which is probably not actually that preventative (spammers often steal CPU anyway).

<a name="p2p-dark-mail-alliance"></a>Dark Mail Alliance
-----------------------------------------------------------

The Dark Mail Alliance plans to incorporate traditional email, a federated email alternative, and a second email alternative that is pure peer-to-peer. Details are not yet forthwith.

<a name="flowingmail"></a>FlowingMail
-----------------------------------------------------------

[FlowingMail](http://flowingmail.com)

P2P secure, encrypted email system.

<a name="related-works"></a>Related Works
===========================================================

There are many technologies that don't belong in this document because they either (a) are not trying to make encrypted email-like communication easier, (b) use some kind of weird proprietary escrow system, or (c) we just don't know enough about them yet. Here is a place to store links to such projects.

* [Virtru](https://www.virtru.com) has a secure email product that relies on a centralized key escrow. For details, see [here](http://www.theregister.co.uk/Print/2014/01/24/ex_nsa_cloud_guru_email_privacy_startup) and [here](https://www.virtru.com/how-virtru-works).
* [OpenCom](http://opencom.io) is a secure email and email-like communication in the planning stages.
* [Ubiquitous Encrypted Email](https://github.com/tomrittervg/uee) is a protocol draft for standards that could lead to universal adoption of encrypted email.
* [Redecentralize](https://github.com/redecentralize/alternative-internet) has a list of decentralized networks, such as Tor.
* [#youbroketheinternet](http://youbroketheinternet.org) is a platform that explains and promotes public-key based routing technologies for a new Internet (usually spelled GNU for its strict political requirements) and organizes them on an architectural map. It has dozens of videos from the project presentations held at events it hosts, explaining the various projects in detail. You should particularly appreciate the one with Jacob Appelbaum presenting Pond.

