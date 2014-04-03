Title: Data storage and data file workflow
Date: 2014-04-02 23:05
Category: Blog
Author: Joshua Ryan Smith
Summary: Store data read-only using unique filenames in a single, flat directory.

Summary
=======
* Raw data files should be stored in a single directory named `data`.
* `data` should be centrally accessible.
* Files in `data` should have [unique names](http://jrsmith3.github.io/naming-data-files.html).
* `data` should have a flat structure (no subdirectories).
* Files in `data` should remain pristine as they came off the instrument and never be altered.
* Do not use `data` as a working directory. I.e. don't put code, manuscripts, etc. in `data`; use a project directory instead.

Introduction
============
Its 2014 and you are going to generate computer data files in the course of your research. These may be SEM images in TIFF format, files containing I-V data for your Schottky diodes in ASCII format, or AFM images in some proprietary binary format. how should you organize these files? How should you store them so others can get access when they need to (without bothering you)? How can you keep track of them and any changes during data analysis?

The solution: unique filenames, read-only files, and a single, flat `data` directory
====================================================================================
Most of your data management issues can be resolved by adopting a single, flat (no subdirectories), centrally accessable directory containing read-only copies of your group's data files which are uniquely named. The advantages of this approach far outweigh the disadvantages.

The workflow for this kind of system is straightforward: data is taken on an instrument and gets copied to `data`. If analysis is to be done on files in `data`, the person analyzing the data would first copy the files into a different project directory and then perform the analysis.

Having all data in a single, centrally accessible `data` directory has a number of advantages. First, this approach eliminates findability costs. People will never have to go around to different computers looking for the data they took. Second, this approach decreases transaction costs. If you need data that I took, simply find it in `data`. There's no need to ask me to email or otherwise send you the data.

This approach to data management is strongly coupled to choosing [unique, metadata-rich filenames](http://jrsmith3.github.io/naming-data-files.html). To recap, use 

    `YYYYMMDD-HHMM_experiment_sample_experimenter.extension`

on systems that support long filenames and the nested directory structure
    
    `experimenter/sample/experiment/YYYYMMDD/HHMM.extension`

on older systems that do not support long filenames.

Having a `data` directory filled with uniquely and descriptively named files is the next best thing to having a [URI](https://en.wikipedia.org/wiki/Uniform_resource_identifier) for each file and having the files in some kind of relational database. In addition to the advantage of findability I mentioned above, uniquely naming files in a single `data` directory yields addressability. Cross-referencing a file in a [lab notebook](http://jrsmith3.github.io/effective-lab-notebooks.html) or [sample log](http://jrsmith3.github.io/sample-logs-the-secret-to-managing-multi-person-projects.html) is as simple as writing the filename. To find the cross-referenced file, simply look for it in `data`. 

Using metadata-rich filenames gives you another advantage: it is easy to perform searches for specific files or sets of files using filename search functions in your file browser or shell. More advanced searches can be done via simple python scripts. For example, lets say I wanted to find all the XPS data files I've taken

```bash
gamma:data jrsmith3$ ls | grep -i jrs | grep -i xps
20100202-0844_xps_tfan25_jrs.dat
20100202-0850_xps_tfan25_jrs.dat
20100202-1008_xps_tfan25_jrs.dat
20100202-1019_xps_tfan25_jrs.dat
20100202-1127_xps_tfan25_jrs.dat
20100202-1133_xps_tfan25_jrs.dat
20100202-1223_xps_tfan25_jrs.dat
20100202-1227_xps_tfan25_jrs.dat
20100202-1324_xps_tfan25_jrs.dat
20100202-1330_xps_tfan25_jrs.dat
20100203-0823_xps_tfan24_jrs.dat
20100203-0829_xps_tfan24_jrs.dat
20100203-0932_xps_tfan24_jrs.dat
20100203-0937_xps_tfan24_jrs.dat
20100203-0956_xps_tfan24_jrs.dat
20100203-1033_xps_tfan24_jrs.dat
20100203-1039_xps_tfan24_jrs.dat
```

The same search can be performed from OSX Finder:

(picture of search as viewed in finder)

I don't use Windows, but I would be happy to post instructions on the same search from powershell or Windows file Explorer, [email](mailto:joshua.r.smith@gmail.com) me or pull request.

Using the file naming scheme I suggest above gives one final advantage. File browsers and the shell will automatically list files in chronological order. In the bash shell:

```bash
gamma:data jrsmith3$ ls
20110520-1543_stm_jrs0075_jrs.sm4
20110520-1614_stm_jrs0075_jrs.sm4
20110520-1622_oscilloscope_jrs0075_jrs.txt
20110520-1623_stm_jrs0075_jrs.sm4
20110520-1700_stm_jrs0075_jrs.sm4
20110520-1709_stm_jrs0075_jrs.sm4
20110520-1721_stm_jrs0075_jrs.sm4
20110520-1731_stm_jrs0075_jrs.sm4
20110520-1742_stm_jrs0075_jrs.sm4
20110520-1819_stm_jrs0076_jrs.sm4
20110520-1830_stm_jrs0076_jrs.sm4
20110520-1840_stm_jrs0076_jrs.sm4
20110520-1840_stm_jrs0076_jrs.txt
20110520-1916_stm_jrs0076_jrs.sm4
20110520-1925_stm_jrs0076_jrs.sm4
20110520-2002_stm_jrs0076_jrs.sm4
20110523-1052_xps_jrs0076_jrs.dat
20110523-1110_xps_jrs0076_jrs.dat
20110527-1614_TEM_JRST27_ATW.dm3
20110527-1615_TEM_JRST27_ATW.dm3
20110527-1617_TEM_JRST27_ATW.dm3
20110527-1619_TEM_JRST27_ATW.dm3
20110527-162412_TEM_JRST26_ATW.dm3
20110527-162430_TEM_JRST26_ATW.dm3
20110527-1627_TEM_JRST26_ATW.dm3
20110527-1628_TEM_JRST26_ATW.dm3
20110527-1635_TEM_JRST26_ATW.dm3
```

The same thing in the OS X finder: 

(picture of `data` as viewed in file browser)  




Old
===
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
