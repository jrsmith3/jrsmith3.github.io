Title: Verifying a self-signed SSL cert
Date: 2015-09-11
Category: Blog
Author: Joshua Ryan Smith
Summary: Adeventures in verifying the identity of an SSL/TLS connection which uses a self-signed x.509 certificate.

tl;dr
=====
[Skip straight to the CLI commands]() to manually verify the identity of a server using its x.509 certificate.


Motivation
==========
A few months ago, my buddy Marty and I started a little side project for which we needed a few private repos on GitHub.
In order to save money on the $25/month GitHub organizational plan, we recently moved these repos from GitHub to Marty's personal server running an instance of gitlab.
For security, Marty deployed a self-signed x.509 certificate to encrypt the connection to the server over SSL/TLS.

Every time I tried to connect to the server, my browser would pop up a warning telling me the identity of the server was unknown.
The popup was annoying, but the annoyance wasn't a dealbreaker.
The real issue was: how do I know that the server to which I'm connecting is actually Marty's server?
Trying to address this issue led me to the more general question of: is there a framework outside the traditional certificate authority (CA) model in which this process is more automated and less error prone?
Ultimately, I think the most important issue raised by this exercise was: how is semi-anonymity facilitated while enjoying the encryption and verification benefits of the SSL/TLS protocol?


Background: How do I know my information is going to who I think its going to on the interweb?
==============================================================================================
Before going into the details of how I verified Marty's server, I want to give a little background on internet security.
Note that I am not an internet security expert, so take everything here with a grain of salt.
Imagine you open your web browser and type in a URL like "facebook.com".
You press the `Return` key and in a few milliseconds, your Facebook page pops up.
Your computer sent some bits out over the network connection to the internet, and some bits representing your Facebook page came back.

1. Who sent those bits?
2. Are you sure it was Facebook's server?
3. When you send a very private message to your spouse/significant other/child/best friend over this connection to this "Facebook", how do you know that some nefarious person isn't reading your very private messages by spoofing your Facebook page?
4. Even if a nefarious person is not spoofing your Facebook page, how do you know that a nefarious person cannot intercept your very private message and read it as it makes its way to Facebook's server?

The short answer is cryptography via two protocols called [SSL/TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security), and [x.509](https://en.wikipedia.org/wiki/X.509).

A good implementation of the SSL/TLS protocol takes care of #4 in the above list because traffic over an SSL/TLS connection is encrypted using a special file on the server called a certificate, or cert for short.
Thus, a man-in-the-middle listening to data from your computer to the server sees what appears to be random nonsense.

Issues 1-3 in the above list are different manifestations of the same issue: who is on the other end of the connection?
It is entirely possible that someone is spoofing the server to which you are trying to connect *and* is implementing SSL/TLS.
In this case, your data is encrypted as it travels to the spoofer; men-in-the-middle can't read your data, but the spoofer [still can](http://sadtrombone.com/?autoplay=true).

The solution to this problem requires broader coordination; you need some kind of assurance that the server to which you are connecting is the server you think it is.
This assurance can either come from the operator of the server itself, or from some trusted third party.
In the case of Marty and I, I had to get that assurance directly from Marty.
In the case of Facebook, Twitter, Google, GitHub, etc. that assurance comes from a trusted third party.

The bottom line is the SSL/TLS protocol and careful security engineering on Facebook's part ensures that your browser can positively identify that the data coming from "facebook.com" indeed comes from Facebook the company and not someone spoofing their server.
This system works because your browser ships with so-called "root certificates" issued by trusted third parties like VeriSign.
Organizations like VeriSign are called Certificate Authorities, or CAs for short.
These root certificates, along with audits by VeriSign and similar companies, are used to verify the authenticity of the certificates Facebook (for example) uses to create the secure SSL/TLS connection.
These built-in root certs represent the broader coordination mentioned above: the CAs use the private component of their root cert to "sign" Facebook et.al.'s SSL/TLS cert.
In addition, the public component of the CAs root certs come packaged with your browser.
Your browser can therefore use the public component of the root certs it has to verify that a server is sending a cert that was signed by one of the root certs.


Self-signed certs and the little guy
------------------------------------
There is nothing special about the x.509 cert that Facebook holds other than VeriSign, etc. helped issue it.
You could generate your own x.509 certificate and the end-to-end encryption of traffic over SSL/TLS protocol would work just as well.
This practice is known as creating a self-signed certificate, and is common.
There are a number of legitimate reasons why someone would want to issue a self-signed certificate.
First, leaving the server identity issue aside, SSL/TLS ensures an encrypted connection; it is a good practice to encrypt all data across the internet.
In fact, nowadays all major internet companies encrypt their data *by default* via SSL/TLS; doing so is considered a best practice and you should be skeptical of any service that doesn't.
Second, acquiring a signed certificate from a CA is expensive.
There's no sense in spending money if all you want is to set up a little hobby server with a secure connection.
Third, getting a cert from a CA requires the CA to perform some kind of background check.
In this case, you would have to divulge a lot of personal data to the CA which raises some privacy issues.
For example, what happens if the CA itself doesn't have good internal security practices and your personal data is leaked (a la Target, Home Depot, Ashley Madison, etc.)?


Semi-anonymity
--------------
The most important issue that is not well addressed in the CA framework of SSL/TLS is semi-anonymity.
Let's say you have an associate you know as [Mr. X]().
You don't know X's identity, but there's some amount of trust you have in his work.
Mr. X finds it unacceptable to divulge his identity to a corporate CA, but he needs to set up a website that employs both the encryption and identity features of SSL/TLS.
Encryption is relatively easy: X sets up a self-signed x.509 cert.
Identity is much more tedious because it looks like the steps listed below.
In my opinion, this issue of semi-anonymity is the edge case that needs a better solution.
I may not know exactly who X is, but I automatically want to be sure that my browser is connecting to his server.


The problem: it isn't straightforward to verify a self-signed cert
==================================================================
At this point you should be convinced that it isn't technically too difficult to set up a self-signed cert on your webserver, SSL/TLS traffic is secure end-to-end, and there are legitimate reasons for deploying a self-signed certificate.
The big problem is: how do I know the server holding a self-signed cert and to which I'm sending data is actually Marty's server?

It turns out that verifying the identity of Marty's server was more involved than I would have liked.
I had to actually type more than one command into the command line, send an email to Marty, he had to log into his server and type several commands into the command line, and then respond with another email.
There were an unacceptably large number of manual steps in this scenario.
When this many manual steps are required, security will end up suffering because nobody is going to take the time to do them all.


Specifics: How did I verify the identity of Marty's server?
===========================================================
This process was implemented over the following steps.
The upshot is that I had an excuse to use the `openssl` command in the terminal; this exercise demystified a lot of the SSL/TLS framework for me.

1. Acquire Marty's cert and associated metadata

```
$ openssl s_client -showcerts -connect git.schmarty.net:443 | openssl x509 -text -fingerprint
```

In this command, I am calling the `openssl` program twice, piping the output of one command into another.
The first time I am invoking the `s_client` option to show the certificate (`-showcerts`) after connecting to Marty's server (`-connect` command) on the TLS port 443.
The second `openssl` command is to manage the `x509` cert, returning it as `-text` and calculating its `-fingerprint`.

The above command yielded the following data:

```
Certificate:
    Data:
        Version: 1 (0x0)
        Serial Number: 13286215849167086773 (0xb8621eebe2c4e4b5)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=US, ST=Maryland, L=Baltimore, O=SCHMARTY DOT NET, CN=SCHMARTY DOT NET
        Validity
            Not Before: Oct 15 15:57:00 2014 GMT
            Not After : Oct 14 15:57:00 2019 GMT
        Subject: C=US, ST=Maryland, L=Baltimore, O=SCHMARTY DOT NET, CN=*.schmarty.net
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:d7:a3:a8:ff:70:df:53:3f:c1:4f:6a:86:99:ec:
                    16:4a:64:69:32:fc:33:38:d1:b4:7f:02:f3:3e:5f:
                    8a:8f:60:fe:ab:9c:76:08:e5:53:83:6a:37:f7:97:
                    6a:33:ad:b4:bd:c4:a7:ab:2f:ff:e4:24:7d:8b:86:
                    7a:da:3a:a1:b0:a6:b6:d0:49:ea:9d:49:4c:21:80:
                    14:48:df:d8:3d:9a:36:15:71:0e:99:7d:e0:a1:3b:
                    c0:fe:0f:b8:08:cc:9f:a8:2e:69:a2:a8:e7:9a:5d:
                    93:c5:94:d2:77:f4:dc:f6:c1:0c:d8:ca:48:69:f2:
                    c1:f1:04:4d:0d:b5:ac:9e:6b:1b:ef:4a:3c:67:88:
                    91:c6:e5:f5:6b:ca:be:bc:31:3d:9e:90:6e:f4:4f:
                    90:cd:f6:6b:57:c9:eb:97:1a:63:d9:e6:4a:c7:98:
                    6b:1d:76:6b:6e:4b:8b:c3:fc:a0:d1:4c:c4:23:39:
                    06:70:e5:88:b5:bf:06:36:25:6a:b3:db:0b:3c:c4:
                    c6:e1:5a:d3:f8:d1:21:05:93:60:0c:a6:04:e1:90:
                    21:3b:76:1e:c1:29:76:7a:33:67:a6:4f:73:2e:3c:
                    b4:73:b3:e7:01:93:49:c8:92:1a:48:72:25:0f:3b:
                    54:f4:7a:16:35:63:db:16:6e:e6:53:6c:19:ff:38:
                    1f:85
                Exponent: 65537 (0x10001)
    Signature Algorithm: sha256WithRSAEncryption
         36:f9:e7:ee:24:1c:98:ca:22:a8:d7:de:c0:7d:9e:8a:6e:3e:
         cd:dc:b4:e7:98:1b:c6:58:94:79:d1:be:b2:5d:a1:8c:33:97:
         6e:49:23:85:a9:cd:2c:32:85:00:7c:1b:e6:91:30:83:ef:c3:
         1b:e4:c8:be:ea:6f:61:b3:9f:e2:c2:c3:8b:27:6e:49:49:0f:
         0a:42:cf:e5:fd:4f:aa:a5:9a:03:ca:6b:70:14:5f:da:38:66:
         d4:ae:3f:90:ba:1d:b4:29:27:78:d7:0c:d6:31:72:10:86:df:
         10:2b:00:11:b9:4d:56:8f:c7:4f:d2:47:aa:d1:f8:09:eb:50:
         c0:69:9a:b7:34:e4:d0:84:9c:74:b5:82:36:59:58:00:ae:a7:
         9c:81:69:8b:0e:13:7d:3d:d7:12:fe:02:6e:64:75:83:81:d4:
         56:b7:7b:78:f9:bf:3d:45:1c:a8:a6:9a:14:20:ed:28:3d:17:
         59:2d:cb:ef:22:27:52:d5:10:03:77:5d:cc:be:d2:bf:43:4d:
         d5:a4:61:a7:db:6a:c9:43:2d:d3:06:af:48:2a:ec:00:27:11:
         c9:03:fa:2e:91:2a:06:95:5a:d0:9b:d0:3e:67:83:c9:ac:c5:
         ef:09:2f:e5:9a:9b:13:4e:65:2e:c6:55:b4:35:ea:06:75:6e:
         26:02:62:f5
SHA1 Fingerprint=C6:E2:AF:00:12:E6:F4:53:96:3F:F5:00:2D:4D:7D:C1:26:D9:F2:C8
-----BEGIN CERTIFICATE-----
MIIDTjCCAjYCCQC4Yh7r4sTktTANBgkqhkiG9w0BAQsFADBqMQswCQYDVQQGEwJV
UzERMA8GA1UECAwITWFyeWxhbmQxEjAQBgNVBAcMCUJhbHRpbW9yZTEZMBcGA1UE
CgwQU0NITUFSVFkgRE9UIE5FVDEZMBcGA1UEAwwQU0NITUFSVFkgRE9UIE5FVDAe
Fw0xNDEwMTUxNTU3MDBaFw0xOTEwMTQxNTU3MDBaMGgxCzAJBgNVBAYTAlVTMREw
DwYDVQQIDAhNYXJ5bGFuZDESMBAGA1UEBwwJQmFsdGltb3JlMRkwFwYDVQQKDBBT
Q0hNQVJUWSBET1QgTkVUMRcwFQYDVQQDDA4qLnNjaG1hcnR5Lm5ldDCCASIwDQYJ
KoZIhvcNAQEBBQADggEPADCCAQoCggEBANejqP9w31M/wU9qhpnsFkpkaTL8MzjR
tH8C8z5fio9g/qucdgjlU4NqN/eXajOttL3Ep6sv/+QkfYuGeto6obCmttBJ6p1J
TCGAFEjf2D2aNhVxDpl94KE7wP4PuAjMn6guaaKo55pdk8WU0nf03PbBDNjKSGny
wfEETQ21rJ5rG+9KPGeIkcbl9WvKvrwxPZ6QbvRPkM32a1fJ65caY9nmSseYax12
a25Li8P8oNFMxCM5BnDliLW/BjYlarPbCzzExuFa0/jRIQWTYAymBOGQITt2HsEp
dnozZ6ZPcy48tHOz5wGTSciSGkhyJQ87VPR6FjVj2xZu5lNsGf84H4UCAwEAATAN
BgkqhkiG9w0BAQsFAAOCAQEANvnn7iQcmMoiqNfewH2eim4+zdy055gbxliUedG+
sl2hjDOXbkkjhanNLDKFAHwb5pEwg+/DG+TIvupvYbOf4sLDiyduSUkPCkLP5f1P
qqWaA8prcBRf2jhm1K4/kLodtCkneNcM1jFyEIbfECsAEblNVo/HT9JHqtH4CetQ
wGmatzTk0IScdLWCNllYAK6nnIFpiw4TfT3XEv4CbmR1g4HUVrd7ePm/PUUcqKaa
FCDtKD0XWS3L7yInUtUQA3ddzL7Sv0NN1aRhp9tqyUMt0wavSCrsACcRyQP6LpEq
BpVa0JvQPmeDyazF7wkv5ZqbE05lLsZVtDXqBnVuJgJi9Q==
-----END CERTIFICATE-----
```

This data (e.g. the SHA1 fingerprint) matched what I saw when I connected to the server with Safari and examined the certificate.

2. I copied the above data (especially the part between the "BEGIN CERTIFICATE" and "END CERTIFICATE") and pasted it into a textfile with an email message to Marty.
I then signed this message with my [gpg key (200C61F9)](https://keybase.io/joshuarsmith/key.asc) because it seemed like the crypto thing to do.
Also I wanted to indicate that I wanted Marty to sign whatever response he sent back because I wanted to be sure that I wasn't experiencing a sophisticated spoofing attack (super low probability, but no sense in half-assing this security exercise).

3. Marty executed some commands on his hardware to verify the cert:

```marty@dogeland:~$ openssl x509 -noout -modulus -in saved_certificate.txt | openssl sha256
(stdin)= ef835bdf6080bfe6e73c8c81545e8088bf2169970a0a70ac3305a54267431a52
marty@dogeland:~$ openssl rsa -noout -modulus -in /path/to/gitlab/server.key | openssl sha256
(stdin)= ef835bdf6080bfe6e73c8c81545e8088bf2169970a0a70ac3305a54267431a52
```

Note that `saved_certificate.txt` contained the stuff between "BEGIN CERTIFICATE" and "END CERTIFICATE".

For fun, he also compared this with the server certificate file used by his web server:

```
marty@dogeland:~$ openssl x509 -noout -modulus -in /path/to/gitlab/server.crt | openssl sha256
(stdin)= ef835bdf6080bfe6e73c8c81545e8088bf2169970a0a70ac3305a54267431a52
```

We were both happy that the above results verified his server.


Reiteration: these steps are a pain in the ass
----------------------------------------------
Again, there are too many steps here.
Normal people won't take the time to figure out how to do them, even if they are willing to take the time to set up a self-signed x.509 cert for their server.
Pretty much the only reason I did them was out of boredom while waiting for my phone to get fixed at the Apple store.


95% Solution: Let's Encrypt
===========================
The key problem that needs to be solved is identity: was the self-signed x.509 cert issued by someone with a key I already trust?
Related, can the holder of that cert revoke it?

Fortunately, it looks like some of these issues will be resolved when the [Let's Encrypt]() project is out of beta.
It looks like Let's Encrypt will run a service that issues free certs that are signed by a root cert that most browsers already trust.
Let's Encrypt has a clever way to guarantee the owner of the domain is the operator of the server to which the domain resolves; I invite you to [read their description]() if you want to know the technical details.

The issue of facilitating semi-anonymity still exists in this scenario because Marty/Mr. X and I still have to exchange communication (via cryptographically signed messages) for me to know his server can be reached at a particular domain.
I think this problem could be solved by integrating Let's Encrypt and keybase.io:

1. A keybase.io user could first [verify their domain]().
2. Next, they would request a cert from Let's Encrypt.
3. Let's Encrypt would note that the domain was verified on keybase.io and embed that information into the cert it issues.

I'm not an expert at this kind of infrastructure, so a better implementation likely exists.

At this point, there should be enough information in the cert held by Marty's server that I can cryptographically convince myself that:

1. The connection is end-to-end secure.
2. The identity of the server at the other end is what it should be (avoiding man-in-the-middle).
3. This server actually belongs to Marty because his gpg signature appears in the cert.

The third item could be implemented in the `keybase` command line client and also appear on a user's keybase.io profile page.
