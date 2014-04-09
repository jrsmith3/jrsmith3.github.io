Title: Pre-brief: Collaborative proposal prep via GitHub with non-developer professors
Date: 2014-04-09 12:47
Category: Blog
Author: Joshua Ryan Smith
Summary: Initial thoughts on using GitHub to collaborate on a proposal with newbies.

I am just starting writing a proposal with a few professors, all of whom are over 40, use Windows and MS Word, and none of whom have any software development experience. To make things more interesting, the team is scattered across the US. My goal is to manage the proposal writing using GitHub, and ultimately use GitHub to collaborate on the papers, software, and other products coming out of this work.

This post is a pre-brief of the process; I want to record what I've done to introduce my collaborators to a git-based workflow via GitHub, and some thoughts and concerns before sending out the email asking them to set up a GitHub account.

I intend to post updates during the proposal writing process, and I will post a debrief once the proposal has been submitted. In these posts, I will cover what worked and what didn't work.

Motivation
==========
My experience is that the default method of collaboration is completely ad-hoc and consists of emailing various versions of documents. There typically is not a central location, such as a fileserver, on which documents and data are stored.

I've seen GitHub used [successfully](https://github.com/swcarpentry/) to coordinate the [Software-Carpentry](http://software-carpentry.org) project. I believe that several contributors to SWC use a git or GitHub based workflow to coordinate the work in their labs.

I believe the git workflow can be applied to most parts of the proposed project and the goal is to impose this workflow in at the very beginning of the project.


Preparation
===========
I've had several phone and email conversations with my collaborators, to sketch out the scope of the proposal and to get their feedback and buy-in. I have sent them drafts of a document, in PDF, that will ultimately become the proposal. This proto-proposal was prepared using LaTeX, but the whitepaper and proposal itself will be prepared using markdown.

I am currently at the point where I need to invite my collaborators to edit the proposal. I have set everything up to (hopefully) reduce friction as much as possible; to this end I have prepared a few items:

* An opening email.
* A private GitHub repository containing the files for the proposal.
* A README in the root directory of the repo.
* Additional instructions on git, GitHub, and markdown.

Email
-----
This invitational email is about 500 words long and includes several components: I mention the scope of the project, I mention the fact that the first step in the process is to submit a whitepaper, and the date I hope to submit the whitepaper. I also give some simple instructions for how to create an account on github in order to access the repository. Finally, I briefly sketch out how the markdown/GitHub workflow is different from the MS Word/email workflow.

Repo
----
Before sending out the invitation email, I created an organization for this project, and set up a private repository on GitHub.

README
------
In the root of the GitHub repo, I created a 200 word `README.md` that will be displayed when people visit the repo page. This file has a section of links to the relevant documents from the funding agency. The second section reiterates the scope of the proposal. Finally, there is a section of simple instructions for how to edit the whitepaper via GitHub's web interface with a mention that files in the repository can be edited locally on their machine.

Additional instructions
-----------------------
I've added a file of additional instructions regarding git, GitHub, markdown, and a few other odds-and-ends. I go into more detail about how to clone repos locally. This file is 633 words long. I'm not explicitly linking to it from the `README.md`, but perhaps I will if there are too many questions.


Concerns
========
One of my main concerns is that the jump from editing a document locally on their computer in MS Word to editing a document on GitHub in markdown will be too large. Another concern is that I am not starting with a comprehensive tutorial of what git is and how the git workflow of forking, pull requests, branching, and merging works -- I'm simply showing them GitHub's web interface of a git repo and asking them to edit a document via that interface. Finally, I'm concerned that my collaborators will be turned off by the fact that markdown doesn't automatically do pagination like a word processor (the funding agency has a strict page limit on the whitepaper).

Another big concern I have is how to transition my collaborators from the idea that they are editing a document on the web to the idea that they can edit documents locally on their machine, commit changes to the local repo, then push those changes to the remote.
