Title: Migrating a LaTeX manuscript hosted on GitHub to Authorea
Date: 2014-05-14 09:24
Category: Blog
Author: Joshua Ryan Smith
Summary: How to integrate Authorea with a LaTeX manuscript hosted in an existing GitHub repository.

Without having used it extensively, I am very excited about [Authorea](https://authorea.com/). Authorea looks like the collaborative authoring tool I would have created. It allows teams to collaborate on LaTeX documents, and it has a git backend that can be connected to a GitHub repository. My hope is that Authorea will bridge a gap: it will allow people with a lot of git and LaTeX experience to collaborate on manuscripts with people that have none. In this way, the more experienced members of the team can contribute to **project management (find a better word)** via a git workflow and LaTeX, and the less experienced members of the group aren't overwhelmed by the idiosyncracies of those tools. Longer term, perhaps the people with less experience will get a gentle introduction to version control and markup languages so they can see the value without having to pass through [the valley of death](http://software-carpentry.org/blog/2014/05/playing-the-kazoo.html#glass-law).

Authorea is currently thin on the documentation of how to connect to an existing LaTeX document hosted on GitHub. This post will be a case study of how I integrated Authorea into an existing LaTeX document on GitHub. I will use an example GitHub repo called [authorea_test](https://github.com/jrsmith3/authorea_test) and Authorea [document] so that you can poke around the result.

More motivation: I am currently writing a proposal with several professors; these professors range in age from mid-40s to 60s, and all are strictly MS Windows and MS Word people. Their typical document collaborative workflow is to email an MS Word .doc file around. I'm not going to go into the shortcomings of this workflow. I will say that one requirement we have is confidentiality: I don't want to write this document in public.

One wrinkle: I am *not* starting a document from scratch on Authorea and then inviting others to work on it. I already have a document in a git repository with 10s of commits. I want to enable collaborative editing on this document via Authorea.

I will also say one additional thing before I get started: don't get fooled by the false economy of "free." Both GitHub and Authorea offer exceptionally good services and have very low cost starter plans. Just spend the nominal money, make a private GitHub organization for your lab, and also buy some private repos on Authorea.

GitHub
======
Lets say you already have a LaTeX document hosted in a github [repo](https://github.com/jrsmith3/authorea_test). My typical workflow is to author or edit the LaTeX source, then build the result with a [makefile](https://github.com/jrsmith3/latex_template).

Step 1: Make a new article on Authorea
======================================
Click on the red "New article" button and choose "Import". The dialogue box will ask you for several things. For "Project name" I chose "authorea_test" to match the repo name on GitHub. For the LaTeX file in the "Source files" section, I navigated to the directory in my local filesystem containing the git repo and chose the `main.tex` file. I ended up choosing "Public" in the "Article availability" section for this example, but for my actual proposal I used "Private." Finally, click "Submit."

Upon uploading, I received an error (image). It looks like Authorea breaks up `main.tex` into sections according to the various parts of the document. For example there's an `abstract.tex`, `header.tex`, etc. It looks like the main part of the document is located in `untitled.tex`. For what its worth, the state of the repo can be found at commit [hex_number](). Here are a list of files:

- *bibliography* - A directory containing an empty file called `biblio.bib`. Probably my own bibTeX file would have gone here had I uploaded it during the creation of this document.
- *figures* - An empty directory.
- *abstract.tex* - A file containing the string between the `\begin{abstract}` and `\end{abstract}` directives in the original `main.tex` file.
- *header.tex* - Contains some directives pulled from 
- *layout.md* - Markdown file with a list of LaTeX files. I suppose this is the order in which Authorea assembles the various .tex files to be built.
- *pdftitleTitle_pdfaut.tex* - Some TeX directives from my original `main.tex`.
- *preamble.tex* - Looks like boilerplate LaTeX preamble. I suspect this is so Authorea can build the LaTeX.
- *sectionIntroduction_.tex* - `\section{}` command and text from the `Introduction` section.
- *title.tex* - Text of title of document.
- *untitled.tex* - Boilerplate empty .tex file.

There's probably a better way to prep your LaTeX to go into Authorea, but I suspect it will be easier after connecting it to the git repo.

Step 2: Connect Authorea document to GitHub
===========================================
Things in the Authorea document are a bit of a mess. We will first connect to the GitHub repo and then clean things up from GitHub.

Authorea SSH keys
-----------------
From the `main` tab on Authorea, click on the `Settings` button; once the "Article Settings" window pops up, click on the green "setup a deploy key" button. The next few pages walk you through the GitHub integration steps. One of the ways GitHub authenticates access to repositories is via ssh keys. Therefore, your git repo will need a public ssh key for which Authorea has the private key. To create this keypair, click on the "Generate ssh key pair" button. A new window will pop up entitled, "Public Deploy Key." Copy the entire block of text. Next, navigate over to the GitHub page with the repo containing your LaTeX manuscript. Click on the "Settings" tab. Then click on the "Deploy keys" tab on the next page that pops up. Click on "Add deploy key" and then paste the public ssh key that Authorea generated into the "Key" box. I named the key "authorea_auto_generated" but you can pick whatever name you want. Finally, click the green "Add key" button. GitHub may ask you to enter your password to add the public ssh key to the repository.

Here's a recap of what happened: Authorea generated a public/private ssh key pair for the `authorea_test` document. We copied the public key into the `authorea_test` GitHub repository. Now Authorea has access to the `authorea_test` repo on GitHub.

We aren't finished. Authorea needs to know the git URL for the GitHub repository, and the GitHub repository needs a webhook.

Git URL
-------
Before leaving GitHub, navigate back to the repository's main page and copy the "SSH clone URL." Now navigate to the Authorea page and close the ssh public key window. Copy the ssh clone URL into the box in step 2. Authorea now has everything it needs to modify the GitHub repo. Click "Submit." 

GitHub Webhook
--------------
If everything is successful, Authorea will give you a page with a Web Hook URL; copy the URL and navigate back to the GitHub page with the repo. Click on the "Settings" tab, but this time click on "Webhooks and Services." Click "Add webook." Paste the URL from Authorea into the "Payload URL" box, then click the green "Add webhook" button.

Once the webhook has been set up, Authorea pushes what it has to the GitHub repo and clobbers your stuff via a merge. You should go back to the local directory on your machine and pull in the changes.

Interlude
---------
At this point, the connection between Authorea and GitHub has been made. I'm pretty sure that every time a push is made to `master` on the GitHub repo, Authorea will pull in the changes. Likewise, any time edits are saved on Authorea, Authorea will push the changes to GitHub. I think that if a merge conflict arises, Authorea will push the changes to the branch `authorea` and require a manual merge.

Reorganizing what Authorea has done
===================================
First, we remove cruft by deleting `untitled.tex`. Then we need to adjust `layout.md` and the associated .tex files to get the document buildable from our local machine (thus we need to change the Makefile). Finally, we can delete `main.tex` as well.

Deleting `untitled.tex`, `pdftitleTitle_pdfaut.tex`, `main.tex`.

Here's what's happening. `layout.md` lists the order in which the files are built. Combining what's in `layout.md` with the rest of the files, I think the document gets built by parsing files in the following order:

preamble.tex
header.tex
title.tex
abstract.tex
sectionIntroduction_.tex

I think that between `title.tex` and `abstract.tex`, probably a `\maketitle` command is issued somewhere.
