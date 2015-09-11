Title: Verifying a self-signed SSL cert
Date: 2015-09-11
Category: Blog
Author: Joshua Ryan Smith
Summary: Adeventures in verifying the identity of an SSL/TLS connection which uses a self-signed x.509 certificate.


A few months ago, my buddy and I started a little side project for which we needed a few private repos on GitHub.
In order to save money on the $25/month GitHub organizational plan, we recently moved these repos from GitHub to my buddy's personal server running an instance of gitlab.
For security, my buddy deployed a self-signed x.509 certificate to encrypt the connection to the server over SSL/TLS.

Every time I tried to connect to the server, my browser would pop up a warning telling me the identity of the server was unknown.
The popup was annoying, but the annoyance wasn't a dealbreaker.
The real issue was: how do I know that the server to which I'm connecting is actually my buddy's server?
Trying to address this issue led me to the more general question of: is there a framework outside the traditional certificate authority (CA) model in which this process is more automated and less error prone?


Background: How do I know my information is going to who I think its going to on the interweb?
==============================================================================================
Before going into the details of how I verified my buddy's server, I want to give a little background on internet security.
Imagine you open your web browser and type in a URL like "facebook.com".
You press the `Return` key and in a few milliseconds, your Facebook page pops up.
Your computer sent some bits out over the network connection to the internet, and some bits representing your Facebook page came back.
Who sent those bits?
Are you sure it was Facebook's server?
When you send a very private message to your spouse/significant other/child/best friend over this connection to this "Facebook", how do you know that some nefarious person isn't reading your very private messages by spoofing your Facebook page?
Even if a nefarious person is not spoofing your Facebook page, how do you know that a nefarious person cannot intercept your very private message and read it as it makes its way to Facebook's server?

The short answer is cryptography via a protocol called SSL/TLS.

The bottom line is the SSL/TLS protocol and careful security engineering on Facebook's part ensures that your browser can positively identify that the data coming from "facebook.com" indeed comes from Facebook the company and not someone spoofing their server.
Furthermore, the SSL/TLS protocol and Facebook's security practices ensure that any data intercepted between your browser and Facebook's servers cannot be read or understood by a nefarious man-in-the-middle; it is encrypted and will appear to be an utter nonsense set of random data.
I won't go into any technical detail about how this process or the process of encryption/identity verification works because its beyond the scopep of this discussion.
You should read up on it though, because they are important issues.

This system works because your browser ships with so-called "root certificates" issued by trusted third parties like Verisign.
These root certificates, along with audits by Verisign and similar companies, are used to verify the authenticity of the certificates Facebook (for example) uses to create the secure SSL/TLS connection.

On the other hand, there is nothing special about the x.509 cert that Facebook holds other than Verisign, etc. helped issue it.
You could generate your own x.509 certificate and the SSL/TLS protocol would work just as well.
This practice is known as creating a self-signed certificate, and is common.
There are a number of legitimate reasons why someone would want to issue a self-signed certificate.
First, leaving the server identity issue aside, SSL/TLS ensures an encrypted connection; it is a good practice to encrypt all data across the internet.
Second, acquiring a signed certificate from a CA is expensive.
There's no sense in spending money if all you want is to set up a little hobby server with a secure connection.
Third, getting a cert from a CA requires the CA to perform some kind of background check.
In this case, you would have to divulge a lot of personal data to the CA which raises some privacy issues.
For example, what happens if the CA itself doesn't have good internal security practices and your personal data is leaked (a la Target, Home Depot, Ashley Madison, etc.)?


Look, there's another issue which is you want to verify the identity of the server but not necessarily the identity of the individual running the server.


The only issue is what I mentioned in the top of the post: how do I know the data I'm sending to a server holding a self-signed certificate is actually my buddy's server?



It turns out that verifying the identity of my buddy's server was more involved than I would have liked.
I had to actually type more than one command into the command line, send an email to my buddy, he had to log into his server and type several commands into the command line, and then respond with another email.
There were an unacceptably large number of manual steps in this scenario.
When this many manual steps are required, security will end up suffering because nobody is going to take the time to do them all.




Here I need to address the following questions:

* How is the identity of a website assured?
* What are the benefits of SSL/TLS?
* Argue that basically all web traffic should occur over a secure connection. Mention that the big internet players (Google, Twitter, Facebook, GitHub, etc.) employ a ubiquitous secure connection.
* Why use a self-signed cert?
* Security vs. ease of use.


Specifics: How did I verify the identity of my buddy's server?
==============================================================


Proposal: Web-of-trust approach applied to SSL/TLS
==================================================


Footnotes/bibliography
======================
