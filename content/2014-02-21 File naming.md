Title: File naming
Date: 2014-02-21 17:47
Category: Blog
Author: Joshua Ryan Smith
status: draft

Dates should be written in the YYYYMMDD format. This practice organizes the groups of numbers from largest increment of time to smallest. Its better than MM DD YY because that form can be ambiguous. Times should be written as HHMMSS with the seconds being optional for the most part. 24-hour times should be used because it is precise where as 12h, am/pm can lead to ambiguity.

People tend to have a difficult tie with coming up with filenames and putting files into a filesystem. The main issue is that people do't have a coherent, unambiguous, trusted system. A trick people use to "organize" things is context: "I know that I need to remember this, so I put it on the left hand side of my desktop." People do the same thing in physical space: they will put things in drawers, on countertops, put post-its on computer monitors all to imbue these artifacts with context so that they will remember teh context of the item at the right time.

This approach is fraught.

For one, humans are bad at remembering things, bad at predicting things, and context isn't a universal thing for everyone. People tend to forget where they put things, or forget what a particular thing existed in the first place. Out of sight, out of mind. Another pattern is that people will put a thing in a location in order to have it handy when it comes time to use it. However, it is nearly impossible to predict the future, and therefore these efforts rearely achieve their goals. Third, just because the location of something makes sense to one person, doesn't mean the organization makes snese to anyone else. In fact, people have all kinds of cognitive bias and blind spots that make ti difficult to create ad-hoc logically consistant organizational structures. Sometimes people make self-contradicting organizational structures without knowing it.

The two keys to an organizational system are unique naming of things and locatability. A third useful component is metadata. I'll take all of this stuff in turn.

Lets say we have the simplest system: a collection of things. Not all collections of things are the same. If we had a collection of letters (A, B, C, E, D), then we could easily impose an order on these items: A, B, C, D, E. A collection of sports equipment, on the other hand, doesn't offer such natural ordering: tennis ball, cricket bat, golf club, swim trunks, cleats, football helmet. Most collections of things that we found in labs can't easily be ordered like the letters of the alphabet, so any attempt to do so will yield much weeping and gnashing of teeth. On the other hand, it is usually quite useful to be able to preciesely and unambiguously refer to things. In other words, organization is better if items in systems have a unique name. From here on out, I'll refer to the unique name of something as its uniform resource name or URN. Sometimes people call this a unique identifier or UID. If something has a URN, you can talk about that thing without ambiguity. URNs are very important when it comes to information. If you have 10 unique files, all called "new_data.dat" scattered throughout your filesystem, your life is terrible.

People tacitly deal with this problem by locating these fiels in different parts of the filesystem. The problem gets compounded when copies of these files are made, or if the file gets changed over time, or both. A more descriptive filename doesn't help because people are bad at predicting the future and because filenames have a length restriction.

What you want is a unique filename and sufficient metadata to contextualize the meaning of the fie. By using a unique filename, all of your data can be stored in a single directory, making locatability trivial. If the file is in the `data` folder, it exists. If not, it doesn't. Furthermore, a flat filesystem structure is better. Data that comes directly off an instrument i spristine. Data maniupulation can be performed on the pristine copy, but the pristine copy hsouldn never be altered. Altering this data is at best sloppy, and at worst unethical. Having a single folder for data makes it easy to have a pristine copy: if you need to do data analysis or manipulation, just copy the file out of teh data directory and hack away. In this way multiple and conflicting copies of the data can be kept, but everybody knows thtat the "official" pristine data is in the `data` directory. Therefore, if somebody gets a result, everyone else can check the result by making their own copy of the relevant data from the pristine data directory.

So, how does one make unique filenames without resorting to comments in the filename? Here's a way that works well:

    YYYYMMDD-HHMM_experiment_sample_experimenter

This construction has the right amount of metadata. There are no special notes that aren't universal. The metadata that's included gives enough info to track down everything you'd need to know about the file. The datestirng serves two purposes: it organizes all data chronologically in time and it ensures that all filenames will be unique. Its unlikely that you can take data at a rate fast enough that seconds are necessary. I usually name the file the time that the data is saved. The precise time isn't terribly important because all of that information should be recorded in a lab notebook. The experiment field tells the reader what kind of data this is and liekly what instrument it is. Simplicity here is better than specificity. The sample name allows the reader of the file to know where to find more information: all he or she has to do is look at the appropriate sample log. Finally, the filename attributes the researcher who took the data. In this way it is easy to figure out who gets to be co-author or who you can talk to if you need more detail about an experiment.

This naming scheme is not a URN. I think you'd need some prefix like lab name, etc. However, it should work out for a small group of < 50 people. Your CMS can deal with the actual URN creation.

Things I wrote for Randy
========================
Data file naming convention and location
========================================
This document describes the workflow for data collection and storage in our group. It also outlines the convention we will use for data files generated in the course of research.

Executive summary
-----------------
Data will be named with a long filename adhering to the rubric:
  
  YYYYMMDD-HHMM_technique_sample-number_scientist-initials
  
All data files will be stored in a directory named `data` with a flat directory structure, i.e. no subdirectories. The data will be mirrored across several locations, but will typically come off an instrument via floppy, to an external hard drive, then to the local filesystem of peoples' workstations and finally onto the shared l: drive.


Data workflow and location
--------------------------
Data will be stored in three locations, in a directory named `data`:
  
  * Randy Tompkins's external drive in directory `data`
  * Randy Tompkins's computer on `C:\Users\randy.tompkins\Documents\Documents\ARL\data`
  * The server on `L:\WBG Team\data`
  
The `data` directory will have a flat structure: there will be no subdirectories because the filenames will be sufficiently descriptive to determine the contents of the file. Additionally, records will be kept using sample logs and lab notebooks giving sufficient context to understand what is contained in the file.

The workflow of the data through these various locations will be as follows. First, data will be acquired using an instrument. The filename for this data will be specified according to the rubric below. If the instrument has a filename character limit preventing the use of the file naming rubric, a suitable temporary filename will be used until the file is on a computer that supports long filenames. For example, the Agilent 4155C likely only supports filenames 8 characters in length. Data would be saved as `a.dat`, `b.dat`, etc. onto the floppy inserted in the 4155C. When the files are copied off of that floppy, their names will be changed and the newly named files will be added to the `data` directory. The temporary filename as well as the filename according to the rubric may be recorded on the sample log to avoid confusion.

Once the data has been named appropriately, it will first be copied into the `data` directory on RT's external drive. From there it will be mirrored to RT's c: drive and the l: drive when convenient. Eventually the data will be kept under version control using git. At that point, the copy operations will occur as pulls from the 'official' repo defined by RT's external drive.

Data taken before 2014-02-21
----------------------------
Data acquired before 2014-02-21 will be stored in a directory named data-pre_2014-02-21.

Data file naming convention
---------------------------
Data files will be named by combining specific metadata fields with underscores according to the following rubric:

  YYYYMMDD-HHMM_technique_sample-number_scientist-initials

Where:

  * YYYY: four-digit year (e.g. 2014, not 14)
  * MM: two-digit month (e.g. 02, not 2)
  * DD: two-digit day (e.g. 05, not 5)
  * HH: two-digit hour in 24 hour mode (e.g. 14, not 2pm. 09, not 9.)
  * MM: two-digit minute
  * technique: name of technique (e.g. XPS, AFM, IV, etc.)
  * sample-number: unique number identifying the sample
  * scientist-initials: initials of the person who took the data. Important for attribution when writing papers. (e.g. JRS).
  
This rubric is based on the idea of a sufficient level of metadata detail and sorting of metadata fields from big-to-small or general-to-specific. It has the added advantage of providing ascii-betical sorting by default because of the timestamp at the beginning.

Generally speaking, the metadata fields should be sorted from big-to-small or general-to-specific. For example, the date/time metadata field goe from year to month, day, hour, and minute; smaller and smaller denominations of time. The "technique" field could be split by a dash (-) for example, to indicate technique (iv) and instrument. An example would be "iv-4155c" where "iv" stands for "current-voltage" and "4155c" indicates the Agilent semiconductor parameter analyzer. The sample number field could be split this way when addressing a specific device on a particular wafer. For example: "AG5416.2-12".

Please use common sense when naming files. Note the tradeoff between filename specificity and the cognitive load of including the maximum amount of metadata to a file. The longer the filename and more specific the metadata included, the more difficult and error prone keying filenames by hand becomes. Again, use good judgement and realize that perhaps a sane approach involves taking good notes in your lab notebook with the appropriate level of cross-referencing between lab notebook, sample logs, and filenames.

Example data file name
----------------------
An example of this nomenclature would be, if Randy (Preston) Tompkins made a CV measurement on sample AG5416.2 on December 7, 1991 at 1 pm the file would be named:

19911207-1300_CV_AG5416.2_RPT.dat