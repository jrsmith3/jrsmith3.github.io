Title: Naming data files revisited
Date: 2015-03-22
Category: Blog
Author: Joshua Ryan Smith
Summary: In which I suggest files should be named using the old NextSTEP dictionary plist format

I wrote a [post about naming data files](http://jrsmith3.github.io/naming-data-files.html) awhile back. Also, yesterday I found that [conda canonical filenames](http://conda.pydata.org/docs/intro.html#package-naming-conventions) [cannot support semver version strings](https://github.com/conda/conda/issues/974#issuecomment-61172554) because the dash ("-") character is disallowed in the string.

This issue is perennial: everybody reinvents the wheel of coming up with a file naming scheme for their system. Look, the goal of a file naming convention is to balance a few competing requirements:

* Human legibility. People should have a fighting chance of understanding the file contents by reading the filename.
* Uniqueness. The convention should mitigate name collisions.
* Orthogonality and precision. Components of the naming convention should be conceptually disjoint.
* Consistency.


Suggestion: data serialization format as filename
=================================================
I suggest that a general solution to the problem of coming up with file naming schemes is to employ some kind of human-readable data serialization standard like JSON, YAML, etc. for filenames. The idea is to use a string encoding a dictionary and populate the dictionary with metadata about the file. The only constraint on the type of data serilization format is constraints on filenames imposed by the filesystem.

JSON would be the obvious choice here, but unfortunately defining a dict requires a colon character (":") between the key and value. This character is illegal in filenames on Apple and MS Windows systems. YAML is out for the same reason plus the use of whitespace and newlines.

Thus, I propose using the old [NextSTEP plist format](http://www.gnustep.org/resources/documentation/Developer/Base/Reference/NSPropertyList.html). The fields and values should be of type string. In order to avoid invalid filesystem characters, the strings should be converted to [percent encoding](https://en.wikipedia.org/wiki/Percent-encoding) before writing the filename.

Using this plist dictionary as a filename, one could imagine someone writing a plugin for `grep` that parses the filename and searches the dict.

Unforunately, the NextSTEP plist format is pretty old and in five minutes of searching Google, I wasn't able to find many packages to parse it. On the other hand, the NextSTEP plist format for dicts isn't terribly different from the JSON representation; replacing the "=" with ":" in the filename string should suffice to make the string parseable JSON.


Examples
========
For files containing experimental data, the filename could include the following information:

* date and time stamp
* sample
* researcher's name
* technique

Additionally, the filename could include a checksum (e.g. SHA256) to verify the integrity of the data.

For example, the filename `20110701-1453_stm_jrs0069_jrs.sm4` could become:

```
{"d"="20110701T1453","e"="stm","s"="jrs0069","p"="Joshua Ryan Smith","sha256sum"="140acb7ef861ba01d74f3ee10a0a28eb80a6212157cc6ee1755bf4dfb772c6b7"}.sm4
```

where "d" stands for "date," "e" stands for "experiment type," "s" stands for "sample," "p" stands for "person," and "sha256sum" is self-explanatory.

The plist style filename contains more characters, but retains many of the advantages of my [old suggestion](http://jrsmith3.github.io/naming-data-files.html).
