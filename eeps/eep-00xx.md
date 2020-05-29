    Author: Richard Carlsson <carlsson(dot)richard(at)gmail(dot)com>
    Status: Draft
    Type: Standards Track
    Created: 29-May-2020
    Erlang-Version: 24
    Post-History:
****
EEP XX: UTF-8 Binary String Literals and Concatenation Operator
----



Abstract
========

This EEP extends the syntax of Erlang with backtick-quoted literals ``
`...` `` for UTF-8 encoded binary strings, and a binary concatenation
operator `<>` for simplifying composing such strings.



Rationale
=========

In modern Erlang code, it is often desirable to use UTF-8 encoded
binaries as the main format for text strings - and indeed, this is the
preferred representation of text in Elixir. There now exists a fair
amount of library support in Erlang for working with such
binaries. However, the syntax for writing a UTF-8 encoded binary
string literal in Erlang is `<<"...text..."/utf8>>`, which is clunky
to say the least. It is also easily confused with the shorter syntax
`<<"...text...">>` which instead creates a Latin-1 encoded binary (and
currently, this will silently truncate any code points above 255 to
the lower 8 bits).

There is furthermore no concise syntax for concatenating multiple such
binaries, which is needed when building a larger string from
parts. One way is to write `<<A/binary, B/binary>>`. Another is to
create a deep list `[A, B]` and then join this through
`unicode:characters_to_binary/2`. In Elixir, the corresponding syntax
is simply `A <> B`.



Specification
=============

A new form of string literal is added to Erlang, using back-quotes
(also called "back-ticks"):

    `...text...`

This is equivalent to the longer `<<"...text..."/utf8>>`, and can be
used in any position where such a binary literal can be used, both in
expressions and patterns.

Furthermore, a new operator `<>` is added (also available as
`erlang:'<>'/2`), with the same precedence as the `++` operator. The
implementation is simply that

    A <> B

is equivalent to

    <<A/binary, B/binary>>



Rejected alternatives
=====================

The syntax needs to be convenient enough to be a major improvement
over writing `<<"..."/utf8>>`, so quotation markers using 2 or more
characters such as, say, `<<'...'>>` are disqualified. We also do not
want to add new special cases to the literal syntax, such as `<"`
being different from `< "` with a space between the characters.

It would be possible to have a notation using a "sigil" such as the
`U"..."` or `u8"..."` notation in C/C++, or the `~s"..."` notation
used in Elixir, but this looks frankly terrible for code that uses a
lot of such strings, and there is a risk of accidentally omitting the
sigil now and then.

Finally, it is not desirable to use double quote characters for a
notation that creates binaries, since this could often cause confusion
with the ordinary double quoted strings which represent lists of
integers. Similarly, single quotes are strongly associated with
atoms. Therefore, back-quotes, though not available without using a
shift key on many keyboard layouts, seem to be the best choice.



Implementation
==============

A reference implementation exists in the author's [backticks][1]
branch on GitHub.



[1]: https://github.com/richcarl/otp/tree/backticks



Copyright
=========

This document has been placed in the public domain.



[EmacsVar]: <> "Local Variables:"
[EmacsVar]: <> "mode: indented-text"
[EmacsVar]: <> "indent-tabs-mode: nil"
[EmacsVar]: <> "sentence-end-double-space: t"
[EmacsVar]: <> "fill-column: 70"
[EmacsVar]: <> "coding: utf-8"
[EmacsVar]: <> "End:"
[VimVar]: <> " vim: set fileencoding=utf-8 expandtab shiftwidth=4 softtabstop=4: "
