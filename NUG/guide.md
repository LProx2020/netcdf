- \subpage netcdf_introduction
- \subpage getting_and_building_netcdf
- \subpage file_structure_and_performance
- \subpage data_type
- \subpage netcdf_data_set_components
- \subpage netcdf_perf_chunking
- \subpage netcdf_utilities_guide
- \subpage dap2
- \subpage dap4
- \subpage compress
- \subpage BestPractices
- \subpage user_defined_formats
- \subpage users_guide_appendices

## The Purpose of NetCDF

The purpose of the Network Common Data Form (netCDF) interface is to
allow you to create, access, and share array-oriented data in a form
that is self-describing and portable. "Self-describing" means that a
dataset includes information defining the data it contains. "Portable"
means that the data in a dataset is represented in a form that can be
accessed by computers with different ways of storing integers,
characters, and floating-point numbers. Using the netCDF interface for
creating new datasets makes the data portable. Using the netCDF
interface in software for data access, management, analysis, and
display can make the software more generally useful.

The netCDF software includes C, Fortran 77, Fortran 90, and C++
interfaces for accessing netCDF data. These libraries are available
for many common computing platforms.

The community of netCDF users has contributed ports of the software to
additional platforms and interfaces for other programming languages as
well. Source code for netCDF software libraries is freely available to
encourage the sharing of both array-oriented data and the software
that makes the data useful.

This User's Guide presents the netCDF data model. It explains how the
netCDF data model uses dimensions, variables, and attributes to store
data.

Reference documentation for UNIX systems, in the form of UNIX 'man'
pages for the C and FORTRAN interfaces is also available at the netCDF
web site (http://www.unidata.ucar.edu/netcdf), and with the netCDF
distribution.

The latest version of this document, and the language specific guides,
can be found at the netCDF web site (http://www.unidata.ucar.edu/netcdf/docs) along with extensive additional information about netCDF, including pointers to other software that works with netCDF data.

Separate documentation of the Java netCDF library can be found at
http://www.unidata.ucar.edu/software/netcdf-java/.

\page netcdf_introduction An Introduction to NetCDF

\tableofcontents

\section netcdf_interface The netCDF Interface

The Network Common Data Form, or netCDF, is an interface to a library
of data access functions for storing and retrieving data in the form
of arrays. An array is an n-dimensional (where n is 0, 1, 2, ...)
rectangular structure containing items which all have the same data
type (e.g., 8-bit character, 32-bit integer). A scalar (simple single
value) is a 0-dimensional array.

NetCDF is an abstraction that supports a view of data as a collection
of self-describing, portable objects that can be accessed through a
simple interface. Array values may be accessed directly, without
knowing details of how the data are stored. Auxiliary information
about the data, such as what units are used, may be stored with the
data. Generic utilities and application programs can access netCDF
datasets and transform, combine, analyze, or display specified fields
of the data. The development of such applications has led to improved
accessibility of data and improved re-usability of software for
array-oriented data management, analysis, and display.

The netCDF software implements an abstract data type, which means that
all operations to access and manipulate data in a netCDF dataset must
use only the set of functions provided by the interface. The
representation of the data is hidden from applications that use the
interface, so that how the data are stored could be changed without
affecting existing programs. The physical representation of netCDF
data is designed to be independent of the computer on which the data
were written.

Unidata supports the netCDF interfaces for C (see <a
href="http://www.unidata.ucar.edu/netcdf/docs/" >NetCDF-C User's
Guide</a>), FORTRAN 77 (see <a
href="http://www.unidata.ucar.edu/netcdf/documentation/historic/netcdf-f77/index.html#Top"
>NetCDF Fortran 77 Interface Guide</a>), FORTRAN 90 (see <a
href="http://www.unidata.ucar.edu/netcdf/documentation/historic/netcdf-f90/index.html"
>NetCDF Fortran 90 Interface Guide</a>), and C++ (see <a
href="http://www.unidata.ucar.edu/netcdf/documentation/historic/cxx4/index.html"
>NetCDF C++ Interface Guide</a>).

The netCDF library is supported for various UNIX operating systems. A
MS Windows port is also available. The software is also ported and
tested on a few other operating systems, with assistance from users
with access to these systems, before each major release. Unidata's
netCDF software is freely available <a
href="ftp://ftp.unidata.ucar.edu/pub/netcdf">via FTP</a> to encourage
its widespread use.

For detailed installation instructions, see \ref getting_and_building_netcdf.

\section netcdf_format The netCDF File Format

Until version 3.6.0, all versions of netCDF employed only one binary
data format, now referred to as netCDF classic format. NetCDF classic
is the default format for all versions of netCDF.

In version 3.6.0 a new binary format was introduced, 64-bit offset
format. Nearly identical to netCDF classic format, it uses 64-bit
offsets (hence the name), and allows users to create far larger
datasets. This format is also referred as CDF-2, because it bears the signature
string "CDF2" in the file header. After this extension, the classic file
format (i.e. not supporting 64-bit offsets) is now referred as CDF-1.

In version 4.0.0 a third binary format was introduced: the HDF5
format. Starting with this version, the netCDF library can use HDF5
files as its base format. (Only HDF5 files created with netCDF-4 can
be understood by netCDF-4).

Starting from version 4.4.0, netCDF included the support of CDF-5 format. In
order to allows defining large array variables with more than 4-billion
elements, CDF-5 replaces most of the 32-bit integers used to describe metadata
in file header with 64-bit integers. In addition, it supports the following new
external data types: NC_UBYTE, NC_USHORT, NC_UINT, NC_INT64, and NC_UINT64. The
CDF-5 format specifications can be found in
(http://cucis.ece.northwestern.edu/projects/PnetCDF/CDF-5.html).

The classic file formats are now referring to the collection of CDF-1, 2 and 5
formats. By default, netCDF uses the classic format (CDF-1). To use the CDF-2,
CDF-5, or netCDF-4/HDF5 format, set the appropriate constant in the file mode
argument when creating the file.

To achieve network-transparency (machine-independence), netCDF classic
formats are implemented in terms of an external
representation much like XDR (eXternal Data Representation, see
http://www.ietf.org/rfc/rfc1832.txt), a standard for describing and
encoding data. This representation provides encoding of data into
machine-independent sequences of bits. It has been implemented on a
wide variety of computers, by assuming only that eight-bit bytes can
be encoded and decoded in a consistent way. The IEEE 754
floating-point standard is used for floating-point data
representation.

Descriptions of the overall structure of netCDF classic files are provided
later in this manual. See \ref file_structure_and_performance.

The details of the CDF-1 and CDF-2 formats are described in
an appendix. See \ref netcdf_format. However, users are discouraged from
using the format specification to develop independent low-level
software for reading and writing netCDF files, because this could lead
to compatibility problems if the format is ever modified.

\subsection select_format How to Select the Format

With four different base formats, care must be taken in creating data
files to choose the correct base format.

The format of a netCDF file is determined at create time.

When opening an existing netCDF file the netCDF library will
transparently detect its format and adjust accordingly. However,
netCDF library versions earlier than 3.6.0 cannot read CDF-2
format files, library versions before 4.0 can't read netCDF-4/HDF5
files, and versions before 4.4.0 cannot read CDF-5 files.
NetCDF classic format files (even if created by version 3.6.0
or later) remain compatible with older versions of the netCDF library.

Users are encouraged to use netCDF classic CDF-1 format to distribute data,
for maximum portability.

To select CDF-2, CDF-5 or netCDF-4 format files, C programmers should use flag
NC_64BIT_OFFSET, NC_64BIT_DATA, or NC_NETCDF4 respectively in function
nc_create().

In Fortran, use flag nf_64bit_offset, nf_64bit_data, or nf_format_netcdf4 in
function NF_CREATE. See NF_CREATE.

It is also possible to change the default creation format, to convert
a large body of code without changing every create call. C programmers
see nc_set_default_format(). Fortran programs see NF_SET_DEFAULT_FORMAT.

\subsection classic_format NetCDF Classic Format (CDF-1)

The original netCDF format is identified using four bytes in the file
header. All files in this format have "CDF\001" at the beginning of
the file. In this documentation this format is referred to as CDF-1
format.

NetCDF CDF-1 format is identical to the format used by every
previous version of netCDF. It has maximum portability, and is still
the default netCDF format.

\subsection netcdf_64bit_offset_format NetCDF 64-bit Offset Format (CDF-2)

For some users, the various 2 GiB format limitations of the classic
format become a problem. (see \ref limitations).
For these users, 64-bit offset format is a natural choice. It greatly
eases the size restrictions of netCDF classic files (see \ref
limitations).

Files with the 64-bit offsets are identified with a "CDF\002" at the
beginning of the file. In this documentation this format is called
CDF-2 format.

Since CDF-2 format was introduced in version 3.6.0, earlier
versions of the netCDF library can't read CDF-2 files.

\subsection netcdf_64bit_data_format NetCDF 64-bit Data Format (CDF-5)

To allow large variables with more than 4-billion array elements,
64-bit data format is develop to support such I/O requests.

Files with the 64-bit data are identified with a "CDF\005" at the
beginning of the file. In this documentation this format is called
CDF-5 format.

Since CDF-5 format was introduced in version 4.4.0, earlier
versions of the netCDF library can't read CDF-5 files.

\subsection netcdf_4_format NetCDF-4 Format

In version 4.0, netCDF included another new underlying format: HDF5.

NetCDF-4 format files offer new features such as groups, compound
types, variable length arrays, new unsigned integer types, parallel
I/O access, etc. None of these new features can be used with classic
or 64-bit offset files.

NetCDF-4 files can't be created at all, unless the netCDF configure
script is run with –enable-netcdf-4. This also requires version 1.8.0
of HDF5.

For the netCDF-4.0 release, netCDF-4 features are only available from
the C and Fortran interfaces. We plan to bring netCDF-4 features to
the CXX API in a future release of netCDF.

NetCDF-4 files can't be read by any version of the netCDF library
previous to 4.0. (But they can be read by HDF5, version 1.8.0 or
better).

For more discussion of format issues see <a
href="http://www.unidata.ucar.edu/software/netcdf/docs/tutorial_8dox.html">The
NetCDF Tutorial</a>.

\section architecture NetCDF Library Architecture

\image html netcdf_architecture.png "NetCDF Architecture"
\image latex netcdf_architecture.png "NetCDF Architecture"
\image rtf netcdf_architecture.png "NetCDF Architecture"


The netCDF C-based libraries depend on a core C library and some externally developed libraries.

- NetCDF-Java is an independent implementation, not shown here
- C-based 3rd-party netCDF APIs for other languages include Python, Ruby, Perl, Fortran-2003, MATLAB, IDL, and R
- Libraries that don't support netCDF-4 include Perl and old C++
- 3rd party libraries are optional (HDF5, HDF4, zlib, szlib, pnetcdf, libcurl), depending on what features are needed and how netCDF is configured
- "Apps" in the above means applications, not mobile apps!

\section performance What about Performance?

One of the goals of netCDF is to support efficient access to small
subsets of large datasets. To support this goal, netCDF uses direct
access rather than sequential access. This can be much more efficient
when the order in which data is read is different from the order in
which it was written, or when it must be read in different orders for
different applications.

The amount of overhead for a portable external representation depends
on many factors, including the data type, the type of computer, the
granularity of data access, and how well the implementation has been
tuned to the computer on which it is run. This overhead is typically
small in comparison to the overall resources used by an
application. In any case, the overhead of the external representation
layer is usually a reasonable price to pay for portable data access.

Although efficiency of data access has been an important concern in
designing and implementing netCDF, it is still possible to use the
netCDF interface to access data in inefficient ways: for example, by
requesting a slice of data that requires a single value from each
record. Advice on how to use the interface efficiently is provided in
\ref file_structure_and_performance.

The use of HDF5 as a data format adds significant overhead in metadata
operations, less so in data access operations. We continue to study
the challenge of implementing netCDF-4/HDF5 format without
compromising performance.

\section creating_self Creating Self-Describing Data conforming to Conventions

The mere use of netCDF is not sufficient to make data
"self-describing" and meaningful to both humans and machines. The
names of variables and dimensions should be meaningful and conform to
any relevant conventions. Dimensions should have corresponding
coordinate variables (See \ref coordinate_variables) where sensible.

Attributes play a vital role in providing ancillary information. It is
important to use all the relevant standard attributes using the
relevant conventions. For a description of reserved attributes (used
by the netCDF library) and attribute conventions for generic
application software, see \ref attribute_conventions.

A number of groups have defined their own additional conventions and
styles for netCDF data. Descriptions of these conventions, as well as
examples incorporating them can be accessed from the netCDF
Conventions site, http://www.unidata.ucar.edu/netcdf/conventions.html.

These conventions should be used where suitable. Additional
conventions are often needed for local use. These should be
contributed to the above netCDF conventions site if likely to interest
other users in similar areas.

\section limitations Limitations of netCDF

The netCDF classic data model is widely applicable to data that can be
organized into a collection of named array variables with named
attributes, but there are some important limitations to the model and
its implementation in software. Some of these limitations have been
removed or relaxed in netCDF-4 files, but still apply to netCDF
classic and netCDF 64-bit offset files.

NetCDF classic CDF-1 and CDF-2 formats offer a limited
number of external numeric data types: 8-, 16-, 32-bit integers, or
32- or 64-bit floating-point numbers. The CDF-5 and netCDF-4 formats add 64-bit
integer types and unsigned integer types.

With the netCDF-4/HDF5 format, new unsigned integers (of various
sizes), 64-bit integers, and the string type allow improved expression
of meaning in scientific data. The new VLEN (variable length) and
COMPOUND types allow users to organize data in new ways.

With the classic CDF-1 file format, there are constraints that limit
how a dataset is structured to store more than 2 GiBytes (a GiByte is
2^30 or 1,073,741,824 bytes, as compared to a Gbyte, which is
1,000,000,000 bytes.) of data in a single netCDF dataset. (see \ref
limitations). This limitation is a result of 32-bit offsets used for
storing relative offsets within a classic netCDF format file. Since
one of the goals of netCDF is portable data, and some file systems
still can't deal with files larger than 2 GiB, it is best to keep
files that must be portable below this limit. Nevertheless, it is
possible to create and access netCDF files larger than 2 GiB on
platforms that provide support for such files (see
\ref large_file_support).

The CDF-2 format allows large files, and makes it easy to
create to create fixed variables of about 4 GiB, and record variables
of about 4 GiB per record. (see \ref netcdf_64bit_offset_format). However,
old netCDF applications will not be able to read the 64-bit offset
files until they are upgraded to at least version 3.6.0 of netCDF
(i.e. the version in which 64-bit offset format was introduced).

With the netCDF-4/HDF5 format, size limitations are further relaxed,
and files can be as large as the underlying file system
supports. NetCDF-4/HDF5 files are unreadable to the netCDF library
before version 4.0.

Similarly, CDF-5 format uses 64-bit integers to allow users to define
large variables. CDF-5 files are not unreadable to the netCDF library
before version 4.4.0.

Another limitation of the classic formats (CDF-1, 2 and 5) is that
only one unlimited (changeable) dimension is permitted for each netCDF
data set. Multiple variables can share an unlimited dimension, but
then they must all grow together. Hence the classic netCDF model does
not permit variables with several unlimited dimensions or the use of
multiple unlimited dimensions in different variables within the same
dataset. Variables that have non-rectangular shapes (for example,
ragged arrays) cannot be represented conveniently.

In netCDF-4/HDF5 files, multiple unlimited dimensions are fully
supported. Any variable can be defined with any combination of limited
and unlimited dimensions.

The extent to which data can be completely self-describing is limited:
there is always some assumed context without which sharing and
archiving data would be impractical. NetCDF permits storing meaningful
names for variables, dimensions, and attributes; units of measure in a
form that can be used in computations; text strings for attribute
values that apply to an entire data set; and simple kinds of
coordinate system information. But for more complex kinds of metadata
(for example, the information necessary to provide accurate
georeferencing of data on unusual grids or from satellite images), it
is often necessary to develop conventions.

Specific additions to the netCDF data model might make some of these
conventions unnecessary or allow some forms of metadata to be
represented in a uniform and compact way. For example, adding explicit
georeferencing to the netCDF data model would simplify elaborate
georeferencing conventions at the cost of complicating the model. The
problem is finding an appropriate trade-off between the richness of
the model and its generality (i.e., its ability to encompass many
kinds of data). A data model tailored to capture the shared context
among researchers within one discipline may not be appropriate for
sharing or combining data from multiple disciplines.

The classic netCDF data model (which is used for classic CDF-1, 2 and 5 format
data) does not support nested data structures
such as trees, nested arrays, or other recursive structures. Through
use of indirection and conventions it is possible to represent some
kinds of nested structures, but the result may fall short of the
netCDF goal of self-describing data.

In netCDF-4/HDF5 format files, the introduction of the compound type
allows the creation of complex data types, involving any combination
of types. The VLEN type allows efficient storage of ragged arrays, and
the introduction of hierarchical groups allows users new ways to
organize data.

NetCDF-4 supports parallel read/write access to netCDF-4/HDF5 files,
using the underlying HDF5 library and parallel read/write access to
classic files using the PnetCDf library.

For more information about HDF5, see the HDF5 web site:
http://hdfgroup.org/HDF5/.

For more information about PnetCDF, see their web site:
https://parallel-netcdf.github.io/.

\page netcdf_data_set_components The Components of a NetCDF Data Set

\tableofcontents

\section data_model The Data Model

A netCDF dataset contains dimensions, variables, and attributes, which
all have both a name and an ID number by which they are
identified. These components can be used together to capture the
meaning of data and relations among data fields in an array-oriented
dataset. The netCDF library allows simultaneous access to multiple
netCDF datasets which are identified by dataset ID numbers, in
addition to ordinary file names.

\subsection Enhanced Data Model in NetCDF-4/HDF5 Files

Files created with the netCDF-4 format have access to an enhanced data
model, which includes named groups. Groups, like directories in a Unix
file system, are hierarchically organized, to arbitrary depth. They
can be used to organize large numbers of variables.

\image html nc4-model.png "Enhanced NetCDF Data Model"
\image latex nc4-model.png "Enhanced NetCDF Data Model"
\image rtf nc4-model.png "Enhanced NetCDF Data Model"

Each group acts as an entire netCDF dataset in the classic model. That
is, each group may have attributes, dimensions, and variables, as well
as other groups.

The default group is the root group, which allows the classic netCDF
data model to fit neatly into the new model.

Dimensions are scoped such that they can be seen in all descendant
groups. That is, dimensions can be shared between variables in
different groups, if they are defined in a parent group.

In netCDF-4 files, the user may also define a type. For example a
compound type may hold information from an array of C structures, or a
variable length type allows the user to read and write arrays of
variable length values.

Variables, groups, and types share a namespace. Within the same group,
variables, groups, and types must have unique names. (That is, a type
and variable may not have the same name within the same group, and
similarly for sub-groups of that group.)

Groups and user-defined types are only available in files created in
the netCDF-4/HDF5 format. They are not available for classic format files.

\section dimensions Dimensions

A dimension may be used to represent a real physical dimension, for
example, time, latitude, longitude, or height. A dimension might also
be used to index other quantities, for example station or
model-run-number.

A netCDF dimension has both a name and a length.

A dimension length is an arbitrary positive integer, except that only one
dimension in a classic netCDF dataset can have the
length UNLIMITED. In a netCDF-4 dataset, any number of unlimited
dimensions can be used.

Such a dimension is called the unlimited dimension or the record
dimension. A variable with an unlimited dimension can grow to any
length along that dimension. The unlimited dimension index is like a
record number in conventional record-oriented files.

A netCDF classic dataset can have at most one
unlimited dimension, but need not have any. If a variable has an
unlimited dimension, that dimension must be the most significant
(slowest changing) one. Thus any unlimited dimension must be the first
dimension in a CDL shape and the first dimension in corresponding C
array declarations.

A netCDF-4 dataset may have multiple unlimited dimensions, and there
are no restrictions on their order in the list of a variables
dimensions.

To grow variables along an unlimited dimension, write the data using
any of the netCDF data writing functions, and specify the index of the
unlimited dimension to the desired record number. The netCDF library
will write however many records are needed (using the fill value,
unless that feature is turned off, to fill in any intervening
records).

CDL dimension declarations may appear on one or more lines following
the CDL keyword dimensions. Multiple dimension declarations on the
same line may be separated by commas. Each declaration is of the form
name = length. Use the “/” character to include group information
(netCDF-4 output only).

There are four dimensions in the above example: lat, lon, level, and
time (see \ref data_model). The first three are assigned fixed
lengths; time is assigned the length UNLIMITED, which means it is the
unlimited dimension.

The basic unit of named data in a netCDF dataset is a variable. When a
variable is defined, its shape is specified as a list of
dimensions. These dimensions must already exist. The number of
dimensions is called the rank (a.k.a. dimensionality). A scalar
variable has rank 0, a vector has rank 1 and a matrix has rank 2.

It is possible (since version 3.1 of netCDF) to use the same dimension
more than once in specifying a variable shape. For example,
correlation(instrument, instrument) could be a matrix giving
correlations between measurements using different instruments. But
data whose dimensions correspond to those of physical space/time
should have a shape comprising different dimensions, even if some of
these have the same length.

\section variables Variables

Variables are used to store the bulk of the data in a netCDF
dataset. A variable represents an array of values of the same type. A
scalar value is treated as a 0-dimensional array. A variable has a
name, a data type, and a shape described by its list of dimensions
specified when the variable is created. A variable may also have
associated attributes, which may be added, deleted or changed after
the variable is created.

A variable external data type is one of a small set of netCDF
types. In classic CDF-1 and 2 files, only the original six types
are available (byte, character, short, int, float, and
double). CDF-5 adds unsigned byte, unsigned short, unsigned int, 64-bit int,
and unsigned 64-bit int.  In netCDF-4, variables may also use these additional
data types, plus the string data type. Or the user
may define a type, as an opaque blob of bytes, as an array of variable
length arrays, or as a compound type, which acts like a C struct. (See
\ref data_type).

In the CDL notation, classic data type can be used. They
are given the simpler names byte, char, short, int, float, and
double. The name real may be used as a synonym for float in the CDL
notation. The name long is a deprecated synonym for int. For the exact
meaning of each of the types see External Types. The ncgen utility
supports new primitive types with names ubyte, ushort, uint, int64,
uint64, and string.

CDL variable declarations appear after the variable keyword in a CDL
unit. They have the form

\code
          type variable_name ( dim_name_1, dim_name_2, ... );
\endcode

for variables with dimensions, or

\code
          type variable_name;
\endcode

for scalar variables.

In the above CDL example there are six variables. As discussed below,
four of these are coordinate variables (See \ref
coordinate_variables). The remaining variables (sometimes called
primary variables), temp and rh, contain what is usually thought of as
the data. Each of these variables has the unlimited dimension time as
its first dimension, so they are called record variables. A variable
that is not a record variable has a fixed length (number of data
values) given by the product of its dimension lengths. The length of a
record variable is also the product of its dimension lengths, but in
this case the product is variable because it involves the length of
the unlimited dimension, which can vary. The length of the unlimited
dimension is the number of records.

\section coordinate_variables Coordinate Variables

It is legal for a variable to have the same name as a dimension. Such
variables have no special meaning to the netCDF library. However there
is a convention that such variables should be treated in a special way
by software using this library.

A variable with the same name as a dimension is called a
<em>coordinate variable</em>. It typically defines a physical coordinate corresponding to
that dimension. The above CDL example includes the coordinate
variables lat, lon, level and time, defined as follows:

\code
             int     lat(lat), lon(lon), level(level);
             short   time(time);
     ...
     data:
             level   = 1000, 850, 700, 500;
             lat     = 20, 30, 40, 50, 60;
             lon     = -160,-140,-118,-96,-84,-52,-45,-35,-25,-15;
             time    = 12;
\endcode

These define the latitudes, longitudes, barometric pressures and times
corresponding to positions along these dimensions. Thus there is data
at altitudes corresponding to 1000, 850, 700 and 500 millibars; and at
latitudes 20, 30, 40, 50 and 60 degrees north. Note that each
coordinate variable is a vector and has a shape consisting of just the
dimension with the same name.

A position along a dimension can be specified using an index. This is
an integer with a minimum value of 0 for C programs, 1 in Fortran
programs. Thus the 700 millibar level would have an index value of 2
in the example above in a C program, and 3 in a Fortran program.

If a dimension has a corresponding coordinate variable, then this
provides an alternative, and often more convenient, means of
specifying position along it. Current application packages that make
use of coordinate variables commonly assume they are numeric vectors
and strictly monotonic (all values are different and either increasing
or decreasing).

\section attributes Attributes

NetCDF attributes are used to store data about the data (ancillary
data or metadata), similar in many ways to the information stored in
data dictionaries and schema in conventional database systems. Most
attributes provide information about a specific variable. These are
identified by the name (or ID) of that variable, together with the
name of the attribute.

Some attributes provide information about the dataset as a whole and
are called global attributes. These are identified by the attribute
name together with a blank variable name (in CDL) or a special null
"global variable" ID (in C or Fortran).

In netCDF-4 file, attributes can also be added at the group level.

An attribute has an associated variable (the null "global variable"
for a global or group-level attribute), a name, a data type, a length,
and a value. The current version treats all attributes as vectors;
scalar values are treated as single-element vectors.

Conventional attribute names should be used where applicable. New
names should be as meaningful as possible.

The external type of an attribute is specified when it is created. The
types permitted for attributes are the same as the netCDF external
data types for variables. Attributes with the same name for different
variables should sometimes be of different types. For example, the
attribute valid_max specifying the maximum valid data value for a
variable of type int should be of type int, whereas the attribute
valid_max for a variable of type double should instead be of type
double.

Attributes are more dynamic than variables or dimensions; they can be
deleted and have their type, length, and values changed after they are
created, whereas the netCDF interface provides no way to delete a
variable or to change its type or shape.

The CDL notation for defining an attribute is

\code
         variable_name:attribute_name = list_of_values;
\endcode

for a variable attribute, or

\code
         :attribute_name = list_of_values;
\endcode

for a global attribute.

For the netCDF classic model, the type and length of each attribute
are not explicitly declared in CDL; they are derived from the values
assigned to the attribute. All values of an attribute must be of the
same type. The notation used for constant values of the various netCDF
types is discussed later (see \ref cdl_syntax).

The extended CDL syntax for the enhanced data model supported by
netCDF-4 allows optional type specifications, including user-defined
types, for attributes of user-defined types. See ncdump output or the
reference documentation for ncgen for details of the extended CDL
syntax.

In the netCDF example (see \ref data_model), units is an attribute for
the variable lat that has a 13-character array value
'degrees_north'. And valid_range is an attribute for the variable rh
that has length 2 and values '0.0' and '1.0'.

One global attribute, called “source”, is defined for the example
netCDF dataset. This is a character array intended for documenting the
data. Actual netCDF datasets might have more global attributes to
document the origin, history, conventions, and other characteristics
of the dataset as a whole.

Most generic applications that process netCDF datasets assume standard
attribute conventions and it is strongly recommended that these be
followed unless there are good reasons for not doing so. For
information about units, long_name, valid_min, valid_max, valid_range,
scale_factor, add_offset, _FillValue, and other conventional
attributes, see \ref attribute_conventions.

Attributes may be added to a netCDF dataset long after it is first
defined, so you don't have to anticipate all potentially useful
attributes. However adding new attributes to an existing classic
format dataset can incur the same expense as copying the
dataset. For a more extensive discussion see \ref file_structure_and_performance.

\section differences_atts_vars Differences between Attributes and Variables

In contrast to variables, which are intended for bulk data, attributes
are intended for ancillary data, or information about the data. The
total amount of ancillary data associated with a netCDF object, and
stored in its attributes, is typically small enough to be
memory-resident. However variables are often too large to entirely fit
in memory and must be split into sections for processing.

Another difference between attributes and variables is that variables
may be multidimensional. Attributes are all either scalars
(single-valued) or vectors (a single, fixed dimension).

Variables are created with a name, type, and shape before they are
assigned data values, so a variable may exist with no values. The
value of an attribute is specified when it is created, unless it is a
zero-length attribute.

A variable may have attributes, but an attribute cannot have
attributes. Attributes assigned to variables may have the same units
as the variable (for example, valid_range) or have no units (for
example, scale_factor). If you want to store data that requires units
different from those of the associated variable, it is better to use a
variable than an attribute. More generally, if data require ancillary
data to describe them, are multidimensional, require any of the
defined netCDF dimensions to index their values, or require a
significant amount of storage, that data should be represented using
variables rather than attributes.

\section object_name NetCDF Names

\subsection Permitted Characters in NetCDF Names

The names of dimensions, variables and attributes (and, in netCDF-4
files, groups, user-defined types, compound member names, and
enumeration symbols) consist of arbitrary sequences of alphanumeric
characters, underscore '_', period '.', plus '+', hyphen '-', or at
sign '@', but beginning with an alphanumeric character or
underscore. However names commencing with underscore are reserved for
system use.

Beginning with versions 3.6.3 and 4.0, names may also include UTF-8
encoded Unicode characters as well as other special characters, except
for the character '/', which may not appear in a name.

Names that have trailing space characters are also not permitted.

Case is significant in netCDF names.

\subsection Name Length

A zero-length name is not allowed.

Names longer than ::NC_MAX_NAME will not be accepted any netCDF define
function. An error of ::NC_EMAXNAME will be returned.

All netCDF inquiry functions will return names of maximum size
::NC_MAX_NAME for netCDF files. Since this does not include the
terminating NULL, space should be reserved for NC_MAX_NAME + 1
characters.

\subsection NetCDF Conventions

Some widely used conventions restrict names to only alphanumeric
characters or underscores.

\note Note that, when using the DAP2 protocol to access netCDF data,
there are \em reserved keywords, the use of which may result in
undefined behavior.  See \ref dap2_reserved_keywords for more
information.

\section archival Is NetCDF a Good Archive Format?

NetCDF classic formats can be used as a
general-purpose archive format for storing arrays. Compression of data
is possible with netCDF (e.g., using arrays of eight-bit or 16-bit
integers to encode low-resolution floating-point numbers instead of
arrays of 32-bit numbers), or the resulting data file may be
compressed before storage (but must be uncompressed before it is
read). Hence, using these netCDF formats may require more space than
special-purpose archive formats that exploit knowledge of particular
characteristics of specific datasets.

With netCDF-4/HDF5 format, the zlib library can provide compression on
a per-variable basis. That is, some variables may be compressed,
others not. In this case the compression and decompression of data
happen transparently to the user, and the data may be stored, read,
and written compressed.


\section background Background and Evolution of the NetCDF Interface

The development of the netCDF interface began with a modest goal
related to Unidata's needs: to provide a common interface between
Unidata applications and real-time meteorological data. Since Unidata
software was intended to run on multiple hardware platforms with
access from both C and FORTRAN, achieving Unidata's goals had the
potential for providing a package that was useful in a broader
context. By making the package widely available and collaborating with
other organizations with similar needs, we hoped to improve the then
current situation in which software for scientific data access was
only rarely reused by others in the same discipline and almost never
reused between disciplines (Fulker, 1988).

Important concepts employed in the netCDF software originated in a
paper (Treinish and Gough, 1987) that described data-access software
developed at the NASA Goddard National Space Science Data Center
(NSSDC). The interface provided by this software was called the Common
Data Format (CDF). The NASA CDF was originally developed as a
platform-specific FORTRAN library to support an abstraction for
storing arrays.

The NASA CDF package had been used for many different kinds of data in
an extensive collection of applications. It had the virtues of
simplicity (only 13 subroutines), independence from storage format,
generality, ability to support logical user views of data, and support
for generic applications.

Unidata held a workshop on CDF in Boulder in August 1987. We proposed
exploring the possibility of collaborating with NASA to extend the CDF
FORTRAN interface, to define a C interface, and to permit the access
of data aggregates with a single call, while maintaining compatibility
with the existing NASA interface.

Independently, Dave Raymond at the New Mexico Institute of Mining and
Technology had developed a package of C software for UNIX that
supported sequential access to self-describing array-oriented data and
a "pipes and filters" (or "data flow") approach to processing,
analyzing, and displaying the data. This package also used the "Common
Data Format" name, later changed to C-Based Analysis and Display
System (CANDIS). Unidata learned of Raymond's work (Raymond, 1988),
and incorporated some of his ideas, such as the use of named
dimensions and variables with differing shapes in a single data
object, into the Unidata netCDF interface.

In early 1988, Glenn Davis of Unidata developed a prototype netCDF
package in C that was layered on XDR. This prototype proved that a
single-file, XDR-based implementation of the CDF interface could be
achieved at acceptable cost and that the resulting programs could be
implemented on both UNIX and VMS systems. However, it also
demonstrated that providing a small, portable, and NASA CDF-compatible
FORTRAN interface with the desired generality was not
practical. NASA's CDF and Unidata's netCDF have since evolved
separately, but recent CDF versions share many characteristics with
netCDF.

In early 1988, Joe Fahle of SeaSpace, Inc. (a commercial software
development firm in San Diego, California), a participant in the 1987
Unidata CDF workshop, independently developed a CDF package in C that
extended the NASA CDF interface in several important ways (Fahle,
1989). Like Raymond's package, the SeaSpace CDF software permitted
variables with unrelated shapes to be included in the same data object
and permitted a general form of access to multidimensional
arrays. Fahle's implementation was used at SeaSpace as the
intermediate form of storage for a variety of steps in their
image-processing system. This interface and format have subsequently
evolved into the Terascan data format.

After studying Fahle's interface, we concluded that it solved many of
the problems we had identified in trying to stretch the NASA interface
to our purposes. In August 1988, we convened a small workshop to agree
on a Unidata netCDF interface, and to resolve remaining open
issues. Attending were Joe Fahle of SeaSpace, Michael Gough of Apple
(an author of the NASA CDF software), Angel Li of the University of
Miami (who had implemented our prototype netCDF software on VMS and
was a potential user), and Unidata systems development
staff. Consensus was reached at the workshop after some further
simplifications were discovered. A document incorporating the results
of the workshop into a proposed Unidata netCDF interface specification
was distributed widely for comments before Glenn Davis and Russ Rew
implemented the first version of the software. Comparison with other
data-access interfaces and experience using netCDF are discussed in
Rew and Davis (1990a), Rew and Davis (1990b), Jenter and Signell
(1992), and Brown, Folk, Goucher, and Rew (1993).

In October 1991, we announced version 2.0 of the netCDF software
distribution. Slight modifications to the C interface (declaring
dimension lengths to be long rather than int) improved the usability
of netCDF on inexpensive platforms such as MS-DOS computers, without
requiring recompilation on other platforms. This change to the
interface required no changes to the associated file format.

Release of netCDF version 2.3 in June 1993 preserved the same file
format but added single call access to records, optimizations for
accessing cross-sections involving non-contiguous data, subsampling
along specified dimensions (using 'strides'), accessing non-contiguous
data (using 'mapped array sections'), improvements to the ncdump and
ncgen utilities, and an experimental C++ interface.

In version 2.4, released in February 1996, support was added for new
platforms and for the C++ interface, significant optimizations were
implemented for supercomputer architectures, and the file format was
formally specified in an appendix to the User's Guide.

FAN (File Array Notation), software providing a high-level interface
to netCDF data, was made available in May 1996. The capabilities of
the FAN utilities include extracting and manipulating array data from
netCDF datasets, printing selected data from netCDF arrays, copying
ASCII data into netCDF arrays, and performing various operations (sum,
mean, max, min, product, and others) on netCDF arrays.

In 1996 and 1997, Joe Sirott implemented and made available the first
implementation of a read-only netCDF interface for Java, Bill Noon
made a Python module available for netCDF, and Konrad Hinsen
contributed another netCDF interface for Python.

In May 1997, Version 3.3 of netCDF was released. This included a new
type-safe interface for C and Fortran, as well as many other
improvements. A month later, Charlie Zender released version 1.0 of
the NCO (netCDF Operators) package, providing command-line utilities
for general purpose operations on netCDF data.

Version 3.4 of Unidata's netCDF software, released in March 1998,
included initial large file support, performance enhancements, and
improved Cray platform support. Later in 1998, Dan Schmitt provided a
Tcl/Tk interface, and Glenn Davis provided version 1.0 of netCDF for
Java.

In May 1999, Glenn Davis, who was instrumental in creating and
developing netCDF, died in a small plane crash during a
thunderstorm. The memory of Glenn's passions and intellect continue to
inspire those of us who worked with him.

In February 2000, an experimental Fortran 90 interface developed by
Robert Pincus was released.

John Caron released netCDF for Java, version 2.0 in February
2001. This version incorporated a new high-performance package for
multidimensional arrays, simplified the interface, and included
OpenDAP (known previously as DODS) remote access, as well as remote
netCDF access via HTTP contributed by Don Denbo.

In March 2001, netCDF 3.5.0 was released. This release fully
integrated the new Fortran 90 interface, enhanced portability,
improved the C++ interface, and added a few new tuning functions.

Also in 2001, Takeshi Horinouchi and colleagues made a netCDF
interface for Ruby available, as did David Pierce for the R language
for statistical computing and graphics. Charles Denham released
WetCDF, an independent implementation of the netCDF interface for
Matlab, as well as updates to the popular netCDF Toolbox for Matlab.

In 2002, Unidata and collaborators developed NcML, an XML
representation for netCDF data useful for cataloging data holdings,
aggregation of data from multiple datasets, augmenting metadata in
existing datasets, and support for alternative views of data. The Java
interface currently provides access to netCDF data through NcML.

Additional developments in 2002 included translation of C and Fortran
User Guides into Japanese by Masato Shiotani and colleagues, creation
of a “Best Practices” guide for writing netCDF files, and provision of
an Ada-95 interface by Alexandru Corlan.

In July 2003 a group of researchers at Northwestern University and
Argonne National Laboratory (Jianwei Li, Wei-keng Liao, Alok
Choudhary, Robert Ross, Rajeev Thakur, William Gropp, and Rob Latham)
contributed a new parallel interface for writing and reading netCDF
data, tailored for use on high performance platforms with parallel
I/O. The implementation built on the MPI-IO interface, providing
portability to many platforms.

In October 2003, Greg Sjaardema contributed support for an alternative
format with 64-bit offsets, to provide more complete support for very
large files. These changes, with slight modifications at Unidata, were
incorporated into version 3.6.0, released in December, 2004.

In 2004, thanks to a NASA grant, Unidata and NCSA began a
collaboration to increase the interoperability of netCDF and HDF5, and
bring some advanced HDF5 features to netCDF users.

In February, 2006, release 3.6.1 fixed some minor bugs.

In March, 2007, release 3.6.2 introduced an improved build system that
used automake and libtool, and an upgrade to the most recent autoconf
release, to support shared libraries and the netcdf-4 builds. This
release also introduced the NetCDF Tutorial and example programs.

The first beta release of netCDF-4.0 was celebrated with a giant party
at Unidata in April, 2007. Over 2000 people danced 'til dawn at the
NCAR Mesa Lab, listening to the Flaming Lips and the Denver Gilbert &
Sullivan repertory company. Brittany Spears performed the
world-premire of her smash hit "Format me baby, one more time."

In June, 2008, netCDF-4.0 was released. Version 3.6.3, the same code
but with netcdf-4 features turned off, was released at the same
time. The 4.0 release uses HDF5 1.8.1 as the data storage layer for
netcdf, and introduces many new features including groups and
user-defined types. The 3.6.3/4.0 releases also introduced handling of
UTF8-encoded Unicode names.

NetCDF-4.1.1 was released in April, 2010, provided built-in client
support for the DAP protocol for accessing data from remote OPeNDAP
servers, full support for the enhanced netCDF-4 data model in the
ncgen utility, a new nccopy utility for copying and conversion among
netCDF format variants, ability to read some HDF4/HDF5 data archives
through the netCDF C or Fortran interfaces, support for parallel I/O
on netCDF classic files (CDF-1, 2, and 5) using the PnetCDF
library from Argonne/Northwestern, a new nc-config
utility to help compile and link programs that use netCDF, inclusion
of the UDUNITS library for handling “units” attributes, and inclusion
of libcf to assist in creating data compliant with the Climate and
Forecast (CF) metadata conventions.

In September, 2010, the Netcdf-Java/CDM (Common Data Model) version
4.2 library was declared stable and made available to users. This
100%-Java implementation provided a read-write interface to netCDF-3
classic format files, as well as a read-only interface to
netCDF-4 enhanced model data and many other formats of scientific data
through a common (CDM) interface. More recent releases support
writing netCDF-4 data. The NetCDF-Java library also
implements NcML, which allows you to add metadata to CDM datasets. A ToolsUI
application is also included that provides a graphical user interface
to capabilities similar to the C-based ncdump and ncgen utilities, as
well as CF-compliance checking and many other features.



\section remote_client The Remote Data Access Client

Starting with version 4.1.1 the netCDF C libraries and utilities have
supported remote data access.

\section data_access Data Access

To access (read or write) netCDF data you specify an open netCDF
dataset, a netCDF variable, and information (e.g., indices)
identifying elements of the variable. The name of the access function
corresponds to the internal type of the data. If the internal type has
a different representation from the external type of the variable, a
conversion between the internal type and external type will take place
when the data is read or written.

Access to data in classic formats is direct. Access
to netCDF-4 data is buffered by the HDF5 layer. In either case you can
access a small subset of data from a large dataset efficiently,
without first accessing all the data that precedes it.

Reading and writing data by specifying a variable, instead of a
position in a file, makes data access independent of how many other
variables are in the dataset, making programs immune to data format
changes that involve adding more variables to the data.

In the C and FORTRAN interfaces, datasets are not specified by name
every time you want to access data, but instead by a small integer
called a dataset ID, obtained when the dataset is first created or
opened.

Similarly, a variable is not specified by name for every data access
either, but by a variable ID, a small integer used to identify each
variable in a netCDF dataset.

\section forms_of_data_access Forms of Data Access

The netCDF interface supports several forms of direct access to data
values in an open netCDF dataset. We describe each of these forms of
access in order of increasing generality:
- access to all elements;
- access to individual elements, specified with an index vector;
- access to array sections, specified with an index vector, and count vector;
- access to sub-sampled array sections, specified with an index
  vector, count vector, and stride vector; and
- access to mapped array sections, specified with an index vector,
  count vector, stride vector, and an index mapping vector.

The four types of vector (index vector, count vector, stride vector
and index mapping vector) each have one element for each dimension of
the variable. Thus, for an n-dimensional variable (rank = n),
n-element vectors are needed. If the variable is a scalar (no
dimensions), these vectors are ignored.

An array section is a "slab" or contiguous rectangular block that is
specified by two vectors. The index vector gives the indices of the
element in the corner closest to the origin. The count vector gives
the lengths of the edges of the slab along each of the variable's
dimensions, in order. The number of values accessed is the product of
these edge lengths.

A subsampled array section is similar to an array section, except that
an additional stride vector is used to specify sampling. This vector
has an element for each dimension giving the length of the strides to
be taken along that dimension. For example, a stride of 4 means every
fourth value along the corresponding dimension. The total number of
values accessed is again the product of the elements of the count
vector.

A mapped array section is similar to a subsampled array section except
that an additional index mapping vector allows one to specify how data
values associated with the netCDF variable are arranged in memory. The
offset of each value from the reference location, is given by the sum
of the products of each index (of the imaginary internal array which
would be used if there were no mapping) by the corresponding element
of the index mapping vector. The number of values accessed is the same
as for a subsampled array section.

The use of mapped array sections is discussed more fully below, but
first we present an example of the more commonly used array-section
access.

\section c_array_section_access A C Example of Array-Section Access

Assume that in our earlier example of a netCDF dataset, we wish to
read a cross-section of all the data for the temp variable at one
level (say, the second), and assume that there are currently three
records (time values) in the netCDF dataset. Recall that the
dimensions are defined as

\code
       lat = 5, lon = 10, level = 4, time = unlimited;
\endcode

and the variable temp is declared as

\code
       float   temp(time, level, lat, lon);
\endcode

in the CDL notation.

A corresponding C variable that holds data for only one level might be
declared as:

\code
     #define LATS  5
     #define LONS 10
     #define LEVELS 1
     #define TIMES 3                 /* currently */
         ...
     float   temp[TIMES*LEVELS*LATS*LONS];
\endcode

     to keep the data in a one-dimensional array, or

\code
         ...
     float   temp[TIMES][LEVELS][LATS][LONS];
\endcode

using a multidimensional array declaration.

To specify the block of data that represents just the second level,
all times, all latitudes, and all longitudes, we need to provide a
start index and some edge lengths. The start index should be (0, 1, 0,
0) in C, because we want to start at the beginning of each of the
time, lon, and lat dimensions, but we want to begin at the second
value of the level dimension. The edge lengths should be (3, 1, 5, 10)
in C, (since we want to get data for all three time values, only one
level value, all five lat values, and all 10 lon values. We should
expect to get a total of 150 floating-point values returned (3 * 1 * 5
* 10), and should provide enough space in our array for this many. The
order in which the data will be returned is with the last dimension,
lon, varying fastest:

\code
          temp[0][1][0][0]
          temp[0][1][0][1]
          temp[0][1][0][2]
          temp[0][1][0][3]

                ...

          temp[2][1][4][7]
          temp[2][1][4][8]
          temp[2][1][4][9]
\endcode

Different dimension orders for the C, FORTRAN, or other language
interfaces do not reflect a different order for values stored on the
disk, but merely different orders supported by the procedural
interfaces to the languages. In general, it does not matter whether a
netCDF dataset is written using the C, FORTRAN, or another language
interface; netCDF datasets written from any supported language may be
read by programs written in other supported languages.  3.4.3 More on
General Array Section Access for C

The use of mapped array sections allows non-trivial relationships
between the disk addresses of variable elements and the addresses
where they are stored in memory. For example, a matrix in memory could
be the transpose of that on disk, giving a quite different order of
elements. In a regular array section, the mapping between the disk and
memory addresses is trivial: the structure of the in-memory values
(i.e., the dimensional lengths and their order) is identical to that
of the array section. In a mapped array section, however, an index
mapping vector is used to define the mapping between indices of netCDF
variable elements and their memory addresses.

With mapped array access, the offset (number of array elements) from
the origin of a memory-resident array to a particular point is given
by the inner product[1] of the index mapping vector with the point's
coordinate offset vector. A point's coordinate offset vector gives,
for each dimension, the offset from the origin of the containing array
to the point.In C, a point's coordinate offset vector is the same as
its coordinate vector.

The index mapping vector for a regular array section would have–in
order from most rapidly varying dimension to most slowly–a constant 1,
the product of that value with the edge length of the most rapidly
varying dimension of the array section, then the product of that value
with the edge length of the next most rapidly varying dimension, and
so on. In a mapped array, however, the correspondence between netCDF
variable disk locations and memory locations can be different.

For example, the following C definitions:

\code
     struct vel {
         int flags;
         float u;
         float v;
     } vel[NX][NY];
     ptrdiff_t imap[2] = {
         sizeof(struct vel),
         sizeof(struct vel)*NY
     };
\endcode

where imap is the index mapping vector, can be used to access the
memory-resident values of the netCDF variable, vel(NY,NX), even though
the dimensions are transposed and the data is contained in a 2-D array
of structures rather than a 2-D array of floating-point values.

A detailed example of mapped array access is presented in the
description of the interfaces for mapped array access. See Write a
Mapped Array of Values - nc_put_varm_ type.

Note that, although the netCDF abstraction allows the use of
subsampled or mapped array-section access there use is not
required. If you do not need these more general forms of access, you
may ignore these capabilities and use single value access or regular
array section access instead.

\page file_structure_and_performance File Structure and Performance

\tableofcontents

\section  classic_file_parts Parts of a NetCDF Classic File

A netCDF classic dataset (including CDF-1, 2, and 5 formats) is stored as a
single file comprising two parts:
- a header, containing all the information about dimensions, attributes,
and variables except for the variable data;
- a data part, comprising fixed-size data, containing the data for
variables that don't have an unlimited dimension; and variable-size
data, containing the data for variables that have an unlimited
dimension.

Both the header and data parts are represented in a
machine-independent form. This form is very similar to XDR (eXternal
Data Representation), extended to support efficient storage of arrays
of non-byte data.

The header at the beginning of the file contains information about the
dimensions, variables, and attributes in the file, including their
names, types, and other characteristics. The information about each
variable includes the offset to the beginning of the variable's data
for fixed-size variables or the relative offset of other variables
within a record. The header also contains dimension lengths and
information needed to map multidimensional indices for each variable
to the appropriate offsets.

By default, this header has little usable extra space; it is only as
large as it needs to be for the dimensions, variables, and attributes
(including all the attribute values) in the netCDF dataset, with a
small amount of extra space from rounding up to the nearest disk block
size. This has the advantage that netCDF files are compact, requiring
very little overhead to store the ancillary data that makes the
datasets self-describing. A disadvantage of this organization is that
any operation on a netCDF dataset that requires the header to grow
(or, less likely, to shrink), for example adding new dimensions or new
variables, requires moving the data by copying it. This expense is
incurred when the enddef function is called: nc_enddef() in C,
NF_ENDDEF() in Fortran, after a previous call to the redef function:
nc_redef() in C or NF_REDEF() in Fortran. If you create all necessary
dimensions, variables, and attributes before writing data, and avoid
later additions and renamings of netCDF components that require more
space in the header part of the file, you avoid the cost associated
with later changing the header.

Alternatively, you can use an alternative version of the enddef
function with two underbar characters instead of one to explicitly
reserve extra space in the file header when the file is created: in C
nc__enddef(), in Fortran NF__ENDDEF(), after a previous call to the
redef function. This avoids the expense of moving all the data later
by reserving enough extra space in the header to accommodate
anticipated changes, such as the addition of new attributes or the
extension of existing string attributes to hold longer strings.

When the size of the header is changed, data in the file is moved, and
the location of data values in the file changes. If another program is
reading the netCDF dataset during redefinition, its view of the file
will be based on old, probably incorrect indexes. If netCDF datasets
are shared across redefinition, some mechanism external to the netCDF
library must be provided that prevents access by readers during
redefinition, and causes the readers to call nc_sync/NF_SYNC before
any subsequent access.

The fixed-size data part that follows the header contains all the
variable data for variables that do not employ an unlimited
dimension. The data for each variable is stored contiguously in this
part of the file. If there is no unlimited dimension, this is the last
part of the netCDF file.

The record-data part that follows the fixed-size data consists of a
variable number of fixed-size records, each of which contains data for
all the record variables. The record data for each variable is stored
contiguously in each record.

The order in which the variable data appears in each data section is
the same as the order in which the variables were defined, in
increasing numerical order by netCDF variable ID. This knowledge can
sometimes be used to enhance data access performance, since the best
data access is currently achieved by reading or writing the data in
sequential order.

\section parts_of_netcdf4 Parts of a NetCDF-4 HDF5 File

NetCDF-4 files are created with the HDF5 library, and are HDF5 files
in every way, and can be read without the netCDF-4 interface. (Note
that modifying these files with HDF5 will almost certainly make them
unreadable to netCDF-4.)

Groups in a netCDF-4 file correspond with HDF5 groups (although the
netCDF-4 tree is rooted not at the HDF5 root, but in group “_netCDF”).

Variables in netCDF coorespond with identically named datasets in
HDF5. Attributes similarly.

Since there is more metadata in a netCDF file than an HDF5 file,
special datasets are used to hold netCDF metadata.

\deprecated The _netcdf_dim_info dataset (in group _netCDF) contains the ids of
the shared dimensions, and their length (0 for unlimited dimensions).

\deprecated The _netcdf_var_info dataset (in group _netCDF) holds an array of
compound types which contain the variable ID, and the associated
dimension ids.

\section xdr_layer The Extended XDR Layer

XDR is a standard for describing and encoding data and a library of
functions for external data representation, allowing programmers to
encode data structures in a machine-independent way. Classic
netCDF employs an extended form of XDR for representing
information in the header part and the data parts. This extended XDR
is used to write portable data that can be read on any other machine
for which the library has been implemented.

The cost of using a canonical external representation for data varies
according to the type of data and whether the external form is the
same as the machine's native form for that type.

For some data types on some machines, the time required to convert
data to and from external form can be significant. The worst case is
reading or writing large arrays of floating-point data on a machine
that does not use IEEE floating-point as its native representation.

\section large_file_support Large File Support

It is possible to write netCDF files that exceed 2 GiByte on platforms
that have "Large File Support" (LFS). Such files are
platform-independent to other LFS platforms, but trying to open them
on an older platform without LFS yields a "file too large" error.

Without LFS, no files larger than 2 GiBytes can be used. The rest of
this section applies only to systems with LFS.

The original binary format of netCDF (classic format) limits the size
of data files by using a signed 32-bit offset within its internal
structure. Files larger than 2 GiB can be created, with certain
limitations. See \ref limitations.

In version 3.6.0, netCDF included its first-ever variant of the
underlying data format. The new format introduced in 3.6.0 uses 64-bit
file offsets in place of the 32-bit offsets. The new format is also referred as
CDF-2 format as it bears a signature string of "CDF2" in the file header.
There are still some
limits on the sizes of variables, but the new format can create very
large datasets. See \ref netcdf_64bit_offset_format.

Starting from version 4.4.0, netCDF included the support of CDF-5 format.  In
order to allows defining large array variables with more than 4-billion
elements, CDF-5 replaces most of the 32-bit integers used to describe metadata
with 64-bit integers. In addition, it supports the following new external data
types: NC_UBYTE, NC_USHORT, NC_UINT, NC_INT64, and NC_UINT64. The CDF-5 format
specifications can be found in
(http://cucis.ece.northwestern.edu/projects/PnetCDF/CDF-5.html).

NetCDF-4 variables and files can be any size supported by the
underlying file system.

The original data format (netCDF classic), is still the default data
format for the netCDF library.

The following table summarizes the size limitations of various
permutations of LFS support, netCDF version, and data format. Note
that 1 GiB = 2^30 bytes or about 1.07e+9 bytes, 1 EiB = 2^60 bytes or
about 1.15e+18 bytes. Note also that all sizes are really 4 bytes less
than the ones given below. For example the maximum size of a fixed
variable in netCDF 3.6 classic format is really 2 GiB - 4 bytes.

Limits                                     | No LFS     |  v3.5   |  v3.6/classic |  v3.6/64-bit offset |    v4.0/netCDF-4 and CDF-5
-------------------------------------------|------------|---------|---------------|---------------------|-----------------
Max File Size                              | 2 GiB      | 8 EiB   |  8 EiB        |  8 EiB              | unlimited
Max Number of Fixed Vars > 2 GiB           |     0      | 1 (last)|  1 (last)     |  2^32               | unlimited
Max Record Vars w/ Rec Size > 2 GiB        |     0      | 1 (last)|  1 (last)     |  2^32               | unlimited
Max Size of Fixed/Record Size of Record Var| 2 GiB      | 2 GiB   |  2 GiB        |  4 GiB              | unlimited
Max Record Size                            | 2 GiB/nrecs| 4 GiB   |  8 EiB/nrecs  |  8 EiB/nrecs        | unlimited

For more information about the different file formats of netCDF see
\ref select_format "How to Select the Format".

\section offset_format_limitations NetCDF 64-bit Offset Format Limitations

Although the 64-bit offset format (CDF-2) allows the creation of much larger
netCDF files than was possible with the classic format, there are
still some restrictions on the size of variables.

It's important to note that without Large File Support (LFS) in the
operating system, it's impossible to create any file larger than 2
GiBytes. Assuming an operating system with LFS, the following
restrictions apply to the CDF-2 format.

No fixed-size variable can require more than 2^32 - 4 bytes (i.e. 4GiB
- 4 bytes, or 4,294,967,292 bytes) of storage for its data, unless it
is the last fixed-size variable and there are no record
variables. When there are no record variables, the last fixed-size
variable can be any size supported by the file system, e.g. terabytes.

A CDF-2 file can have up to 2^32 - 1 fixed sized
variables, each under 4GiB in size. If there are no record variables
in the file the last fixed variable can be any size.

No record variable can require more than 2^32 - 4 bytes of storage for
each record's worth of data, unless it is the last record variable. A
CDF-2 file can have up to 2^32 - 1 records, of
up to 2^32 - 1 variables, as long as the size of one record's data for
each record variable except the last is less than 4 GiB - 4.

Note also that all netCDF variables and records are padded to 4 byte
boundaries.

\section classic_format_limitations NetCDF Classic Format Limitations

There are important constraints on the structure of large netCDF
classic files that result from the 32-bit relative offsets that are
part of the netCDF classic file format (CDF-1):

The maximum size of a record in the classic format in versions 3.5.1
and earlier is 2^32 - 4 bytes, or about 4 GiB. In versions 3.6.0 and
later, there is no such restriction on total record size for the
classic formats.

If you don't use the unlimited dimension, only one variable can exceed
2 GiB in size, but it can be as large as the underlying file system
permits. It must be the last variable in the dataset, and the offset
to the beginning of this variable must be less than about 2 GiB.

The limit is really 2^31 - 4. If you were to specify a variable size
of 2^31 -3, for example, it would be rounded up to the nearest
multiple of 4 bytes, which would be 2^31, which is larger than the
largest signed integer, 2^31 - 1.

For example, the structure of the data might be something like:

\code
     netcdf bigfile1 {
         dimensions:
            x=2000;
            y=5000;
            z=10000;
         variables:
            double x(x);         // coordinate variables
            double y(y);
            double z(z);
            double var(x, y, z); // 800 Gbytes
         }
\endcode

If you use the unlimited dimension, record variables may exceed 2 GiB
in size, as long as the offset of the start of each record variable
within a record is less than 2 GiB - 4. For example, the structure of
the data in a 2.4 Tbyte file might be something like:

\code
     netcdf bigfile2 {
         dimensions:
            x=2000;
            y=5000;
            z=10;
            t=UNLIMITED;         // 1000 records, for example
         variables:
            double x(x);         // coordinate variables
            double y(y);
            double z(z);
            double t(t);
                                 // 3 record variables, 2400000000 bytes per record
            double var1(t, x, y, z);
            double var2(t, x, y, z);
            double var3(t, x, y, z);
         }
\endcode

\section netcdf_3_io The NetCDF-3 I/O Layer

The following discussion applies only to netCDF classic files (i.e. CDF-1, 2,
and 5 formats). For netCDF-4 files, the I/O layer is the HDF5 library.

For netCDF classic offset files, an I/O layer implemented
much like the C standard I/O (stdio) library is used by netCDF to read
and write portable data to netCDF datasets. Hence an understanding of
the standard I/O library provides answers to many questions about
multiple processes accessing data concurrently, the use of I/O
buffers, and the costs of opening and closing netCDF files. In
particular, it is possible to have one process writing a netCDF
dataset while other processes read it.

Data reads and writes are no more atomic than calls to stdio fread()
and fwrite(). An nc_sync/NF_SYNC call is analogous to the fflush call
in the C standard I/O library, writing unwritten buffered data so
other processes can read it; The C function nc_sync(), or
the Fortran function NF_SYNC(), also brings header changes
up-to-date (for example, changes to attribute values). Opening the
file with the NC_SHARE (in C) or the NF_SHARE (in Fortran) is
analogous to setting a stdio stream to be unbuffered with the _IONBF
flag to setvbuf.

As in the stdio library, flushes are also performed when "seeks" occur
to a different area of the file. Hence the order of read and write
operations can influence I/O performance significantly. Reading data
in the same order in which it was written within each record will
minimize buffer flushes.

You should not expect netCDF classic format data
access to work with multiple writers having the same file open for
writing simultaneously.

It is possible to tune an implementation of netCDF for some platforms
by replacing the I/O layer with a different platform-specific I/O
layer. This may change the similarities between netCDF and standard
I/O, and hence characteristics related to data sharing, buffering, and
the cost of I/O operations.

The distributed netCDF implementation is meant to be
portable. Platform-specific ports that further optimize the
implementation for better I/O performance are practical in some cases.

\section parallel_access Parallel Access with NetCDF-4

Use the special parallel open (or create) calls to open (or create) a
file, and then to use parallel I/O to read or write that file (see
nc_open_par()).

Note that the chunk cache is turned off if a file is opened for
parallel I/O in read/write mode. Open the file in read-only mode to
engage the chunk cache.

NetCDF uses the HDF5 parallel programming model for parallel I/O with
netCDF-4/HDF5 files. The HDF5 tutorial
(http://hdfgroup.org/HDF5//HDF5/Tutor) is a good reference.

For classic files, netCDF uses the PnetCDF library from Argonne National
Labs/Northwestern University. For parallel access of files in classic formats,
netCDF must be configured with the --with-pnetcdf option at build time. See the
PnetCDF site for more information (https://parallel-netcdf.github.io).
Addition information and example programs can be found in
(http://cucis.ece.northwestern.edu/projects/PnetCDF/#InteroperabilityWithNetCDF4)

\section interoperability_with_hdf5 Interoperability with HDF5

To create HDF5 files that can be read by netCDF-4, use the latest in
the HDF5 1.8.x series.

HDF5 has some features that will not be supported by netCDF-4, and
will cause problems for interoperability:
  - HDF5 allows a Group to be both an ancestor and a descendant of
    another Group, creating cycles in the subgroup graph. HDF5 also
    permits multiple parents for a Group. In the netCDF-4 data model,
    Groups form a tree with no cycles, so each Group (except the
    top-level unnamed Group) has a unique parent.
  - HDF5 supports "references" which are like pointers to objects and
    data regions within a file. The netCDF-4 data model omits
    references.
  - HDF5 supports some primitive types that are not included in the
    netCDF-4 data model, including H5T_TIME and H5T_BITFIELD.
  - HDF5 supports multiple names for data objects like Datasets
    (netCDF-4 variables) with no distinguished name. The netCDF-4 data
    model requires that each variable, attribute, dimension, and group
    have a single distinguished name.
  - HDF5 (like netCDF) supports scalar attributes, but netCDF-4 cannot
    read scalar HDF5 attributes (unless it is a string
    attribute). This limitation will be removed in a future release of
    netCDF.

These are fairly easy requirements to meet, but there is one relating
to shared dimensions which is a little more challenging. Every HDF5
dataset must have a dimension scale attached to each dimension.

Dimension scales are a new feature for HF 1.8, which allow
specification of shared dimensions.

Without creation order in the HDF5 file, the files will still be
readable to netCDF-4, it's just that netCDF-4 will number the
variables in alphabetical, rather than creation, order.

Interoperability is a complex task, and all of this is in the alpha
release stage. It is tested in libsrc4/tst_interops.c, which contains
some examples of how to create HDF5 files, modify them in netCDF-4,
and then verify them in HDF5. (And vice versa).


\page netcdf_perf_chunking Improving Performance with Chunking

\tableofcontents

\section chunk_cache The Chunk Cache

When data are first read or written to a netCDF-4/HDF5 variable, the
HDF5 library opens a cache for that variable. The default size of that
cache (settable with the –with-chunk-cache-size at netCDF build time).

For good performance your chunk cache must be larger than one chunk of
your data - preferably that it be large enough to hold multiple chunks
of data.

In addition, when a file is opened (or a variable created in an open
file), the netCDF-4 library checks to make sure the default chunk
cache size will work for that variable. The cache will be large enough
to hold N chunks, up to a maximum size of M bytes. (Both N and M are
settable at configure time with the –with-default-chunks-in-cache and
the –with-max-default-cache-size options to the configure
script. Currently they are set to 10 and 64 MB.)

To change the default chunk cache size, use the set_chunk_cache
function before opening the file with nc_set_chunk_cache(). Fortran 77
programmers see NF_SET_CHUNK_CACHE()). Fortran 90 programmers use the
optional cache_size, cache_nelems, and cache_preemption parameters to
nf90_open/nf90_create to change the chunk size before opening the
file.

To change the per-variable cache size, use the set_var_chunk_cache
function at any time on an open file. C programmers see
nc_set_var_chunk_cache(), Fortran 77 programmers see
NF_SET_VAR_CHUNK_CACHE().

\section default_chunking_4_1 The Default Chunking Scheme

Unfortunately, there are no general-purpose chunking defaults that are
optimal for all uses. Different patterns of access lead to different
chunk shapes and sizes for optimum access. Optimizing for a single
specific pattern of access can degrade performance for other access
patterns.  By creating or rewriting datasets using appropriate
chunking, it is sometimes possible to support efficient access for
multiple patterns of access.

If you don't know or can't anticipate what access patterns will be most common, or you want to store a variable in a way that will support reasonable access along any of its dimensions, you can use the library's default chunking strategy.

The size and shape of chunks for each individual variable are determined at creation time by the size of each variable element and by the shape of the variable, specified by the ordered list of its dimensions and the lengths of each dimension, with special rules for unlimited dimensions.

The best default chunk size would be as large as possible without exceeding the size of a physical disk access. However, block sizes differ for different file systems and platforms, and in particular may be different when the data is first written and later read. Currently the netCDF default chunk size is 4MiB, which is reasonable for filesystems on high-performance computing platforms. A different default may be specified at configuration time when building the library from source, for example 4KiB for filesystems with small physical block sizes.

The current default chunking strategy of the netCDF library is to balance access time along any of a variable's dimensions, by using chunk shapes similar to the shape of the entire variable but small enough that the resulting chunk size is less than or equal to the default chunk size. This differs from an earlier default chunking strategy that always used one for the length of a chunk along any unlimited dimension, and otherwise divided up the number of chunks along fixed dimensions to keep chunk sizes less than or equal to the default chunk size.

A pragmatic exception to the default strategy is used for variables that only have a single unlimited dimension, for example time series with only a time dimension. In that case, in order to avoid chunks much larger than needed when there are only a small number of records, the chunk sizes for such variables are limited to 4KiB. This may be overridden by explicitly setting the chunk shapes for such variables.

\section chunking_parallel_io Chunking and Parallel I/O

When files are opened for read/write parallel I/O access, the chunk
cache is not used. Therefore it is important to open parallel files
with read only access when possible, to achieve the best performance.

\section bm_file A Utility to Help Benchmark Results: bm_file

The bm_file utility may be used to copy files, from one netCDF format
to another, changing chunking, filter, parallel I/O, and other
parameters. This program may be used for benchmarking netCDF
performance for user data files with a range of choices, allowing data
producers to pick settings that best serve their user base.

NetCDF must have been configured with –enable-benchmarks at build time
for the bm_file program to be built. When built with
–enable-benchmarks, netCDF will include tests (run with “make check”)
that will run the bm_file program on sample data files.

Since data files and their access patterns vary from case to case,
these benchmark tests are intended to suggest further use of the
bm_file program for users.

Here's an example of a call to bm_file:

\code
     ./bm_file -d -f 3 -o  tst_elena_out.nc -c 0:-1:0:1024:256:256 tst_elena_int_3D.nc
\endcode

Generally a range of settings must be tested. This is best done with a
shell script, which calls bf_file repeatedly, to create output like
this:

<pre>
     *** Running benchmarking program bm_file for simple shorts test files, 1D to 6D...
     input format, output_format, input size, output size, meta read time, meta write time, data read time, data write time, enddianness, metadata reread time, data reread time, read rate, write rate, reread rate, deflate, shuffle, chunksize[0], chunksize[1], chunksize[2], chunksize[3]
     1, 4, 200092, 207283, 1613, 1054, 409, 312, 0, 1208, 1551, 488.998, 641.026, 128.949, 0, 0, 100000, 0, 0, 0
     1, 4, 199824, 208093, 1545, 1293, 397, 284, 0, 1382, 1563, 503.053, 703.211, 127.775, 0, 0, 316, 316, 0, 0
     1, 4, 194804, 204260, 1562, 1611, 390, 10704, 0, 1627, 2578, 499.159, 18.1868, 75.5128, 0, 0, 46, 46, 46, 0
     1, 4, 167196, 177744, 1531, 1888, 330, 265, 0, 12888, 1301, 506.188, 630.347, 128.395, 0, 0, 17, 17, 17, 17
     1, 4, 200172, 211821, 1509, 2065, 422, 308, 0, 1979, 1550, 473.934, 649.351, 129.032, 0, 0, 10, 10, 10, 10
     1, 4, 93504, 106272, 1496, 2467, 191, 214, 0, 32208, 809, 488.544, 436.037, 115.342, 0, 0, 6, 6, 6, 6
     *** SUCCESS!!!
</pre>

Such tables are suitable for import into spreadsheets, for easy
graphing of results.

Several test scripts are run during the “make check” of the netCDF
build, in the nc_test4 directory. The following example may be found
in nc_test4/run_bm_elena.sh.

<pre>
     #!/bin/sh

     # This shell runs some benchmarks that Elena ran as described here:
     # http://hdfeos.org/workshops/ws06/presentations/Pourmal/HDF5_IO_Perf.pdf

     # $Id: netcdf.texi,v 1.82 2010/05/15 20:43:13 dmh Exp $

     set -e
     echo ""

     echo "*** Testing the benchmarking program bm_file for simple float file, no compression..."
     ./bm_file -h -d -f 3 -o  tst_elena_out.nc -c 0:-1:0:1024:16:256 tst_elena_int_3D.nc
     ./bm_file -d -f 3 -o  tst_elena_out.nc -c 0:-1:0:1024:256:256 tst_elena_int_3D.nc
     ./bm_file -d -f 3 -o  tst_elena_out.nc -c 0:-1:0:512:64:256 tst_elena_int_3D.nc
     ./bm_file -d -f 3 -o  tst_elena_out.nc -c 0:-1:0:512:256:256 tst_elena_int_3D.nc
     ./bm_file -d -f 3 -o  tst_elena_out.nc -c 0:-1:0:256:64:256 tst_elena_int_3D.nc
     ./bm_file -d -f 3 -o  tst_elena_out.nc -c 0:-1:0:256:256:256 tst_elena_int_3D.nc
     echo '*** SUCCESS!!!'

     exit 0
</pre>

The reading that bm_file does can be tailored to match the expected
access pattern.

The bm_file program is controlled with command line options.

<pre>
     ./bm_file
     bm_file -v [-s N]|[-t V:S:S:S -u V:C:C:C -r V:I:I:I] -o file_out -f N -h -c V:C:C,V:C:C:C -d -m -p -i -e 1|2 file
       [-v]        Verbose
       [-o file]   Output file name
       [-f N]      Output format (1 - classic, 2 - 64-bit offset, 3 - netCDF-4, 4 - netCDF4/CLASSIC)
       [-h]        Print output header
       [-c V:Z:S:C:C:C[,V:Z:S:C:C:C, etc.]] Deflate, shuffle, and chunking parameters for vars
       [-t V:S:S:S[,V:S:S:S, etc.]] Starts for reads/writes
       [-u V:C:C:C[,V:C:C:C, etc.]] Counts for reads/writes
       [-r V:I:I:I[,V:I:I:I, etc.]] Incs for reads/writes
       [-d]        Doublecheck output by rereading each value
       [-m]        Do compare of each data value during doublecheck (slow for large files!)
       [-p]        Use parallel I/O
       [-s N]      Denom of fraction of slowest varying dimension read.
       [-i]        Use MPIIO (only relevant for parallel builds).
       [-e 1|2]    Set the endianness of output (1=little 2=big).
       file        Name of netCDF file
</pre>

\page netcdf_utilities_guide NetCDF Utilities

\tableofcontents

\section cdl_guide CDL Guide

\subsection cdl_syntax CDL Syntax

Below is an example of CDL, describing a netCDF classic format file with several
named dimensions (lat, lon, time), variables (z, t, p, rh, lat, lon,
time), variable attributes (units, _FillValue, valid_range), and some
data.

\code
     netcdf foo {    // example netCDF specification in CDL

     dimensions:
     lat = 10, lon = 5, time = unlimited;

     variables:
       int     lat(lat), lon(lon), time(time);
       float   z(time,lat,lon), t(time,lat,lon);
       double  p(time,lat,lon);
       int     rh(time,lat,lon);

       lat:units = "degrees_north";
       lon:units = "degrees_east";
       time:units = "seconds";
       z:units = "meters";
       z:valid_range = 0., 5000.;
       p:_FillValue = -9999.;
       rh:_FillValue = -1;

     data:
       lat   = 0, 10, 20, 30, 40, 50, 60, 70, 80, 90;
       lon   = -140, -118, -96, -84, -52;
     }
\endcode

All CDL statements are terminated by a semicolon. Spaces, tabs, and
newlines can be used freely for readability. Comments may follow the
double slash characters '//' on any line.

A CDL description for a classic model file consists of three optional
parts: dimensions, variables, and data. The variable part may contain
variable declarations and attribute assignments. For the enhanced
model supported by netCDF-4, a CDL description may also include
groups, subgroups, and user-defined types.

A dimension is used to define the shape of one or more of the
multidimensional variables described by the CDL description. A
dimension has a name and a length. At most one dimension in a classic
CDL description can have the unlimited length, which means a variable
using this dimension can grow to any length (like a record number in a
file). Any number of dimensions can be declared of unlimited length in
CDL for an enhanced model file.

A variable represents a multidimensional array of values of the same
type. A variable has a name, a data type, and a shape described by its
list of dimensions. Each variable may also have associated attributes
(see below) as well as data values. The name, data type, and shape of
a variable are specified by its declaration in the variable section of
a CDL description. A variable may have the same name as a dimension;
by convention such a variable contains coordinates of the dimension it
names.

An attribute contains information about a variable or about the whole
netCDF dataset or containing group. Attributes may be used to specify
such properties as units, special values, maximum and minimum valid
values, and packing parameters. Attribute information is represented
by single values or one-dimensional arrays of values. For example,
“units” might be an attribute represented by a string such as
“celsius”. An attribute has an associated variable, a name, a data
type, a length, and a value. In contrast to variables that are
intended for data, attributes are intended for ancillary data or
metadata (data about data).

In CDL, an attribute is designated by a variable and attribute name,
separated by a colon (':'). It is possible to assign global attributes
to the netCDF dataset as a whole by omitting the variable name and
beginning the attribute name with a colon (':'). The data type of an
attribute in CDL, if not explicitly specified, is derived from the
type of the value assigned to it. In the netCDF-4
enhanced model, attributes may be declared to be of user-defined type,
like variables.

The length of an attribute is the number of data values assigned to
it.  Multiple values are assigned to non-character attributes by
separating the values with commas (',').  All values assigned to an
attribute must be of the same type.  In the classic data model,
character arrays are used for textual information.  The length of a
character attribute is the number of bytes, and an array of
character values can be represented in string notation.  In the
enhanced data model of netCDF-4, variable-length strings are available
as a primitive type, and the length of a string attribute is the
number of string values assigned to it.

In CDL, just as for netCDF, the names of dimensions, variables and
attributes (and, in netCDF-4 files, groups, user-defined types,
compound member names, and enumeration symbols) consist of arbitrary
sequences of alphanumeric characters, underscore '_', period '.', plus
'+', hyphen '-', or at sign '@', but beginning with a letter or
underscore. However names commencing with underscore are reserved for
system use. Case is significant in netCDF names. A zero-length name is
not allowed. Some widely used conventions restrict names to only
alphanumeric characters or underscores. Names that have trailing space
characters are also not permitted.

Beginning with versions 3.6.3 and 4.0, names may also include UTF-8
encoded Unicode characters as well as other special characters, except
for the character '/', which may not appear in a name (because it is
reserved for path names of nested groups). In CDL, most special
characters are escaped with a backslash '\' character, but that
character is not actually part of the netCDF name. The special
characters that do not need to be escaped in CDL names are underscore
'_', period '.', plus '+', hyphen '-', or at sign '@'.  The formal
specification of CDL name syntax is provided in the classic format
specification (see \ref classic_format).  Note that by using
special characters in names, you may make your data not compliant with
conventions that have more stringent requirements on valid names for
netCDF components, for example the CF Conventions.

The names for the primitive data types are reserved words in CDL, so
names of variables, dimensions, and attributes must not be primitive
type names.

The optional data section of a CDL description is where netCDF
variables may be initialized. The syntax of an initialization is
simple:

\code
     variable = value_1, value_2, ... ;
\endcode

The comma-delimited list of constants may be separated by spaces,
tabs, and newlines. For multidimensional arrays, the last dimension
varies fastest. Thus, row-order rather than column order is used for
matrices. If fewer values are supplied than are needed to fill a
variable, it is extended with the fill value. The types of constants
need not match the type declared for a variable; coercions are done to
convert integers to floating point, for example. All meaningful type
conversions among numeric primitive types are supported.

A special notation for fill values is supported: the ‘_’ character
designates a fill value for variables.

\subsection cdl_data_types CDL Data Types

The CDL primitive data types for the classic model are:
- char - Characters.
- byte - Eight-bit integers.
- short - 16-bit signed integers.
- int - 32-bit signed integers.
- long - (Deprecated, synonymous with int)
- float - IEEE single-precision floating point (32 bits).
- real - (Synonymous with float).
- double - IEEE double-precision floating point (64 bits).

NetCDF-4 supports the additional primitive types:
- ubyte - Unsigned eight-bit integers.
- ushort - Unsigned 16-bit integers.
- uint - Unsigned 32-bit integers.
- int64 - 64-bit signed integers.
- uint64 - Unsigned 64-bit signed integers.
- string - Variable-length string of characters

Except for the added numeric data-types byte and ubyte, CDL supports
the same numeric primitive
data types as C. For backward compatibility, in declarations primitive
type names may be specified in either upper or lower case.

The byte type differs from the char type in that it is intended for
numeric data, and the zero byte has no special significance, as it may
for character data. In the classic data model, byte data could be
interpreted as either signed (-128 to 127) or unsigned (0 to
255). When reading byte data in a way that converts it into another
numeric type, the default interpretation is signed.  The netCDF-4
enhanced data model added an unsigned byte type.

The short type holds values between -32768 and
32767. The ushort type holds values between 0 and 65536. The int type
can hold values between -2147483648 and 2147483647. The uint type
holds values between 0 and 4294967296. The int64 type can hold values
between -9223372036854775808 and 9223372036854775807. The uint64 type
can hold values between 0 and 18446744073709551616.

The float type can hold values between about -3.4+38 and 3.4+38, with
external representation as 32-bit IEEE normalized single-precision
floating-point numbers. The double type can hold values between about
-1.7+308 and 1.7+308, with external representation as 64-bit IEEE
standard normalized double-precision, floating-point numbers. The
string type holds variable length strings.

A netCDF-4 string is a variable length array of Unicode
<http://unicode.org/> characters. When reading/writing a String to a
netCDF file or other external representation, the characters are UTF-8
encoded <http://en.wikipedia.org/wiki/UTF-8> (note that ASCII is a
subset of UTF-8). Libraries may use different internal
representations, for example the Java library uses UTF-16 encoding.

The netCDF char type contains uninterpreted characters, one character
per byte.  Typically these contain 7-bit ASCII characters, but the
character encoding is application specific. For this reason,
applications writing data using the enhanced data model are encouraged
to use the netCDF-4 string data type in preference to the char data
type.  Applications writing string data using the char data type are
encouraged to add the special variable attribute "_Encoding" with a
value that the netCDF libraries recognize. Currently those valid
values are "UTF-8" or "ASCII", case insensitive.

\subsection cdl_constants CDL Notation for Data Constants

This section describes the CDL notation for constants.

Attributes are initialized in the variables section of a CDL
description by providing a list of constants that determines the
attribute's length and type (if primitive and not explicitly
declared). CDL defines a syntax for constant values that permits
distinguishing among different netCDF primitive types. The syntax for
CDL constants is similar to C syntax, with type suffixes appended to
bytes, shorts, and floats to distinguish them from ints and doubles.

A byte constant is represented by an integer constant with a 'b' (or
'B') appended.  In the old netCDF-2 API, byte constants could also be
represented using single characters or standard C character escape
sequences such as 'a' or '\n'.  This is still supported for backward
compatibility, but deprecated to make the distinction clear between
the numeric byte type and the textual char type.  Example byte
constants include:

\code
     0b      // a zero byte
     -1b     // -1 as an 8-bit byte
     255b    // also -1 as a signed 8-bit byte
\endcode

Character constants are enclosed in double quotes. A character array
may be represented as a string enclosed in double quotes. Multiple CDL
strings are concatenated into a single array of characters, permitting
long character arrays to appear on multiple lines. To support multiple
variable-length textual values, a conventional delimiter such as ','
or blank may be used, but interpretation of any such convention for a
delimiter must be implemented in software above the netCDF library
layer. The usual escape conventions for C strings are honored. For
example:

\code
     "a"            // ASCII 'a'
     "Two\nlines\n" // a 10-character string with two embedded newlines
     "a bell:\007"  // a character array containing an ASCII bell
     "ab","cde"     // the same as "abcde"
\endcode

The form of a short constant is an integer constant with an 's' or 'S'
appended. If a short constant begins with '0', it is interpreted as
octal. When it begins with '0x', it is interpreted as a hexadecimal
constant. For example:

\code
     2s      // a short 2
     0123s   // octal
     0x7ffs  // hexadecimal
\endcode

The form of an int constant is an ordinary integer constant. If an int
constant begins with '0', it is interpreted as octal. When it begins
with '0x', it is interpreted as a hexadecimal constant. Examples of
valid int constants include:

\code
     -2
     0123            // octal
     0x7ff           // hexadecimal
     1234567890L     // deprecated, uses old long suffix
\endcode

The float type is appropriate for representing data with about seven
significant digits of precision. The form of a float constant is the
same as a C floating-point constant with an 'f' or 'F' appended. A
decimal point is required in a CDL float to distinguish it from an
integer. For example, the following are all acceptable float
constants:

\code
     -2.0f
     3.14159265358979f       // will be truncated to less precision
     1.f
     .1f
\endcode

The double type is appropriate for representing floating-point data
with about 16 significant digits of precision. The form of a double
constant is the same as a C floating-point constant. An optional 'd'
or 'D' may be appended. A decimal point is required in a CDL double to
distinguish it from an integer. For example, the following are all
acceptable double constants:

\code
     -2.0
     3.141592653589793
     1.0e-20
     1.d
\endcode

Unsigned integer constants can be created by appending the character
'U' or 'u' between the constant and any trailing size specifier.  Thus
one could say 10U, 100us, 100000ul, or 1000000ull, for example.

Constants for the variable-length string type, available as a
primitive type in the netCDF-4 enhanced data model are, like character
constants, represented using double quotes. This represents a
potential ambiguity since a multi-character string may also indicate a
dimensioned character value. Disambiguation usually occurs by context,
but care should be taken to specify the string type to ensure the
proper choice.  For example, these two CDL specifications of global
attributes have different types:

\code
   :att1 = "abcd", "efg" ;       // a char attribute of length 7
   string :att2 = "abcd", efg" ; // a string attribute of length 2
\endcode

Opaque constants are represented as sequences of hexadecimal digits
preceded by 0X or 0x: 0xaa34ffff, for example.  These constants can
still be used as integer constants and will be either truncated or
extended as necessary.

The ncgen man-page reference has more details about CDL representation
of constants of user-defined types.

\section ncdump_guide ncdump

Convert NetCDF file to text form (CDL)

\subsection  ncdump_SYNOPSIS ncdump synopsis

\code
ncdump   [-chistxw]  [-v  var1,...]  [-b lang]  [-f lang]
         [-l  len]  [-n  name]  [-p n[,n]]  [-g  grp1,...]  file


ncdump    -k file
\endcode

\subsection ncdump_DESCRIPTION ncdump description

The \b ncdump utility generates a text representation of a specified
netCDF file on standard output, optionally excluding some or all of
the variable data in the output.  The text representation is in a form
called CDL (network Common Data form Language) that can be viewed,
edited, or serve as input to \b ncgen, a companion program that can
generate a binary netCDF file from a CDL file.  Hence \b ncgen and \b
ncdump can be used as inverses to transform the data representation
between binary and text representations.  See \b ncgen documentation
for a description of CDL and netCDF representations.

\b ncdump may also be used to determine what kind of netCDF file
is used (which variant of the netCDF file format) with the -k
option.

If DAP support was enabled when \b ncdump was built, the file name may
specify a DAP URL. This allows \b ncdump to access data sources from
DAP servers, including data in other formats than netCDF.  When used
with DAP URLs, \b ncdump shows the translation from the DAP data
model to the netCDF data model.

\b ncdump may also be used as a simple browser for netCDF data files,
to display the dimension names and lengths; variable names, types, and
shapes; attribute names and values; and optionally, the values of data
for all variables or selected variables in a netCDF file.  For
netCDF-4 files, groups and user-defined types are also included in \b
ncdump output.

\b ncdump uses '_' to represent data values that are equal to the
'_FillValue' attribute for a variable, intended to represent
data that has not yet been written.  If a variable has no
'_FillValue' attribute, the default fill value for the variable
type is used unless the variable is of byte type.

\b ncdump defines a default display format used for each type of
netCDF data, but this can be changed if a 'C_format' attribute
is defined for a netCDF variable.  In this case, \b ncdump will
use the 'C_format' attribute to format each value.  For
example, if floating-point data for the netCDF variable 'Z' is
known to be accurate to only three significant digits, it would
be appropriate to use the variable attribute

\code
    Z:C_format = "%.3g"
\endcode

\subsection ncdump_OPTIONS ncdump options

@par -c
Show the values of \e coordinate \e variables (1D variables with the
same names as dimensions) as well as the declarations of all
dimensions, variables, attribute values, groups, and user-defined
types.  Data values of non-coordinate variables are not included in
the output.  This is usually the most suitable option to use for a
brief look at the structure and contents of a netCDF file.

@par -h
Show only the header information in the output, that is, output only
the declarations for the netCDF dimensions, variables, attributes,
groups, and user-defined types of the input file, but no data values
for any variables. The output is identical to using the '-c' option
except that the values of coordinate variables are not included. (At
most one of '-c' or '-h' options may be present.)

@par -v \a var1,...

@par
The output will include data values for the specified variables, in
addition to the declarations of all dimensions, variables, and
attributes. One or more variables must be specified by name in the
comma-delimited list following this option. The list must be a single
argument to the command, hence cannot contain unescaped blanks or
other white space characters. The named variables must be valid netCDF
variables in the input-file. A variable within a group in a netCDF-4
file may be specified with an absolute path name, such as
'/GroupA/GroupA2/var'.  Use of a relative path name such as 'var' or
'grp/var' specifies all matching variable names in the file.  The
default, without this option and in the absence of the '-c' or '-h'
options, is to include data values for \e all variables in the output.

@par -b [c|f]
A brief annotation in the form of a CDL comment (text beginning with
the characters '//') will be included in the data section of the
output for each 'row' of data, to help identify data values for
multidimensional variables. If lang begins with 'C' or 'c', then C
language conventions will be used (zero-based indices, last dimension
varying fastest). If lang begins with 'F' or 'f', then FORTRAN
language conventions will be used (one-based indices, first dimension
varying fastest). In either case, the data will be presented in the
same order; only the annotations will differ. This option may be
useful for browsing through large volumes of multidimensional data.

@par -f [c|f]
Full annotations in the form of trailing CDL comments (text beginning
with the characters '//') for every data value (except individual
characters in character arrays) will be included in the data
section. If lang begins with 'C' or 'c', then C language conventions
will be used. If lang begins with 'F' or 'f', then FORTRAN language
conventions will be used. In either case, the data will be presented
in the same order; only the annotations will differ. This option may
be useful for piping data into other filters, since each data value
appears on a separate line, fully identified. (At most one of '-b' or
'-f' options may be present.)

@par -l \e length

@par
Changes the default maximum line length (80) used in formatting lists
of non-character data values.

@par -n \e name

@par
CDL requires a name for a netCDF file, for use by 'ncgen -b' in
generating a default netCDF file name. By default, \b ncdump
constructs this name from the last component of the file name of
the input netCDF file by stripping off any extension it has. Use
the '-n' option to specify a different name. Although the output
file name used by 'ncgen -b' can be specified, it may be wise to
have \b ncdump change the default name to avoid inadvertently
overwriting a valuable netCDF file when using \b ncdump, editing the
resulting CDL file, and using 'ncgen -b' to generate a new netCDF
file from the edited CDL file.

@par -p \e float_digits[, \e double_digits ]

@par
Specifies default precision (number of significant digits) to use in
displaying floating-point or double precision data values for
attributes and variables. If specified, this value overrides the value
of the C_format attribute, if any, for a variable. Floating-point data
will be displayed with \e float_digits significant digits. If \e
double_digits is also specified, double-precision values will be
displayed with that many significant digits. In the absence of any
'-p' specifications, floating-point and double-precision data are
displayed with 7 and 15 significant digits respectively. CDL files can
be made smaller if less precision is required. If both floating-point
and double precisions are specified, the two values must appear
separated by a comma (no blanks) as a single argument to the command.
(To represent every last bit of precision in a CDL file for all
possible floating-point values would requires '-p 9,17'.)

@par -k
Show \e kind of netCDF file, that is which format variant the file uses.
Other options are ignored if this option is specified.  Output will be
one of 'classic'. '64-bit offset', '64-bit data', 'netCDF-4', or 'netCDF-4 classic
model'.

@par -s
Specifies that \e special virtual and hidden attributes should be output
for the file format variant and for variable properties such as
compression, chunking, and other properties specific to the format
implementation that are primarily related to performance rather
than the logical schema of the data. All the special virtual
attributes begin with '_' followed by an upper-case
letter. Currently they include the global attributes '_Format',
'_NCProperties', '_IsNetcdf4', '_SuperblockVersion' and
the variable attributes '_ChunkSizes', '_DeflateLevel',
'_Endianness', '_Fletcher32', '_NoFill', '_Shuffle', and '_Storage'.
The \b ncgen utility recognizes these attributes and
supports them appropriately. For '_NCProperties',
'_IsNetcdf4', and '_SuperblockVersion', the term 'appropriately'
means that they are ignored.

@par -t
Controls display of time data, if stored in a variable that uses a
udunits compliant time representation such as 'days since 1970-01-01'
or 'seconds since 2009-03-15 12:01:17'.  If this option is specified,
time values are displayed as a human-readable date-time strings rather
than numerical values, interpreted in terms of a 'calendar' variable
attribute, if specified.  For numeric attributes of time variables,
the human-readable time value is displayed after the actual value, in
an associated CDL comment.  Calendar attribute values interpreted with
this option include the CF Conventions values 'gregorian' or
'standard', 'proleptic_gregorian', 'noleap' or '365_day', 'all_leap'
or '366_day', '360_day', and 'julian'.

@par -i
Same as the '-t' option, except output time data as date-time strings
with ISO-8601 standard 'T' separator, instead of a blank.

@par -g \e grp1,...

@par
The output will include data values only for the specified groups.
One or more groups must be specified by name in the comma-delimited
list following this option. The list must be a single argument to the
command. The named groups must be valid netCDF groups in the
input-file. The default, without this option and in the absence of the
'-c' or '-h' options, is to include data values for all groups in the
output.

@par -w
For file names that request remote access using DAP URLs, access data
with client-side caching of entire variables.

@par -x
Output XML (NcML) instead of CDL.  The NcML does not include data values.
The NcML output option currently only works for netCDF classic model data.

\subsection  ncdump_EXAMPLES ncdump examples

Look at the structure of the data in the netCDF file foo.nc:

\code
   ncdump -c foo.nc
\endcode

Produce an annotated CDL version of the structure and data in the
netCDF file foo.nc, using C-style indexing for the annotations:

\code
   ncdump -b c foo.nc > foo.cdl
\endcode

Output data for only the variables uwind and vwind from the netCDF
file foo.nc, and show the floating-point data with only three
significant digits of precision:

\code
   ncdump -v uwind,vwind -p 3 foo.nc
\endcode

Produce a fully-annotated (one data value per line) listing of the
data for the variable omega, using FORTRAN conventions for indices,
and changing the netCDF file name in the resulting CDL file to
omega:

\code
   ncdump -v omega -f fortran -n omega foo.nc > Z.cdl
\endcode

Examine the translated DDS for the DAP source from the specified URL:

\code
   ncdump -h http://test.opendap.org:8080/dods/dts/test.01
\endcode

Without dumping all the data, show the special virtual attributes that indicate
performance-related characterisitics of a netCDF-4 file:

\code
   ncdump -h -s nc4file.nc
\endcode

\subsection see_also_ncdump SEE ALSO

ncgen(1), netcdf(3)

- \ref guide_ncgen
- \ref guide_nccopy

\subsection ncdump_string_note NOTE ON STRING OUTPUT

For classic, 64-bit offset, 64-bit data, or netCDF-4 classic model data, \b ncdump
generates line breaks after embedded newlines in displaying character
data.  This is not done for netCDF-4 files, because netCDF-4 supports
arrays of real strings of varying length.

\section guide_nccopy nccopy

Copy a netCDF file, optionally changing format, compression, or chunking in the output.


\subsection  nccopy_SYNOPSIS nccopy synopsis

\code
nccopy [-k kind_name] [-kind_code] [-d n] [-s] [-c chunkspec] [-u] [-w]
       [-[v|V] var1,...] [-[g|G] grp1,...] [-m bufsize] [-h chunk_cache]
       [-e cache_elems] [-r]   infile   outfile
\endcode

\subsection  nccopy_DESCRIPTION nccopy description

The \b nccopy utility copies an input netCDF file in any supported
format variant to an output netCDF file, optionally converting the
output to any compatible netCDF format variant, compressing the data,
or rechunking the data.  For example, if built with the netCDF-3
library, a classic CDF-1 file may be copied to a CDF-2 or CDF-5
file, permitting larger variables.  If built with the netCDF-4
library, a netCDF classic file may be copied to a netCDF-4 file or to
a netCDF-4 classic model file as well, permitting data compression,
efficient schema changes, larger variable sizes, and use of other
netCDF-4 features.

If no output format is specified, with either \b -k  \e kind_name
or \e -kind_code, then the output will use the same
format as the input, unless the input is classic format
and either chunking or compression is specified, in which case the
output will be netCDF-4 classic model format.  Attempting
some kinds of format conversion will result in an error, if the
conversion is not possible.  For example, an attempt to copy a
netCDF-4 file that uses features of the enhanced model, such as
groups or variable-length strings, to any of the other kinds of netCDF
formats that use the classic model will result in an error.

\b nccopy also serves as an example of a generic netCDF-4 program,
with its ability to read any valid netCDF file and handle nested
groups, strings, and user-defined types, including arbitrarily
nested compound types, variable-length types, and data of any valid
netCDF-4 type.

If DAP support was enabled when \b nccopy was built, the file name may
specify a DAP URL. This may be used to convert data on DAP servers to
local netCDF files.

\subsection nccopy_OPTIONS nccopy options

\par -k \e kind_name
Use format name to specify the kind of file to be created
and, by inference, the data model (i.e. netcdf-3 (classic) or
netcdf-4 (enhanced)).  The possible arguments are: \n
    'nc3' or 'classic' => netCDF classic format \n
    'nc6' or '64-bit offset' => netCDF 64-bit offset format \n
    'cdf5' => netCDF 64-bit data format \n
    'nc4' or 'netCDF-4' => netCDF-4 format (enhanced data model) \n
    'nc7' or 'netCDF-4 classic model' => netCDF-4 classic model format \n

\par
Note: The old format numbers '1', '2', '3', '4', equivalent
to the format names 'nc3', 'nc6', 'nc4', or 'nc7' respectively, are
also still accepted but deprecated, due to easy confusion between
format numbers and format names.

\par -k \e kind_code
Use format numeric code (instead of format name) to specify the kind of file to be created
and, by inference, the data model (i.e. netcdf-3 (classic) versus
netcdf-4 (enhanced)).  The numeric codes are: \n
    3 => netcdf classic format \n
    6 => netCDF 64-bit offset format (CDF_2) \n
    5 => netCDF 64-bit data format (CDF-5) \n
    4 => netCDF-4 format (enhanced data model) \n
    7 => netCDF-4 classic model format \n

The numeric code "7" is used because "7=3+4", specifying the format
that uses the netCDF-3 data model for compatibility with the netCDF-4
storage format for performance. Credit is due to NCO for use of these
numeric codes instead of the old and confusing format numbers.

\par -d \e n
For netCDF-4 output, including netCDF-4 classic model, specify
deflation level (level of compression) for variable data output.  0
corresponds to no compression and 9 to maximum compression, with
higher levels of compression requiring marginally more time to
compress or uncompress than lower levels.  Compression achieved may
also depend on output chunking parameters.  If this option is
specified for a classic format input file, it
is not necessary to also specify that the output should be netCDF-4
classic model, as that will be the default.  If this option is not
specified and the input file has compressed variables, the compression
will still be preserved in the output, using the same chunking as in
the input by default.

\par
Note that \b nccopy requires all variables to be compressed using the
same compression level, but the API has no such restriction.  With
a program you can customize compression for each variable independently.

\par -s
For netCDF-4 output, including netCDF-4 classic model, specify
shuffling of variable data bytes before compression or after
decompression.  Shuffling refers to interlacing of bytes in a chunk so
that the first bytes of all values are contiguous in storage, followed
by all the second bytes, and so on, which often improves compression.
This option is ignored unless a non-zero deflation level is specified.
Using -d0 to specify no deflation on input data that has been
compressed and shuffled turns off both compression and shuffling in
the output.

\par -u
Convert any unlimited size dimensions in the input to fixed size
dimensions in the output.  This can speed up variable-at-a-time
access, but slow down record-at-a-time access to multiple variables
along an unlimited dimension.

\par -w
Keep output in memory (as a diskless netCDF file) until output is
closed, at which time output file is written to disk.  This can
greatly speedup operations such as converting unlimited dimension to
fixed size (-u option), chunking, rechunking, or compressing the
input.  It requires that available memory is large enough to hold the
output file.  This option may provide a larger speedup than careful
tuning of the -m, -h, or -e options, and it's certainly a lot simpler.

\par -c  \e  chunkspec
\par
For netCDF-4 output, including netCDF-4 classic model, specify
chunking (multidimensional tiling) for variable data in the output.
This is useful to specify the units of disk access, compression, or
other filters such as checksums.  Changing the chunking in a netCDF
file can also greatly speedup access, by choosing chunk shapes that
are appropriate for the most common access patterns.

\par
The \e chunkspec argument is a string of comma-separated associations,
each specifying a dimension name, a '/' character, and optionally the
corresponding chunk length for that dimension.  No blanks should
appear in the chunkspec string, except possibly escaped blanks that
are part of a dimension name.  A chunkspec names at least one
dimension, and may omit dimensions which are not to be chunked or for
which the default chunk length is desired.  If a dimension name is
followed by a '/' character but no subsequent chunk length, the actual
dimension length is assumed.  If copying a classic model file to a
netCDF-4 output file and not naming all dimensions in the chunkspec,
unnamed dimensions will also use the actual dimension length for the
chunk length.  An example of a chunkspec for variables that use 'm'
and 'n' dimensions might be 'm/100,n/200' to specify 100 by 200
chunks. To see the chunking resulting from copying with a chunkspec,
use the '-s' option of ncdump on the output file.

\par
The chunkspec '/' that omits all dimension names and
corresponding chunk lengths specifies that no chunking is to occur in
the output, so can be used to unchunk all the chunked variables.
To see the chunking resulting from copying with a chunkspec,
use the '-s' option of ncdump on the output file.

\par
As an I/O optimization, \b nccopy has a threshold for the minimum size of
non-record variables that get chunked, currently 8192 bytes.  In the future,
use of this threshold and its size may be settable in an option.

\par
Note that \b nccopy requires variables that share a dimension to also
share the chunk size associated with that dimension, but the
programming interface has no such restriction.  If you need to
customize chunking for variables independently, you will need to use
the library API in a custom utility program.

\par -v \a var1,...

\par
The output will include data values for the specified variables, in
addition to the declarations of all dimensions, variables, and
attributes. One or more variables must be specified by name in the
comma-delimited list following this option. The list must be a single
argument to the command, hence cannot contain unescaped blanks or
other white space characters. The named variables must be valid netCDF
variables in the input-file. A variable within a group in a netCDF-4
file may be specified with an absolute path name, such as
'/GroupA/GroupA2/var'.  Use of a relative path name such as 'var' or
'grp/var' specifies all matching variable names in the file.  The
default, without this option, is to include data values for \e all variables
in the output.

\par -V \a var1,...

\par
The output will include the specified variables only but all dimensions and
global or group attributes. One or more variables must be specified by name in the
comma-delimited list following this option. The list must be a single argument
to the command, hence cannot contain unescaped blanks or other white space
characters. The named variables must be valid netCDF variables in the
input-file. A variable within a group in a netCDF-4 file may be specified with
an absolute path name, such as '/GroupA/GroupA2/var'.  Use of a relative path
name such as 'var' or 'grp/var' specifies all matching variable names in the
file.  The default, without this option, is to include \e all variables in the
output.

\par -g \e grp1,...

\par
The output will include data values only for the specified groups.
One or more groups must be specified by name in the comma-delimited
list following this option. The list must be a single argument to the
command. The named groups must be valid netCDF groups in the
input-file. The default, without this option, is to include data values for all
groups in the output.

\par -G \e grp1,...

\par
The output will include only the specified groups.
One or more groups must be specified by name in the comma-delimited
list following this option. The list must be a single argument to the
command. The named groups must be valid netCDF groups in the
input-file. The default, without this option, is to include all groups in the
output.

\par -m  \e  bufsize
\par
An integer or floating-point number that specifies the size, in bytes,
of the copy buffer used to copy large variables.  A suffix of K, M, G,
or T multiplies the copy buffer size by one thousand, million,
billion, or trillion, respectively.  The default is 5 Mbytes,
but will be increased if necessary to hold at least one chunk of
netCDF-4 chunked variables in the input file.  You may want to specify
a value larger than the default for copying large files over high
latency networks.  Using the '-w' option may provide better
performance, if the output fits in memory.

\par -h \e chunk_cache
\par
For netCDF-4 output, including netCDF-4 classic model, an integer or
floating-point number that specifies the size in bytes of chunk cache
allocated for each chunked variable.  This is not a property of the file, but merely
a performance tuning parameter for avoiding compressing or
decompressing the same data multiple times while copying and changing
chunk shapes.  A suffix of K, M, G, or T multiplies the chunk cache
size by one thousand, million, billion, or trillion, respectively.
The default is 4.194304 Mbytes (or whatever was specified for the
configure-time constant CHUNK_CACHE_SIZE when the netCDF library was
built).  Ideally, the \b nccopy utility should accept only one memory
buffer size and divide it optimally between a copy buffer and chunk
cache, but no general algorithm for computing the optimum chunk cache
size has been implemented yet. Using the '-w' option may provide
better performance, if the output fits in memory.

\par -e \e cache_elems
\par
For netCDF-4 output, including netCDF-4 classic model, specifies
number of chunks that the chunk cache can hold. A suffix of K, M, G,
or T multiplies the number of chunks that can be held in the cache
by one thousand, million, billion, or trillion, respectively.  This is not a
property of the file, but merely a performance tuning parameter for
avoiding compressing or decompressing the same data multiple times
while copying and changing chunk shapes.  The default is 1009 (or
whatever was specified for the configure-time constant
CHUNK_CACHE_NELEMS when the netCDF library was built).  Ideally, the
\b nccopy utility should determine an optimum value for this parameter,
but no general algorithm for computing the optimum number of chunk
cache elements has been implemented yet.

\par -r
Read netCDF classic input file into a diskless netCDF
file in memory before copying.  Requires that input file be small
enough to fit into memory.  For \b nccopy, this doesn't seem to provide
any significant speedup, so may not be a useful option.

\subsection nccopy_EXAMPLES nccopy examples

<H4> Simple Copy </H4>
Make  a copy  of  foo1.nc, a  netCDF  file of  any type,  to
foo2.nc, a netCDF file of the same type:
\code
nccopy foo1.nc foo2.nc
\endcode
Note that the above copy will not be as fast as use of cp or other
simple copy utility, because the file is copied using only the netCDF
API.  If the input file has extra bytes after the end of the netCDF
data, those will not be copied, because they are not accessible
through the netCDF interface.  If the original file was generated in
'No fill' mode so that fill values are not stored for padding for data
alignment, the output file may have different padding bytes.

<H4> Uncompress Data </H4>
Convert a netCDF-4 classic model file, compressed.nc, that uses compression,
to a netCDF-3 file classic.nc:
\code
nccopy -k classic compressed.nc classic.nc
\endcode
Note that 'nc3' could be used instead of 'classic'.

<H4> Remote Access to Data Subset </H4>

Download the variable 'time_bnds' and its associated attributes from
an OPeNDAP server and copy the result to a netCDF file named 'tb.nc':
\code
nccopy 'http://test.opendap.org/opendap/data/nc/sst.mnmean.nc.gz?time_bnds' tb.nc
\endcode
Note that URLs that name specific variables as command-line arguments
should generally be quoted, to avoid the shell interpreting special
characters such as '?'.

<H4> Compress Data </H4>

Compress all the variables in the input file foo.nc, a netCDF file of any
type, to the output file bar.nc:
\code
nccopy -d1 foo.nc bar.nc
\endcode
If foo.nc was a classic netCDF file, bar.nc will be a
netCDF-4 classic model netCDF file, because the classic
formats don't support compression.  If foo.nc was a
netCDF-4 file with some variables compressed using various deflation
levels, the output will also be a netCDF-4 file of the same type, but
all the variables, including any uncompressed variables in the input,
will now use deflation level 1.

<H4>Rechunk Data for Faster Access</H4>

Assume the input data includes gridded variables that use time, lat,
lon dimensions, with 1000 times by 1000 latitudes by 1000 longitudes,
and that the time dimension varies most slowly.  Also assume that
users want quick access to data at all times for a small set of
lat-lon points.  Accessing data for 1000 times would typically require
accessing 1000 disk blocks, which may be slow.

Reorganizing the data into chunks on disk that have all the time in
each chunk for a few lat and lon coordinates would greatly speed up
such access.  To chunk the data in the input file slow.nc, a netCDF
file of any type, to the output file fast.nc, you could use;
\code
nccopy -c time/1000,lat/40,lon/40 slow.nc fast.nc
\endcode
to specify data chunks of 1000 times, 40 latitudes, and 40 longitudes.
If you had enough memory to contain the output file, you could speed
up the rechunking operation significantly by creating the output in
memory before writing it to disk on close:
\code
nccopy -w -c time/1000,lat/40,lon/40 slow.nc fast.nc
\endcode

\subsection see_also_nccopy SEE ALSO

ncdump(1), ncgen(1), netcdf(3)

\section guide_ncgen ncgen

The ncgen tool generates a netCDF file or a C or FORTRAN program that
creates a netCDF dataset. If no options are specified in invoking
ncgen, the program merely checks the syntax of the CDL input,
producing error messages for any violations of CDL syntax.

The ncgen tool is now is capable of producing netcdf-4 files. It
operates essentially identically to the original ncgen.

The CDL input to ncgen may include data model constructs from the
netcdf- data model. In particular, it includes new primitive types
such as unsigned integers and strings, opaque data, enumerations, and
user-defined constructs using vlen and compound types. The ncgen man
page should be consulted for more detailed information.

UNIX syntax for invoking ncgen:

\code
     ncgen [-b] [-o netcdf-file] [-c] [-f] [-k<kind>] [-l<language>] [-x] [input-file]
\endcode

where:

<pre>
-b
Create a (binary) netCDF file. If the '-o' option is absent, a default
file name will be constructed from the netCDF name (specified after
the netcdf keyword in the input) by appending the '.nc'
extension. Warning: if a file already exists with the specified name
it will be overwritten.

-o netcdf-file
Name for the netCDF file created. If this option is specified, it
implies the '-b' option. (This option is necessary because netCDF
files are direct-access files created with seek calls, and hence
cannot be written to standard output.)

-c
Generate C source code that will create a netCDF dataset matching the
netCDF specification. The C source code is written to standard
output. This is only useful for relatively small CDL files, since all
the data is included in variable initializations in the generated
program. The -c flag is deprecated and the -lc flag should be used
intstead.

-f
Generate FORTRAN source code that will create a netCDF dataset
matching the netCDF specification. The FORTRAN source code is written
to standard output. This is only useful for relatively small CDL
files, since all the data is included in variable initializations in
the generated program. The -f flag is deprecated and the -lf77 flag
should be used intstead.

-k
The -k file specifies the kind of netCDF file to generate. The
arguments to the -k flag can be as follows.
        'classic', 'nc3' – Produce a netcdf classic file format file.
        '64-bit offset', 'nc6' – Produce a netcdf 64 bit classic file format file.
        '64-bit data (CDF-5), 'nc5' – Produce a CDF-5 format file.
        'netCDF-4', 'nc4' – Produce a netcdf-4 format file.
        'netCDF-4 classic model', 'nc7' – Produce a netcdf-4 file format, but restricted to netcdf-3 classic CDL input.

Note that the -v flag is a deprecated alias for -k. The code 'nc7' is
used as a short form for the unwieldy 'netCDF-4 classic model' because
7=3+4, a mnemonic for the format that uses the netCDF-3 data model for
compatibility with the netCDF-4 storage format for performance. The
old version format numbers '1', '2', '3', '4', equivalent to the
format names 'nc3', 'nc6', 'nc4', or 'nc7' respectively, are also
still accepted but deprecated, due to easy confusion between format
numbers and format names. Various old format name aliases are also
accepted but deprecated, e.g. 'hdf5', 'enhanced-nc3', for 'netCDF-4'.


-l
The -l file specifies that ncgen should output (to standard output)
the text of a program that, when compiled and executed, will produce
the corresponding binary .nc file. The arguments to the -l flag can be
as follows.
        c|C => C language output.
        f77|fortran77 => FORTRAN 77 language output; note that currently only the classic model is supported for fortran output.

-x
Use “no fill” mode, omitting the initialization of variable values
with fill values. This can make the creation of large files much
faster, but it will also eliminate the possibility of detecting the
inadvertent reading of values that haven't been written.
</pre>

<h1>Examples</h1>

Check the syntax of the CDL file foo.cdl:

\code
     ncgen foo.cdl
\endcode

From the CDL file foo.cdl, generate an equivalent binary netCDF file
named bar.nc:

\code
     ncgen -o bar.nc foo.cdl
\endcode

From the CDL file foo.cdl, generate a C program containing netCDF
function invocations that will create an equivalent binary netCDF
dataset:

\code
     ncgen -l c foo.cdl > foo.c
\endcode

\section guide_ncgen3 ncgen3

The ncgen3 tool is the new name for the older, original ncgen utility.

The ncgen3 tool generates a netCDF file or a C or FORTRAN program that
creates a netCDF dataset. If no options are specified in invoking
ncgen3, the program merely checks the syntax of the CDL input,
producing error messages for any violations of CDL syntax.

The ncgen3 utility can only generate classic-model netCDF-4 files or
programs.

UNIX syntax for invoking ncgen3:

\code
     ncgen3 [-b] [-o netcdf-file] [-c] [-f] [-v2|-v3|-v5] [-x] [input-file]
\endcode

where:
<pre>
-b
Create a (binary) netCDF file. If the '-o' option is absent, a default
file name will be constructed from the netCDF name (specified after
the netcdf keyword in the input) by appending the '.nc'
extension. Warning: if a file already exists with the specified name
it will be overwritten.

-o netcdf-file
Name for the netCDF file created. If this option is specified, it
implies the '-b' option. (This option is necessary because netCDF
files are direct-access files created with seek calls, and hence
cannot be written to standard output.)

-c
Generate C source code that will create a netCDF dataset matching the
netCDF specification. The C source code is written to standard
output. This is only useful for relatively small CDL files, since all
the data is included in variable initializations in the generated
program.

-f
Generate FORTRAN source code that will create a netCDF dataset
matching the netCDF specification. The FORTRAN source code is written
to standard output. This is only useful for relatively small CDL
files, since all the data is included in variable initializations in
the generated program.

-v2
The generated netCDF file or program will use the version of the
format with 64-bit offsets, to allow for the creation of very large
files. These files are not as portable as classic format netCDF files,
because they require version 3.6.0 or later of the netCDF library.

-v3
The generated netCDF file will be in netCDF-4/HDF5 format. These files
are not as portable as classic format netCDF files, because they
require version 4.0 or later of the netCDF library.

-v5
The generated netCDF file or program will use the version of the
format with 64-bit integers, to allow for the creation of very large
variables. These files are not as portable as classic format netCDF files,
because they require version 4.4.0 or later of the netCDF library.

-x
Use “no fill” mode, omitting the initialization of variable values
with fill values. This can make the creation of large files much
faster, but it will also eliminate the possibility of detecting the
inadvertent reading of values that haven't been written.
</pre>

\page users_guide_appendices Appendices

The following appendices are available.

- \subpage attribute_conventions
- \subpage file_format_specification