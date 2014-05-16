Title: Migrating a LaTeX manuscript hosted on GitHub to Authorea
Date: 2014-05-14 09:24
Category: Blog
Author: Joshua Ryan Smith
Summary: How to integrate Authorea with a LaTeX manuscript hosted in an existing GitHub repository.

Summary
=======
- Start with a LaTeX document hosted in an existing GitHub repo.
- Create a new article on Authorea by importing the main LaTeX document source.
- Connect the Authorea document to the exisiting GitHub repo by
    - generating Authorea document ssh keys
    - setting up a GitHub webhook.
- Fix and reorganize the resulting files that Authorea creates.


Authorea + GitHub: extreme power plus low barrier to entry?
===========================================================
Without having used it extensively, I am very excited about [Authorea](https://authorea.com/). Authorea looks like the collaborative authoring tool I would have created. It allows teams to collaborate on LaTeX documents, and it has a git backend that can be connected to a GitHub repository. My hope is that Authorea will bridge a gap: it will allow people with a lot of git and LaTeX experience to collaborate on manuscripts with people that have none. In this way, hopefully the group can operate at the level of the most computer savvy members as opposed to regressing to the lowest-common-denominator .doc + email workflow. Longer term, perhaps the people with less experience will get a gentle introduction to version control and markup languages so they can see the value without having to pass through [the valley of death](http://software-carpentry.org/blog/2014/05/playing-the-kazoo.html#glass-law).

Authorea is currently thin on the documentation of how to connect to an existing LaTeX document hosted on GitHub. This post will be a case study of how I integrated Authorea into an existing LaTeX document on GitHub. I will use an example [GitHub repo called authorea_test](https://github.com/jrsmith3/authorea_test) and [Authorea document]() so that you can poke around the result.

Background
----------
I am currently writing a proposal with several professors; these professors range in age from mid-40s to 60s, and all are strictly MS Windows and MS Word people. Their typical document collaborative workflow is to email an MS Word .doc file around. I'm not going to go into the shortcomings of this workflow here. One requirement we have is confidentiality: I don't want to write this proposal in public.

Note that I am *not* starting a document from scratch on Authorea and then inviting others to work on it. I already have a document in a git repository with 10s of commits. I want to enable collaborative editing on this document via Authorea.

This post is verbose. Authorea's documentation is pretty much the quickstart version.


Step 0: Prep initial LaTeX manuscript on GitHub
===============================================
I started with a simple LaTeX document hosted in a GitHub [repo](https://github.com/jrsmith3/authorea_test/releases/tag/1.0). Note that the previous link is a tag which is the state of the repo right before I connected it to Authorea. My typical workflow is to author or edit the LaTeX source, then build the result with a [makefile](https://github.com/jrsmith3/latex_template). I would like to keep this makefile workflow, but I also want the files to be in a form that can be built by Authorea.

Make a branch to be merged later
--------------------------------
Spoiler alert: when you set up access to your GitHub repo by Authorea, Authorea is going to push to `master` and merge whatever it finds. I suggest moving whatever is currently in `master` to a new branch called `latex`, deleting `master`, and then merging the two on your own terms.


Step 1: Make a new article on Authorea via import
=================================================
Click on the red "New article" button and choose "Import". For "Project name" I chose "authorea_test" to match the repo name on GitHub. For the LaTeX file in the "Source files" section, I navigated to the directory in my local filesystem containing the git repo and chose the `main.tex` file. I ended up choosing "Public" in the "Article availability" section for this example, but for my actual proposal I used "Private." Finally, click "Submit."

![Import article]()

Authorea slices up `main.tex` and creates several .tex files. It also alerts me to a rendering error. This error is because Authorea incorrectly parsed my `\usepackage{hyperref}` directive. 

![Successful import with error]()

I'll fix the `hyperref` problem momentarily, but first take a look the files that Authorea has created by clicking on the `folder` tab.

![Folder tab after import]()

Authorea has parsed `main.tex` and broken out the sections into individual files listed below. Commit [commit_hash_value]() corresponds to the state of the repo at this point.

- **`bibliography`** - A directory containing an empty file called `biblio.bib`. Probably my own bibTeX file would have gone here had I uploaded it during the creation of this document.
- **`figures`** - An empty directory that would contain figures associated with this manuscript.
- **`abstract.tex`** - A file containing the string between the `\begin{abstract}` and `\end{abstract}` directives in the original `main.tex` file.
- **`header.tex`** - Contains the LaTeX commands that weren't ignored as part of the preamble or parsed into `pdftitleTitle_pdfaut.tex`.
- **`layout.md`** - Markdown file with a list of LaTeX files. This file appears to list the order in which Authorea assembles the various .tex files to be built.
- **`pdftitleTitle_pdfaut.tex`** - The `hyperref` package arguments. These commands are what is causing the rendering error.
- **`preamble.tex`** - Looks like boilerplate LaTeX preamble.
- **`sectionIntroduction_.tex`** - `\section{}` command and text from the `Introduction` section.
- **`title.tex`** - Text of title of document. Since I defined my own TeX command to hold the string containing the title, this file simply calls that command.
- **`untitled.tex`** - Empty .tex file.

Based on the above files, it looks like Authorea is building a document like the following:

```latex
\input{preamble}
\input{header}

\title{\input{title}}
\author{\AuthorName}

\begin{document}

\maketitle

\begin{abstract}
\input{abstract}
\end{abstract}

\input{sectionIntroduction_}

\end{document}
```


Step 2: Connect Authorea document to GitHub
===========================================
Things in the Authorea document are a bit of a mess. We will first connect to the GitHub repo, clean things up on our local machine, then push back up to GitHub.

Authorea ssh keys
-----------------
One of the ways GitHub authenticates access to repositories is via ssh keys. Authorea can generate key pairs for each document; once generated you simply copy your document's public key to the correspondig GitHub repo.

From the `main` tab on Authorea, click on the `Settings` button; once the "Article Settings" window pops up, click on the green "setup a deploy key" button. 

![Article Settings with deploy key button]()

To create this keypair, click on the "Generate ssh key pair" button. A new window will pop up entitled, "Public Deploy Key." Copy the entire block of text. 

![Authorea public deploy key]()

Next, navigate over to the GitHub page with the repo containing your LaTeX manuscript. Click on the "Settings" tab. Then click on the "Deploy keys" tab on the next page that pops up. 

![GitHub without public deploy key]()

Click on "Add deploy key" and then paste the public ssh key that Authorea generated into the "Key" box. I named the key "authorea_auto_generated" but you can pick whatever name you want. Finally, click the green "Add key" button. GitHub may ask you to enter your password to add the public ssh key to the repository.

![GitHub adding public deploy key]()
![GitHub with public deploy key]()

Here's a recap of what happened: Authorea generated a public/private ssh key pair for the `authorea_test` document. We copied the public key into the `authorea_test` GitHub repository. Now Authorea has push/pull access to the `authorea_test` repo on GitHub.

We aren't finished. Authorea needs to know the git URL for the GitHub repository, and the GitHub repository needs a webhook.

Git URL
-------
Before leaving GitHub, navigate back to the repository's main page and copy the "SSH clone URL." Now navigate to the Authorea page and close the ssh public key window. Copy the ssh clone URL into the box in step 2. Authorea now has everything it needs to modify the GitHub repo. Click "Submit."

![Authorea GitHub repo url]()

GitHub Webhook
--------------
If everything is successful, Authorea will give you a page with a Web Hook URL. 

![Authorea Git access bridge with webhook]()

Copy the URL and navigate back to the GitHub page with the repo. Click on the "Settings" tab, but this time click on "Webhooks and Services." 

![GitHub webhooks and services]()

Click "Add webook." Paste the URL from Authorea into the "Payload URL" box, then click the green "Add webhook" button.

![GitHub webhooks add webhook]()
![GitHub webhooks and services after webhook add]()

Once the webhook has been set up, Authorea pushes what it has to the GitHub repo and clobbers your stuff via a merge. You should go back to the local directory on your machine and pull in the changes.

![GitHub after merge]()
![GitHub network graph after merge]()

At this point, the connection between Authorea and GitHub has been made. I'm pretty sure that every time a push is made to `master` on the GitHub repo, Authorea will pull in the changes. Likewise, any time edits are saved on Authorea, Authorea will push the changes to GitHub. I think that if a merge conflict arises, Authorea will push the changes to the branch `authorea` and require a manual merge.

For what its worth, [tag `2.0`](https://github.com/jrsmith3/authorea_test/releases/tag/2.0) points to the commit where Authorea initially merged.

Step 3: Reorganizing what Authorea has done
===========================================
Things aren't terrible since all of the original LaTeX in the repo was on its own branch and since `master` was only a single commit containing `.gitignore`. There's some cruft in `master`. Here's a list of the files and how they should be fixed.

- **`untitled.tex`** is superfluous and can be deleted. 
- **`pdftitleTitle_pdfaut.tex`** contains the mangled `hyperref` directive -- `pdftitleTitle_pdfaut.tex` can be deleted because...
- **`header.tex`** should be modified to include the non-mangled `hyperref` directive.
- **`sectionIntroduction_.tex`** should be renamed. I prefer simply `introduction.tex`.
- **`preamble.tex`** should be rewritten with the original preamble.
- **`layout.md`** should be edited to remove the line calling for `pdftitleTitle_pdfaut.tex`. The line referring to the old `sectionIntroduction_.tex` should be updated.

Release [3.0](https://github.com/jrsmith3/authorea_test/releases/tag/3.0) is the state of the repo after the modifications.

Step 4: Merging `latex` back into `main`
========================================
I still want to be able to clone the repo and build the LaTeX document using the makefile. In order to accomplish this goal, I merged the `latex` branch back into `master`, and then modified `main.tex` to essentially the file at the top of this post. Note that I manually `\input{}` the sections in `main.tex`; there's probably a way to parse `layout.md` but its late and I am tired.

The result of the merge and subsequent `make` can be found in release [4.0](https://github.com/jrsmith3/authorea_test/releases/tag/4.0).

Conclusion
==========
The process of setting up Authorea as a frontend for a LaTeX document in an existing GitHub is a little wonky. Hopefully the extreme detail of this post helped.
