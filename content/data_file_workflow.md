Title: Data storage and data file workflow
Date: 2014-03-18 17:33
Category: Blog
Author: Joshua Ryan Smith
Summary: Store data in a single, flat directory.

Summary
=======
  * Raw data files should be stored in a single, flat directory (no subdirectories) named `data`.
  * Raw data files stored in the `data` directory should remain pristine and never be altered.
  * If a raw data file needs to be processed or altered for analysis, a copy should be made from the `data` directory and modified locally.

Introduction
============
I've written previously about how to choose [unique filenames for data]() produced by a computer. To recap, use 

    `YYYYMMDD-HHMM_experiment_sample_experimenter.extension`

on systems that support long filenames and the nested directory structure
    
    `experimenter/sample/experiment/YYYYMMDD/HHMM.extension`

on older systems that do not support long filenames.

In this post, I will describe a way to store data and a workflow for getting data from your instrument to a central repository.

The main takeaway is that your lab should keep all data in a central location. For example, there should be a directory on a computer or server named `data`. Your data files should be stored in this directory, regardless of type (images, I-V data, etc.). It is very important that this directory has a flat structure -- all data files go directly into the `data` directory. THERE SHOULD BE NO SUBDIRECTORIES in the `data` directory.

(Picture of finder looking at `data`)

At this point you may be thinking, "OMG! `data` will contain thousands of files!" So what? If you choose [unique, hierarchical, and descriptive filenames](), your file manager will display the data in chronological order. Think about why you might be having this reaction. Having all your group's data has many advantages white having data apread across several locations has no obvious advantages. Here are the advantages of putting all your group's data files in a single, flat, `data` directory using descriptive filenames. First, locateability. If the file is in `data`, it exists. If it isn't, it doesn't exist. This fact has more implications regarding cross-referencing. For example, writing the filename down is sufficient to reference the file. You can write the filename in your [lab notebook]() entry or the [sample log](), and a reader of your notebook can immediately find the file (hint: its in `data`). If you name files using the scheme I suggest, your file browser will display all your data in chronological order. Again, using my suggested naming scheme means you can do a simple search for the experiment type, sample name, or experimenter initials, and the search will yield exactly what you want.

This method is tightly integrated with my [data file naming scheme]().

Putting all data files in a single `data` directory also minimizes transaction costs. If I take a lot of data and I put it in `data`, you can analyze my data without having to ask me. Since my data files are named with my initials, it is clear that I deserve attribution in your paper.

Downsides of a single `data` directory: single point of failure. Therefore, backup this directory! (is there a way to use dropbox for this task and an online incremental backup service?)

Another downside: modification of files within `data`. This is a separate issue. Don't modify data in `data`. Make a copy and modify the copy. The data in `data` is pristine. It should contain only files that came directly from the instrument.

Workflow
========
Data should move along the following trajectory: Data files are created on the instrument and are named appropriately. Data then moves into the `data` directory either directly or via an intermediate medium. Directly means you mount the device containing `data`, then copy your files. Otherwise you might copy the data from the computer attached to the instrument to a portable external drive, then from the portable drive to `data`.

The rsync program is good for this sort of thing. Also putty (?) has a feature to sync one directory to another.

Version control might be useful for this application.

Short filenames
===============
According to my file naming rubric, short filenames are necessarily in a nested directory structure. Look, part of this scheme assumes `data` is only additive: things can be added; but once added, things don't change.

There's two possibilities with the short filenames: renaming/copying script. Copy into subdir, linking & renaming script.

I still need to talk more about the analysis of the files in `data`. Look, `data` is a repository, not a workshop. Data goes there to be stored, analysis of the data should occur in a separate location. Files that are not pristine copies of the data should not be in this directory.

So I should basically write a spec for the `data` directory.


Is this advice applicable to your lab?
======================================


Note
====
* Backups
* flat filesystem structure
* rsync
* git/version control
* pristine copies of data
* disadvantages of nested filesystem
