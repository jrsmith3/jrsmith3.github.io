Title: Sample logs
Date: 2014-03-05 10:09
Category: Blog
Author: Joshua Ryan Smith

Summary
=======
* A sample log is a record of all the events a sample experiences.
* Sample logs are necessary, especially if sample processing/analysis steps are preformed by different people.
* The fundamental unit of a sample log is an entry.
* Entries should be recorded at the time of event, not before or after.
* Each entry should record a date/time stamp in [YYYY-MM-DD HH:MM format](https://en.wikipedia.org/wiki/ISO_8601), the event name, a brief description, and cross-references -- particularly lab notebooks and computer data files.
* Here's a [PDF sample log](https://github.com/jrsmith3/sample_log) you can print and use.

Introduction
============
A sample log is a record of all the events that a sample experiences. Sample logs are just as necessary as lab notebooks because they capture a set of information that simply isn't captured in a lab notebook. The necessity of such a record is particularly clear in projects where samples undergo process and analysis steps by many different people.

You may think, "What purpose does a sample log serve? Isn't this information contained in lab notebooks?" A sample log provides a concentrated and comprehensive record of all the events a sample experiences, foregoing detailed description for cross-referencing. A glance at a sample log provides the reader the lifecycle of the sample with cross-references the reader can follow to understand more detail. There is not another source of information that provides these features. 

In contrast to a lab notbook, a sample log won't contain a long narrative description of an experiment; it only contains a list of brief records of every event the sample experiences. Each entry contains the bare minimum amount of information with generous cross-referencing to the relevant lab notebooks and computer data files. 

Sample logs and lab notebooks are good compliments to one another since they contain different types of information and serve different purposes -- the information in one is enhanced by the information in the other. In principle, a sample log could be constructed from the entries in a lab notebook, but in practice this task would be very time consuming -- especially since the records are likely split over multiple lab notebooks belonging to multiple people.

Samples tend to move from place to place and from person to person; they have complex and eventful lifecycles. The sample log is not a prescription; it doesn't tell where the sample is going, only where the sample has been. 

Sample logs have two components: metadata and the list of entries.

Metadata
========
The metadta section contains information about the sample. Obviously, the sample log should include the sample's name. The sample's name should be unique. I'll give techniques for choosing unique sample names in a later post. Beyond that, required metadata depends on the needs of individual labs. I've found it useful to record the size of the sample, the substrate, the manufacturer/supplier, and the manufacturer's metadata (lot number, etc.). Doping, structure, etc. is appropriate to record here.

(Picture of metadata fields of a sample log)

Entries
=======
Entries are the fundamental unit of the sample log. Every time the sample experiences an event, an entry should be recorded. Each entry should have four components: date/time stamp, event name, brief description, and cross-reference.

Each entry should have a date and time in [YYYY-MM-DD format](http://en.wikipedia.org/wiki/ISO_8601). The entry should name the event. Obviously, events that change the nature of the sample should be recorded: material deposition, material removal, annealing, and things of that sort. Additionally, experiments that characterize the sample should also be recorded: XPS, AFM, XRD, electronic characterization of devices, etc. Third, it is generally useful to record the location of the sample: mounting to a sample holder, loading and unloading into an instrument, etc.

Every entry should also include any necessary, but brief, description. The description should not go into great detail about the experiment (detail should be recorded in the lab notebook), but it is sometimes useful to annotate the difference between events. For example, if the event is that the sample was loaded, the description would be the instrument. If the event is AFM is performed on the sample, the description might be the scan size (e.g. 1μm × 1μm). There isn't a hard and fast rule for what info should be recorded as the description, but its more based on the experience of the person writing things down.

Finally, the records should have a cross-reference, if applicable. Since the sample log should not be used as a lab notebook, the reference field should include a full cross-reference to a lab notebook and page number, e.g. `JRS Lab Notebook 2014-02-27`. Additionally, since most instruments record data in a computer file, the reference field should include a cross-reference to the computer file(s). For example, `20140217-2355_stm_0001_jrs.dat`. These files should be given unique names as discussed in a later section. There are some events that probably don't require a reference. For example, loading a sample into an instrument won't likely have a reference unless there was some unusual circumstance associated with it, in which case the details should be recorded in a lab notebook and the reference noted on the sample log. I've written some tips for naming and maintaining a [lab notebook](http://jrsmith3.github.io/effective-lab-notebooks.html) and [computer data files](http://jrsmith3.github.io/naming-files-uniquely-to-reduce-confusion.html). These suggestions ensure unique names for both.

Exmample
========
A sample log should be started every time a sample is created. You may wonder, what is a sample? In the type of science I do, a sample is an individual piece of material. Therefore, if a film is grown on a 2" sapphire wafer, that whole 2" wafer is a sample. I would suggest thinking of the virgin wafer as received from teh manufacterer as a sample, any film deposited on it would generate records in its sample log, as well as the grower's lab notebook, and likely computer files. Lets say that example 2" sapphire wafer is named "0001". When sample 0001 is removed from the supplier's package, it's sample log is created and the metadata recorded. If sample 0001 is diced into four separate pieces, sample 0001 ceases to exist and four new samples have been created, perhaps named 0001-1, 0001-2, 0001-3, and 0001-4. 

I have a [repository](https://github.com/jrsmith3/sample_log) on github which contains a sample log that can be printed.

Tips
====
What follows is a list of tips based on my experience working on projects at the intersection of device physics, surface science, materials science, and electrical engineering. I ussually work with the typical materials science samples: mainly semiconductor wafers (Si, SiC, GaN) or insulating wafers (sapphire). I usually deal with some surface science analysis like XPS, AES, LEED, STM, or AFM. I also deal with processing steps like photolithography and metallization and analysis of fabricated devices like IV and CV measurements or microanalysis via SEM or EBIC. Samples usually start as 2" or 4" wafers and then are diced into smaller pieces during processing. If your work isn't based on wafers like mine, this approach may not apply. Also, my experience is in research science and not industrial process engineering. I know the advice I give in this post applies to operations that deal with tens to a few hundred samples per year with a group encompassing four to around twenty people.

Recommendations
===============
Since creating and maintaining sample logs is a continual occurance, I recommend using a pre-formed worksheet. I have created such a worksheet using [scribus](http://scribus.net) for this purpose and put the source and PDF on [github](https://github.com/jrsmith3/sample_log). I've licensed the worksheet with a Creative Commons Attribution 4.0 International license, so feel free to use and modify it.

I recommend printing the worksheets out on paper instead of using an electronic sample log. Electronic sample logs need to be constantly updated and maintained and in the end, this cost means people don't use them consistantly. Additionally use end up running into synchronization issues. With a paper system, you can put a samples's log together with the sample itself using a [page protector](http://www.amazon.com/exec/obidos/ASIN/B00006IC89) and pas around the entire packet to the next person whos doing processing or analysis experiments.

I recommend printing the sample logs on cleanroom paper (if your experiments are in the cleanroom), and applying a three-hole punch. I also recommend getting a three-ring binder (I find [1-inch](http://www.amazon.com/exec/obidos/ASIN/B0001J3R3C) works best) to store sample log sheets.
