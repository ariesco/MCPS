MCPS: Model checking parameterized by the semantics in Maude
============================================================

MCPS is a tool for performing model checking on programs whose semantics has been
specified in Maude. Although Maude can perform this kind of model checking, the
obtained counterexample is difficult to follow because it refers to Maude semantics
and not to the programming language semantics previously specified. MCPS solves this
problem by performing a meta-level analysis that allows us to


Currently, MCPS supports shared-memory and message-passing
semantics.

Getting the tool
----------------

The code of 'MCPS' is contained in a GIT repository on GitHub, whose URL is
https://github.com/ariesco/MCPS. To get a copy of the repository you only
have to write in your Linux/MacOS console:

    $ git clone https://github.com/ariesco/MCPS

This will create a MCPS folder containing the source code of the tool as well as
several examples.

Using the tool
--------------

MCPS is started by loading the *mcps.maude* file into the Maude system as (assuming
we are located in the MCPS directory):

    $ maude mcps.maude

It starts an input/output loop where commands must be introduced by enclosing them in
parentheses (as MCPS extends Full Maude). If the Maude modules containing the semantics
and the formulas for model checking are specified in Full Maude, the loop must be
started again as:

    Maude> select MCPS-INIT .
    Maude> loop init-mcps .
    Maude> (select MOD-NAME .)

where *MOD-NAME* is the name of the module where model checking will take place.
In both shared-memory and message-passing style we need to define the sort in charge
of processes. The command **unit**

    Maude> (unit SORT id N.)

### Model checking programs following a shared-memory approach