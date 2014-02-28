Title: Sample logs
Date: 2014-02-28 00:07
Category: Blog
Author: Joshua Ryan Smith

Summary
=======
* A sample log is a record of all the events a sample experiences.
* Sample logs are necessary, especially if sample processing/analysis steps are preformed by different people.
* The fundamental unit of a sample log is an entry.
* Entries should include references more than details.
* Each entry should record a date/time stamp, event name, brief description, and cross-references -- particularly lab notebooks and computer data files.
* Entries should be recorded at the time of event, not before or after.
* Sample logs are a record, not a prescription.

Introduction
============
A sample log is a record of all the events that a sample experiences. Sample logs are just as necessary as lab notebooks, particularly if processing or analysis steps are performed on a sample by more than one person. Samples tend to move from place to place and from person to person having complex and eventful lifecycles. The sample log is not a prescription; it doesn't tell where the sample is going, only where the sample has been. The sample log is also not a lab notebook; we forego the full details of the history of the sample in order to get a digestable sense of the sample's history.

Sample logs have two components: metadata and the list of entries.

Metadata
========
Obviously, the sample log should include the sample's name. The sample's name should be unique. Beyond that, require metadata probably depends on teh needs of individual labs. I've found it useful to record the size of the sample, the substrate, the manufacturer/supplier, and the manufacturer's metadata (lot number, etc.). Doping, structure, etc. is appropriate to record here.

Entries
=======
Entries are the fundamental unit of the sample log. Every time the sample experiences an event, an entry should be recorded. Each entry should have four components: date/time stamp, event name, brief description, and cross-reference.

Each entry should have a date and time in [YYYY-MM-DD format](http://en.wikipedia.org/wiki/ISO_8601). The entry should name the event. Obviously, events that change the nature of the sample should be recorded: material deposition, material removal, annealing, and things of that sort. Additionally, experiments that characterize the sample should also be recorded: XPS, AFM, XRD, electronic characterization of devices, etc. Third, it is generally useful to record the location of the sample: mounting to a sample holder, loading and unloading into an instrument, etc.

Every entry should also include any necessary, but brief, description. The description should not go into great detail about the experiment (detail should be recorded in the lab notebook), but it is sometimes useful to annotate the difference between events. For example, if the event is that the sample was loaded, the description would be the instrument. If the event is AFM is performed on the sample, the description might be the scan size or the relevant parameter like the RMS roughness. There isn't a hard and fast rule for what info should be recorded as the description, but its more based on the experience of the person writing things down.

Finally, the records should have a cross-reference, if applicable. Since the sample log should not be used as a lab notebook, the reference field should include a full cross-reference to a lab notebook and page number, e.g. `JRS Lab Notebook 2014-02-27`. Additionally, since most instruments record data in a computer file, the reference field should include a cross-reference to the computer file(s). For example, `20140217-2355_stm_0001_jrs.dat`. These files should be given unique names as discussed in a later section. There are some events that probably don't require a reference. For example, loading a sample into an instrument won't likely have a reference unless there was some unusual circumstance associated with it, in which case the details should be recorded in a lab notebook and the reference noted on the sample log.

Exmample
========
A sample log should be started every time a sample is created. You may wonder, what is a sample? In the type of science I do, a sample is an individual piece of material. Therefore, if a film is grown on a 2" sapphire wafer, that whole 2" wafer is a sample. I would suggest thinking of the virgin wafer as received from teh manufacterer as a sample, any film deposited on it would generate records in its sample log, as well as the grower's lab notebook, and likely computer files. Lets say that example 2" sapphire wafer is named "0001". When sample 0001 is removed from the supplier's package, it's sample log is created and the metadata recorded. If sample 0001 is diced into four separate pieces, sample 0001 ceases to exist and four new samples have been created, perhaps named 0001-1, 0001-2, 0001-3, and 0001-4. 

I have a [repository](https://github.com/jrsmith3/sample_log) on github which contains a sample log that can be printed.
