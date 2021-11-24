
# `twstools` – Command Line Tools Around Interactive Brokers TWS API

This is a simple set of command line tools around IB's TWS API with the purpose
of having a powerful scriptable toolbox to automate "jobs" like downloading
historical data, tracking account info or submitting orders etc.

The project homepage (bugtracker, latest git repo) is hosted on [GitHub](https://github.com/rudimeier/twstools).

Released source tarballs [download here](https://bitbucket.org/rudimeier/twstools/downloads).

Binary RPMs [for various Linux distros here](http://software.opensuse.org/download.html?project=home:rudi_m&package=twstools).

## Project's objective

The twstools wants to be small, simple, _stable_, easy to use and easy to
maintain.

A general use case covered by the tool set looks like this:

1. Create a job file (for example "get contract details for 10 lazy specified
   option chains").
2. Process the job file against TWS to get an output file containing the
   complete response without any loss of information.

3a. Parse the output file to get the information you are needing
    currently.
and/or
3b. Parse the output file to generate further jobs from it (for
    example generate a job file to get historical data for the received
    5000 option contracts and goto 2.).


The input/output file format is very simple xml which can be easily
generated/converted from and to csv using the tools itself or any other
tools. Per design the actual job processing tool is completely stupid -
just doing the given job but doing it very well.

BTW the used xml format is intended to be independent from the tools
itself to be used to carry all IB/TWS specific structs/messages for
saving without loss of information and easy conversion to other
existing TWS/API applications, see "Notes on the twsxml format"

## Installation

Building from source requires a C++ compiler and libxml2 which should be
available by your distribution.
Tested compilers are gcc/g++ and icc/icpc. Windows has been successfully tested
using cygwin.

To make life easier pkg-config should be installed.

Additionally we need to install `twsapi` from [here](https://github.com/rudimeier/twsapi).

When building from git checkout you need the autotools and gengetopt and don't
forget to type "autoreconf -vfi" first.

```
./configure
make
make install
```

Now you may just try such quick shot like getting account info:

```
twsdo -h localhost -p 7496 --get-account
```

Note there is still a lot debug output on stderr. Just redirect it into files:

```
twsdo -h localhost -p 7496 --get-account  2>bla.log  >bla.xml
```

## Usage

Currently we have just two commands. The job processor `twsdo` and job generator
`twsgen`.

`twsdo` reads jobs from file and writes TWS response to stdout.
`twsgen` reads such response file, creates new jobs of it or just converts it to
CSV and writes to stdout.

Note that both tools have not much functionality implemented yet, see

```
twsdo --help
```

```
twsgen --help
```

Rather than using twsgen you may create job files using any other tools like
editor, perl, sed, SQL, whatever.

See example scripts in `sample` directory.

# Notes on the `twsxml` Format

The purpose of `twsxml` format is to keep the data received by TWS as original as
possible. Doing something useful with that data is a separate process. If you
will notice that you misinterpreted the data then you are able to fix that and
parse it again.
This is the main advantage about a directly connected database backend.

The output files do NOT contain plain xml but serveral xml documents separated
by `\f` (form feed) characters.
The advantage is that you can use these files already before they are finished.
And if a tool is killed/crashed then the half ready output file contains always
valid data of what has been done so far.
The disadvantage is that existing xml tools can't handle them. However it just
takes one sed command line to convert them into plain xml. The twstools itself
are able to handle plain xml input because this is just the special case where a
file conatins one xml document only.

## Known Issues/TODO (feedback is welcome)

Although particular use cases are working nice and stable twstools development
is still in alpha state! The user interface and xml format is not stabilized
yet. But don't worry all future changes will be made as compatible as possible.

## Contact Information

If you have questions, bug reports, patches etc., contact
Ruediger Meier <sweet_f_a@gmx.de> (in English or German).
