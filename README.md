MCPS: Model checking parameterized by the semantics in Maude
============================================================

MCPS is a tool for performing model checking on programs whose semantics has been
specified in Maude. Although Maude can perform this kind of model checking, the
obtained counterexample is difficult to follow because it refers to Maude semantics
and not to the programming language semantics previously specified. MCPS solves this
problem by performing a meta-level analysis that allows us to infer those rules in
charge of the main events in the counterexample. In addition to focusing on the main
events, MCPS returns a JSON-like trace that can be easily analyzed later.
Currently, MCPS supports *shared-memory* and *message-passing* semantics.

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

where **MOD-NAME** is the name of the module where model checking will take place.
In both shared-memory and message-passing style we need to define the sort in charge
of processes. The command **unit** is used as follows:

    Maude> (unit SORT .)

If the unit has an argument that is used as identifier (which is usual in general for
message passing semantics, since **send** commands require an identifier) we can use
the following command to introduce it:

    Maude> (unit SORT id N .)

### Shared-memory semantics

This kind of model checking requires the user to introduce the sort(s) for
specifying the memory as:

    Maude> (memory sorts SORT_1 ... SORT_n .)

Once this information has been introduced we use the following command for model
checking:

    Maude> (shared memory analysis modelCheck(INIT, FORMULA) .)

where **INIT** is the initial state where model checking takes place and **FORMULA**
is an LTL formula we want to verify. The result is a list of JSON-like elements
where, for each step, we display:
* The process being executed (field **unit**) before the rule is applied.
If the process has an identifier will be displayed in the **id** field.

* The whole system (field **system**) before the rewrite rule is applied.

* The state of the memory. Since the memory will be modified by the application of the rule,
we present the states *before* applying the rule (field **memory-before**) and
*after* applying it (field **memory-after**). In this way the user can inspect the
effects of the rule. Note that this field is a list, since in general different types of
memory can be used.

* Since we can work at the metalevel, we decided to display the value of all atomic formulas
before and after applying the rule, so the user can understand the values taken by the LTL
formula (field **props**). For each atomic proposition in the formula we display its name,
arguments, and how its value changed when the current rule is applied. Hence, steps in this
case have the form:


```
{ id =  ...,
  unit = ...,
  system = ...,
  memory-before = ...,
  memory-after  = ...,
  props  = [{name_1 = ...,
             args_1 = [...],
             prop_1 = ... -> ...},
            ...,
            {name_n = ...,
             args_n = [...],
             prop_n = ... -> ...}]
}
```

### Message-passing semantics

When model checking programs following message-passing semantics, the user must
introduce the operators for creating and consuming messages as follows:

    Maude> (msg creation OP_1 ... OP_n .)
    Maude> (msg consumption OP_1 ... OP_n .)

We can also set the kind of trace we want to obtain. MCPS allows users to select
a summary of the computation with the **process** mode (which is selected by default)
and a trace-like approach with the **trace** mode.  Modes are switched with the commands:

    Maude> (set mode process .)
    Maude> (set mode trace .)

The **process** mode displays:
* Its identifier (**id** field).

* Its final value (**value** field). Note that in some cases this value will not be a normal
form, since some functions (e.g. servers) might be non-terminating.

*
The list of messages it has sent (**sent** field).

* The list of messages it has consumed (**consumed** field).

Hence, the result of this analysis has the form:

```
{processes =[
  {   id_1 = ...,
      value_1 = ...,
      sent_1 = [...],
      consumed_1 = [...]},
  ...,
  {   id_n = ...,
      value_n = ...,
      sent_n = [...],
      consumed_n = [...]}
```

On the other hand, the **trace** mode displays:
* The identifier of the process that performed the action (**id** field).

* The action that took place (**action** field), which can be either
**msg-consumed**, **msg-sent**, and **prop-changed**, which stand for messages consumed,
messages sent, and truth value of atomic propositions changed, respectively.

* The messages involved in the action (**messages** field). This field is omitted
when the action is not referred to message creation or consumption.

* The state of all processes before and after applying the rule, so changes
can be easily analyzed (**processes-before** and **processes-after** fields,
respectively).

* How the properties changed with the rewrite rule (**props** field), which are
displayed as explained above for shared memory.

Hence, one step of this analysis has the form:

```
{id = ...,
 action = ...,
 messages = [...],
 processes-before = [...],
 processes-after = [...],
 props  = [{name_1 = ...,
            args_1 = [...],
            prop_1 = ... -> ...},
           ...,
           {name_n = ...,
            args_n = [...],
            prop_n = ... -> ...}]
}
```

Once this information has been introduced we use the following command for model
checking:

    Maude> (msg passing analysis modelCheck(INIT, FORMULA) .)
























