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

1. Who sent those bits?
2. Are you sure it was Facebook's server?
3. When you send a very private message to your spouse/significant other/child/best friend over this connection to this "Facebook", how do you know that some nefarious person isn't reading your very private messages by spoofing your Facebook page?
4. Even if a nefarious person is not spoofing your Facebook page, how do you know that a nefarious person cannot intercept your very private message and read it as it makes its way to Facebook's server?

The short answer is cryptography via a protocol called SSL/TLS, a.k.a. x.509.

A good implementation of the SSL/TLS protocol takes care of #4 in the above list because traffic over an SSL/TLS connection is encrypted using a special file on the server called a certificate, or cert for short.
Thus, a man-in-the-middle listening to data from your computer to the server sees what appears to be random nonsense.

Issues 1-3 in the above list are different manifestations of the same issue: who is on the other end of the connection?
It is entirely possible that someone is spoofing the server to which you are trying to connect *and* is implementing SSL/TLS.
In this case, your data is encrypted as it travels to the spoofer; men-in-the-middle can't read your data, but the spoofer [still can](http://sadtrombone.com).

The solution to this problem requires broader coordination; you need some kind of assurance that the server to which you are connecting is the server you think it is.
This assurance can either come from the operator of the server itself, or from some trusted third party.
In the case of my buddy and me, I had to get that assurance directly from my buddy.
In the case of Facebook, Twitter, Google, GitHub, etc. that assurance comes from a trusted third party.

The bottom line is the SSL/TLS protocol and careful security engineering on Facebook's part ensures that your browser can positively identify that the data coming from "facebook.com" indeed comes from Facebook the company and not someone spoofing their server.
This system works because your browser ships with so-called "root certificates" issued by trusted third parties like Verisign.
Organizations like Verisign are called Certificate Authorities, or CAs for short.
These root certificates, along with audits by Verisign and similar companies, are used to verify the authenticity of the certificates Facebook (for example) uses to create the secure SSL/TLS connection.
These built-in root certs represent the broader coordination mentioned above: the CAs use the private component of their root cert to "sign" Facebook et.al.'s SSL/TLS cert.
In addition, the public component of the CAs root certs come packaged with your browser.
Your browser can therefore use the public component of the root certs it has to verify that a server is sending a cert that was signed by one of the root certs.


Self-signed certs and the little guy
------------------------------------
There is nothing special about the x.509 cert that Facebook holds other than Verisign, etc. helped issue it.
You could generate your own x.509 certificate and the SSL/TLS protocol would work just as well.
This practice is known as creating a self-signed certificate, and is common.
There are a number of legitimate reasons why someone would want to issue a self-signed certificate.
First, leaving the server identity issue aside, SSL/TLS ensures an encrypted connection; it is a good practice to encrypt all data across the internet.
In fact, nowadays all major internet companies encrypt their data *by default* via SSL/TLS [1]; doing so is considered a best practice and you should be skeptical of any service that doesn't.
Second, acquiring a signed certificate from a CA is expensive.
There's no sense in spending money if all you want is to set up a little hobby server with a secure connection.
Third, getting a cert from a CA requires the CA to perform some kind of background check.
In this case, you would have to divulge a lot of personal data to the CA which raises some privacy issues.
For example, what happens if the CA itself doesn't have good internal security practices and your personal data is leaked (a la Target, Home Depot, Ashley Madison, etc.)?


The problem: it isn't straightforward to verify a self-signed cert
==================================================================
At this point you should be convinced that it isn't technically too difficult to set up a self-signed cert on your webserver, SSL/TLS traffic is secure end-to-end, and there are legitimate reasons for deploying a self-signed certificate.
The big problem is: how do I know the server holding a self-signed cert and to which I'm sending data is actually my buddy's server?

It turns out that verifying the identity of my buddy's server was more involved than I would have liked.
I had to actually type more than one command into the command line, send an email to my buddy, he had to log into his server and type several commands into the command line, and then respond with another email.
There were an unacceptably large number of manual steps in this scenario.
When this many manual steps are required, security will end up suffering because nobody is going to take the time to do them all.


Specifics: How did I verify the identity of my buddy's server?
==============================================================


Proposal: Web-of-trust approach applied to SSL/TLS
==================================================


Footnotes/bibliography
======================
[1] If you are interested, [this website]() has a good list of companies that use SSL/TLS by default and which don't.
