RFC 3 -- .slugignore file
=========================

Abstract
~~~~~~~~

This document specifies syntax of the ``.slugignore`` file, a format for
describing a set of file path patterns to exclude when building an archive of
the source code for an application before deployment.

The format is very strongly inspired by the ``.gitignore`` file format (see
gitignore_).

.. _gitignore: https://git-scm.com/docs/gitignore

Preamble
~~~~~~~~

Copyright (c) 2015 smartmob contributors

This Specification is free software; you can redistribute it and/or modify it
under the terms of the GNU General Public License as published by the Free
Software Foundation; either version 3 of the License, or (at your option) any
later version. This Specification is distributed in the hope that it will be
useful, but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public
License for more details. You should have received a copy of the GNU General
Public License along with this program; if not, see
<http://www.gnu.org/licenses>.

Problem statement
~~~~~~~~~~~~~~~~~

Formal specification
~~~~~~~~~~~~~~~~~~~~

All text MUST be UTF-8_ encoded, without BOM.  Implementations SHOULD reject
files that contain invalid UTF-8 data.

Leading whitespace is ignored for all lines, including commands, blank lines
and comments.

Any blank line (possibly containing only whitespace) MUST be ignored.

Any line starting with a hash/pound ("#") is considered a comment for human
readers and is ignored.  If a rule should start with a hash/pound, the line can
be prefixed with a backslash ("\") to escape the comment delimiter.

Trailing whitespace SHALL be ignored on all lines unless the character
immediately preceding the first whitespace character is a backslash ("\").

A file pattern beginning with a slash ("/") SHALL only match at the start of
the full file path.  A file pattern not beginning with a full slash MAY match
at the end of a file path.

Two consecutive asterisks ("**") SHALL match zero or more directory components.

A file pattern ending with a slash ("/") SHALL NOT match regular files but MAY
match a directory entry.  In case of a match, all files within the directory
SHALL be ignored (recursively).

The file MUST NOT contain any lines that are not a file pattern exclusion rule,
not a comment and not blank.  Implementations MAY reject files containing
non-blank, non-comment lines that do not match a file pattern exclusion rule.

The name ``.slugignore`` MUST always be implicitly ignored.

Implementations MAY choose to implement additional implicit patterns as it sees
fit.  For example, in context, it may be useful to ignore common source control
bookkeeping directories such as ``.git/``.  Implementations MUST document these
additional implicit patterns and SHOULD offer a mechanism to disable these
patterns, for example using configuration options.

.. _UTF-8: https://www.ietf.org/rfc/rfc2279.txt

Examples
~~~~~~~~

Ignore documentation and test files::

  docs/
  test/

Ignore ``.pyc`` files in all directories::

  **/*.pyc

Ignore PNG images at the root and in the documentation, but keep those in other
folders (such as an ``assets`` folder)::

  *.png
  docs/**/*.png

Reference implementations
~~~~~~~~~~~~~~~~~~~~~~~~~

Git_ proposed the ``.gitignore`` format, which Heroku_ then adapted for use in
``.slugignore`` files for use with their slug compiler.

.. _Git: https://git-scm.com/
.. _Heroku: https://www.heroku.com/

Differences with existing implementations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The are no known differences with the ``.slugignore`` format used by Heroku.
However, the format is not formally documented (ex: it refers to the
``.gitignore`` format, but does not specify a Git version) so there is no
guarantee that this specification is 100% compatible with Heroku's.

The prefix negate operator ("!") feature of ``.gitignore`` files is not
supported (as per Heroku's documentation about the ``.slugignore`` format).

Security considerations
~~~~~~~~~~~~~~~~~~~~~~~

All file patterns are relative to the location of the ``.slugignore`` file.
Even if the file patterns can only be used to exclude files and that the
conventional ".." syntax for referring to the parent directory does not carry
that meaning here (it is useless), implementations should still beware of
performance implications of directory traversal attacks if they implement the
filesystem walk naively.  Implementations SHOULD use the following algorithm to
avoid this pitfall: recursively traverse the directory which contains the
``.slugignore`` file and test each path name against the patterns in the
``.slugignore`` file.
