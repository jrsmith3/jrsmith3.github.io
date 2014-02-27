Title: Sample names
Date: 2014-02-21 17:17
Category: Blog
Author: Joshua Ryan Smith
status: draft

How should samples be named? The best answer is the simplest: with an integer. Generally speaking, so long as all samples have a unique name, the actual name is arbitrary. The real problem occurs when people try to name samples by using a contextual naming scheme, this approach always ends in ambiguity and enourages bias by focusing on the wrong thing. For example, someone may be inclined to try to include the name of hte material in the sample name, say the sample is gallium nitride on sapphire and the name is `GN0001`. This name distractingly elevates the gallium nitride part of the sample while ignoring the fac tthat the substrate is on sapphire. What happens if the substrate is SiC? What about HVPE GaN? What happens if metal is deposited on the sample or if the sample has AlGaN regrown on it? An argument could be made for all the following names for the same HEMT sample.

<picture>

    * 0001
    * GN0001
    * AlGaN-GaN-500torrGaN-100torrGaN-Nuc-Sap0001

still another problem occurs: what if a sample is grown with a specific sequence of experiments in mind, but is put on a shelf for a few months. Later, its decided that the sample should be used for a different set of experiments, but the name has already been decided.

The problem with every other naming scheme other than simple incremental is twofold: first, even under the best of circumstances, its very difficult to predict the trajectory of any particular sample. Second, naming schemes including anything other than an incremental interger explicity include various metadata that isn't necessariliy easily categorizable, finite, or orthogonal.

Consider the GN naming scheme; there are actually two fields of information:

    GN0001

Material/increment

but is GN descriptive enough? Based on the above discussion, it isn't.

The other inclination of people is to include the substrate in the sample name. For example:

    * sapphire0001
    * SiC0001
    * GaN0001

This approach introduces a host of new problems: is there a finite and unambiguous set of names for substrates? If not, you're hosed. What if 98% of all your samples are sapphire? Then your naming scheme includes a lot of superflous information. Finally, and worst of all, how do you deal with incrementing the samples? Does every substrate get its own set of increments?

    * sapphire0001
    * sapphire0002
    * sapphire0003
    * sapphire0004
    * sapphire0005
    * SiC0001
    * SiC0002
    * GaN0001
    * GaN0002
    * GaN0003

If so, it will be a nightmare trying to keep track of all these samples. If not, you are left with:

sapphire0001    0001
sapphire0002    0002
sapphire0003    0003
    SiC0004     0004
        GaN0005 0005
        GaN0006 0006
sapphire0007    0007
sapphire0008    0008
        GaN0009 0009

in which case the prefix of the substrate name is superfluous and we could achieve the same thing with a simple incremental integer as the sample name.

The point is that the sample log necessarily records all of the infomation that would make a sample unique: the substrate, the recipe to deposit a film, etch steps, everything. There is a contextual fallacy of sample naming that is an almost invisible human weakness. A simple incremental integer naming scheme combined with a detailed sample log fully mitigates this weakness.
