.. list-table::
   :widths: 40 10 10 10 10 10 10
   :header-rows: 0

   * -
     - English
     - `Русский <https://regex.sorokin.engineer/ru/latest/regular_expressions.html>`__
     - `Deutsch <https://regex.sorokin.engineer/de/latest/regular_expressions.html>`__
     - `Български <https://regex.sorokin.engineer/bg/latest/regular_expressions.html>`__
     - `Français <https://regex.sorokin.engineer/fr/latest/regular_expressions.html>`__
     - `Español <https://regex.sorokin.engineer/es/latest/regular_expressions.html>`__

Regular expressions (RegEx)
===========================

Introduction
------------

Regular expressions are a handy way to specify patterns of
text.

With regular expressions you can validate user input, search for some
patterns like emails of phone numbers on web pages or in some documents
and so on.

Below is the complete regular expressions cheat sheet.

Characters
----------

Simple matches
~~~~~~~~~~~~~~

Any single character (except special regex characters) matches itself.
A series of (not special) characters matches that series of characters in the input
string.

========== ==========
RegEx      Matches
========== ==========
``foobar`` ``foobar``
========== ==========

Non-Printable Characters (escape-codes)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To specify character by its Unicode code, use the prefix ``\x`` followed by the hex code.
For 3-4 digits code (after U+00FF), enclose the code into braces. 

============== ==============================================
RegEx          Matches
============== ==============================================
``\xAB``       character with 2-digit hex code ``AB``
``\x{AB20}``   character with 1..4-digit hex code ``AB20``
``foo\x20bar`` ``foo bar`` (note space in the middle)
============== ==============================================

There are a number of predefined escape-codes for non-printable characters,
like in C language:

=================== ==========================================================================
RegEx               Matches
=================== ==========================================================================
``\t``              tab (HT/TAB), same as ``\x09``
``\n``              line feed (LF), same as ``\x0a``
``\r``              carriage return (CR), same as ``\x0d``
``\f``              form feed (FF), same as ``\x0c``
``\a``              alarm (BEL), same as ``\x07``
``\e``              escape (ESC), same as ``\x1b``
``\cA`` ... ``\cZ`` | chr(0) to chr(25).
                    | For example, ``\cI`` matches the tab-char. 
                    | Lower-case letters "a"..."z" are also supported.
=================== ==========================================================================

.. _escape:

Escaping
~~~~~~~~

To represent special regex character (one of ``.+*?|\()[]{}^$``), prefix it with a backslash ``\``.
The literal backslash must be escaped too. 

=============== ========================================================================
RegEx           Matches
=============== ========================================================================
``\^FooBarPtr`` ``^FooBarPtr``, this is ``^`` and not `start of line <#lineseparators>`__
``\[a\]``       ``[a]``, this is not `character class <#userclass>`__
=============== ========================================================================

Character Classes
-----------------

.. _userclass:

User Character Classes
~~~~~~~~~~~~~~~~~~~~~~

Character class is a list of characters inside square brackets ``[]``.
The class matches any **single** character listed in this class.

================= =============================================================
RegEx             Matches
================= =============================================================
``foob[aeiou]r``  ``foobar``, ``foober`` etc but not ``foobbr``, ``foobcr`` etc
================= =============================================================

You can "invert" the class - if the first character after the ``[`` is
``^``, then the class matches any character **except** the characters listed
in the class.

================= =============================================================
RegEx             Matches
================= =============================================================
``foob[^aeiou]r`` ``foobbr``, ``foobcr`` etc but not ``foobar``, ``foober`` etc
================= =============================================================

Within a list, the dash ``-`` character is used to specify a range, so that
``a-z`` represents all characters between ``a`` and ``z``, inclusive.

If you want the dash ``-`` itself to be a member of a class, put it at the start
or end of the list, or `escape <#escape>`__ it with a backslash.

If you want ``]`` as part of the class you may place it at the start of list or
`escape <#escape>`__ it with a backslash.

============= ==================================
RegEx         Matches
============= ==================================
``[-az]``     ``a``, ``z`` and ``-``
``[az-]``     ``a``, ``z`` and ``-``
``[a\-z]``    ``a``, ``z`` and ``-``
``[a-z]``     characters from ``a`` to ``z``
``[\n-\x0D]`` characters from chr(10) to chr(13)
============= ==================================

Meta-Classes
~~~~~~~~~~~~

There are a number of predefined character classes that keeps regular expressions
more compact, "meta-classes":

======     ==============================================
RegEx      Matches
======     ==============================================
``\w``     an alphanumeric character, including ``_``
``\W``     a non-alphanumeric
``\d``     a numeric character (same as ``[0-9]``)
``\D``     a non-numeric
``\s``     any space (same as ``[ \t\n\r\f]``)
``\S``     a non-space
``\h``     | horizontal whitespace: the tab and all characters
           | in the "space separator" Unicode category
``\H``     not a horizontal whitespace
``\v``     | vertical whitespace: all characters treated as
           | line-breaks in the Unicode standard
``\V``     not a vertical whitespace
======     ==============================================

You may use all meta-classes, mentioned in the table above, within
`user character classes <User Character Classes_>`_.

=============== =====================================================================================
RegEx           Matches
=============== =====================================================================================
``foob\dr``     ``foob1r``, ``foob6r`` and so on, but not ``foobar``, ``foobbr`` and so on
``foob[\w\s]r`` ``foobar``, ``foob r``, ``foobbr`` and so on, but not ``foob1r``, ``foob=r`` and so on
=============== =====================================================================================

.. note::
    `TRegExpr <tregexpr.html>`__

    Properties
    `SpaceChars <tregexpr.html#spacechars>`_ and
    `WordChars <tregexpr.html#wordchars>`_ define
    character classes ``\w``, ``\W``, ``\s``, ``\S``.

    So you can redefine these classes.

Boundaries
----------

.. _lineseparators:

Line Boundaries
~~~~~~~~~~~~~~~

============= ================================================
Metachar      Matches
============= ================================================
``.``         any character, can include line-breaks
``^``         zero-length match at start of line
``$``         zero-length match at end of line
``\A``        zero-length match at the very beginning
``\z``        zero-length match at the very end
``\Z``        like ``\z`` but also matches before the final line-break
============= ================================================

Examples:

============= ================================================
RegEx         Matches
============= ================================================
``^foobar``   ``foobar`` only if it's at the beginning of line
``foobar$``   ``foobar`` only if it's at the end of line
``^foobar$``  ``foobar`` only if it's the only string in line
``foob.r``    ``foobar``, ``foobbr``, ``foob1r`` and so on
============= ================================================

Metachar ``^`` matches zero-length position at the beginning of the input string.
``$`` - at the ending.
If `modifier /m <#m>`_ is **on**, they also match at the beginning/ending
of individual lines in the multi-line text.

Note that there is no empty line within the sequence ``\x0D\x0A``.

.. note::
    `TRegExpr <tregexpr.html>`__

    If you are using
    `Unicode version <tregexpr.html#unicode>`__, then ``^``/``$``
    also matches ``\x2028``, ``\x2029``, ``\x0B``, ``\x0C`` or ``\x85``.

Metachars ``\A`` matches zero-length position at the very beginning of the input string,
``\z`` - at the very ending. They ignore `modifier /m <#m>`_.
``\Z`` is like ``\z`` but also matches before the final line-break (LF and CR LF).

The ``.`` metacharacter by default matches any character, but if you
turn **off** the `modifier /s <#s>`_, then it won't match line-breaks inside the string.

Note that ``^.*$`` does not match a string between ``\x0D\x0A``,
because this is unbreakable line separator.
But it matches the empty string within the sequence ``\x0A\x0D`` because
this is 2 line-breaks in the wrong order.

.. note::
    `TRegExpr <tregexpr.html>`__

    Multi-line processing can be tuned by properties
    `LineSeparators <tregexpr.html#lineseparators>`__ and
    `UseLinePairedBreak <tregexpr.html#linepairedseparator>`_.

    So you can use Unix style separators ``\n`` or DOS/Windows style
    ``\r\n`` or mix them together (as in described above default behaviour).

If you prefer mathematically correct description you can find it on
`www.unicode.org <http://www.unicode.org/unicode/reports/tr18/>`__.


Word Boundaries
~~~~~~~~~~~~~~~

====== ===================
RegEx  Matches
====== ===================
``\b`` a word boundary
``\B`` a non-word boundary
====== ===================

A word boundary ``\b`` is a spot between two characters that has a
``\w`` on one side of it and a ``\W`` on the other side of it (in either
order).

.. _iterator:

Quantification
--------------

Quantifiers
~~~~~~~~~~~

Any item of a regular expression may be followed by quantifier.
Quantifier specifies number of repetition of the item.

========== ============================================================
RegEx      Matches
========== ============================================================
``{n}``    exactly ``n`` times
``{n,}``   at least ``n`` times
``{n,m}``  at least ``n`` but not more than ``m`` times
``*``      zero or more, similar to ``{0,}``
``+``      one or more, similar to ``{1,}``
``?``      zero or one, similar to ``{0,1}``
========== ============================================================

So, digits in curly brackets ``{n,m}``, specify the minimum
number of times to match ``n`` and the maximum ``m``.

The ``{n}`` is equivalent to ``{n,n}`` and matches exactly ``n`` times.
The ``{n,}`` matches ``n`` or more times.

There is no practical limit to the values n and m (limit is maximal signed 32-bit value).

================== ========================================================================
RegEx              Matches
================== ========================================================================
``foob.*r``        ``foobar``,  ``foobalkjdflkj9r`` and ``foobr``
``foob.+r``        ``foobar``, ``foobalkjdflkj9r`` but not ``foobr``
``foob.?r``        ``foobar``, ``foobbr`` and ``foobr`` but not ``foobalkj9r``
``fooba{2}r``      ``foobaar``
``fooba{2,}r``     ``foobaar'``, ``foobaaar``, ``foobaaaar`` etc.
``fooba{2,3}r``    ``foobaar``, or ``foobaaar``  but not ``foobaaaar``
``(foobar){8,10}`` ``8``, ``9`` or ``10`` instances of the ``foobar`` (``()`` is `Subexpression <#subexpression>`__)
================== ========================================================================

.. _greedy:

Greediness
~~~~~~~~~~

`Quantifiers <#iterator>`_ in "greedy" mode takes as many as possible,
in "lazy" mode - as few as possible.

By default all quantifiers are "greedy".
Append the character ``?`` to make any quantifier "lazy".

For string ``abbbbc``:

=========== ============
RegEx       Matches
=========== ============
``b+``      ``bbbb``
``b+?``     ``b``
``b*?``     empty string
``b{2,3}?`` ``bb``
``b{2,3}``  ``bbb``
=========== ============

You can switch all quantifiers into "lazy" mode (`modifier /g <#g>`_,
below we use `in-line modifier change <#inlinemodifiers>`_).

============ =======
RegEx        Matches
============ =======
``(?-g)b+``  ``b``
============ =======

Possessive Quantifier
~~~~~~~~~~~~~~~~~~~~~

The syntax is: ``a++``, ``a*+``, ``a?+``, ``a{2,4}+``.
Currently it's supported only for simple braces, but 
not for braces after group like ``(foo|bar){3,5}+``. 

This regex feature is `described here. <https://regular-expressions.mobi/possessive.html?wlr=1>`__
In short, possessive quantifier speeds up matching in complex cases.

Choice
------

Expressions in the choice are separated by vertical bar ``|``.

So ``fee|fie|foe`` will match any of ``fee``, ``fie``,
or ``foe`` in the target string (as would ``f(e|i|o)e``).

The first expression includes everything from the last pattern delimiter (``(``,
``[``, or the beginning of the pattern) up to the first ``|``, and the
last expression contains everything from the last ``|`` to the next
pattern delimiter.

Sounds a little complicated, so it’s common practice to include
the choice in parentheses, to minimize confusion about where it
starts and ends.

Expressions in the choice are tried from left to right, so the first expression
that matches, is the one that is chosen.

For example, regular expression ``foo|foot`` in string ``barefoot`` will match ``foo``.
Just a first expression that matches.

Also remember that ``|`` is interpreted as a literal within square
brackets, so if you write ``[fee|fie|foe]`` you’re really only matching
``[feio|]``.

================ ========================
RegEx            Matches
================ ========================
``foo(bar|foo)`` ``foobar`` or ``foofoo``
================ ========================

.. _subexpression:

Groups
------

The brackets ``( ... )`` are used to define regular expression groups (ie subexpressions).

.. note::
    `TRegExpr <tregexpr.html>`__

    Subexpression positions, lengths and actual values will be in
    `MatchPos <tregexpr.html#matchpos>`_,
    `MatchLen <tregexpr.html#matchlen>`_ and
    `Match <tregexpr.html#match>`_.

    You can substitute them with
    `Substitute <tregexpr.html#substitute>`_.

Subexpressions are numbered from left to right by their
opening parenthesis (including nested subexpressions).

First subexpression has number ``1``.
Whole regular expression has number ``0``.

.. highlights:: For example for input string ``foobar``
    regular expression ``(foo(bar))`` will find:

    ============= ==========
    subexpression value
    ============= ==========
    ``0``         ``foobar``
    ``1``         ``foobar``
    ``2``         ``bar``
    ============= ==========

Backreferences
--------------

Metacharacters ``\1`` through ``\9`` are interpreted as backreferences to groups.
They match the previously found group with the specified index.

=========== ============================
RegEx       Matches
=========== ============================
``(.)\1+``  ``aaaa`` and ``cc``
``(.+)\1+`` also ``abab`` and ``123123``
=========== ============================

RegEx ``(['"]?)(\d+)\1`` matches ``"13"`` (in double quotes), or ``'4'`` (in
single quotes) or ``77`` (without quotes) etc.

Named Groups and Backreferences
-------------------------------

To make some group (ie subexpression) named, use this syntax: ``(?P<name>expr)``. Also Perl syntax is supported: ``(?'name'expr)``.

Name of group must be valid identifier: first char is letter or "_", other chars are alphanumeric or "_". All named groups are also usual groups and share the same numbers 1 to 9.

Backreferences to named groups are ``(?P=name)``, the numbers ``\1`` to ``\9`` can also be used.

========================== ============================
RegEx                      Matches
========================== ============================
``(?P<qq>['"])\w+(?P=qq)`` ``"word"`` and ``'word'``
========================== ============================

Modifiers
---------

Modifiers are for changing behaviour of regular expressions.

You can set modifiers globally in your system or change inside the
regular expression using the `(?imsxr-imsxr) <#inlinemodifiers>`_.

.. note::
    `TRegExpr <tregexpr.html>`__

    To change modifiers use
    `ModifierStr <tregexpr.html#modifierstr>`__
    or appropriate ``TRegExpr`` properties
    `Modifier* <tregexpr.html#modifieri>`__.

    The default values are defined in `global
    variables <tregexpr.html#global-constants>`_. For example global variable
    ``RegExprModifierX`` defines default value for ``ModifierX`` property.

.. _i:

i, case-insensitive
~~~~~~~~~~~~~~~~~~~

Case-insensitive. Use installed in you system
locale settings, see also
`InvertCase <tregexpr.html#invertcase>`__.

.. _m:

m, multi-line strings
~~~~~~~~~~~~~~~~~~~~~

Treat string as multiple lines. So ``^`` and ``$`` matches the start or end
of any line anywhere within the string.

See also `Line Boundaries <#lineseparators>`__.

.. _s:

s, single line strings
~~~~~~~~~~~~~~~~~~~~~~

Treat string as single line. So ``.`` matches any
character whatsoever, even a line separators.

See also `Line Boundaries <#lineseparators>`__, which it
normally would not match.

.. _g:

g, greediness
~~~~~~~~~~~~~

.. note::
    `TRegExpr <tregexpr.html>`__ only modifier.

Switching it ``Off`` you’ll switch
`quantifiers <#iterator>`__ into `non-greedy <#greedy>`__ mode.

So, if modifier ``/g`` is ``Off`` then ``+`` works as ``+?``,
``*`` as ``*?`` and so on.

By default this modifier is ``On``.

.. _x:

x, eXtended syntax
~~~~~~~~~~~~~~~~~~

Allows to comment regular expression and break them up into
multiple lines.

If the modifier is ``On`` we ignore all whitespaces that
is neither backslashed nor within a character class.

And the ``#`` character separates comments.

Notice that you can use empty lines to format regular expression for
better readability:

.. code-block:: text

    (
    (abc) # comment 1
    #
    (efg) # comment 2
    )

This also means that if you want real whitespace or ``#`` characters in
the pattern (outside a character class, where they are unaffected by
``/x``), you’ll either have to escape them or encode them using
octal or hex escapes.

.. _r:

r, Russian ranges
~~~~~~~~~~~~~~~~~

.. note::
    `TRegExpr <tregexpr.html>`__ only modifier.

In Russian ASCII table characters ``ё``/``Ё`` are placed separately
from others.

Big and small Russian characters are in separated ranges, this is the same
as with English characters but nevertheless I wanted some short form.

With this modifier instead of ``[а-яА-ЯёЁ]`` you can write ``[а-Я]`` if
you need all Russian characters.

When the modifier is ``On``:

======= =======================================
RegEx   Matches
======= =======================================
``а-я`` chars from ``а`` to ``я`` and ``ё``
``А-Я`` chars from ``А`` to ``Я`` and ``Ё``
``а-Я`` all russian symbols
======= =======================================

The modifier is set `On` by default.

Assertions
----------

.. _assertions:

Currently engine supports only these kinds of assertions:

Positive lookahead assertion: ``foo(?=bar)`` matches "foo" only before "bar", and "bar" is excluded from the match.

Negative lookahead assertion: ``foo(?!bar)`` matches "foo" only if it's not followed by "bar".

Positive lookbehind assertion: ``(?<=foo)bar`` matches "bar" only after "foo", and "foo" is excluded from the match.

Assertions are allowed only at the very beginning and ending of expression. They can contain subexpressions of any complexity (quantifiers are allowed, even groups are allowed). Lookahead and lookbehind can be present both.

Non-capturing Groups
--------------------

Syntax is like this: ``(?:subexpression)``.

Such groups do not have the "index" and are invisible for backreferences.
Non-capturing groups are used when you want to group a subexpression, but you do not want to save it as a matched/captured portion of the string. So this is just a way to organize your regex into subexpressions without overhead of capturing result:

================================ =======================================
RegEx                            Matches
================================ =======================================
``(https?|ftp)://([^/\r\n]+)``   in ``https://sorokin.engineer`` matches
                                 ``https`` and ``sorokin.engineer``
``(?:https?|ftp)://([^/\r\n]+)`` in ``https://sorokin.engineer`` matches
                                 only ``sorokin.engineer``
================================ =======================================

Atomic Groups
-------------

Syntax is like this: ``(?>expr|expr|...)``.

Atomic groups are special case of non-capturing groups.
`Description of them. <https://regular-expressions.mobi/atomic.html?wlr=1>`__

Inline Modifiers
----------------

.. _inlinemodifiers:

Syntax is like this: ``(?i)``, ``(?-i)``, ``(?msgxr-imsgxr)``.

You may use it inside regular expression for modifying modifiers on-the-fly.
This can be especially handy because it has local scope in a regular
expression. It affects only that part of regular expression that follows
``(?imsgxr-imsgxr)`` operator.

And if it's inside subexpression it will
affect only this subexpression - specifically the part of the subexpression
that follows after the operator. So in ``((?i)Saint)-Petersburg`` it affects
only subexpression ``((?i)Saint)`` so it will match ``saint-Petersburg``
but not ``saint-petersburg``.

============================= ==================================================
RegEx                         Matches
============================= ==================================================
``(?i)Saint-Petersburg``      ``Saint-petersburg`` and ``Saint-Petersburg``
``(?i)Saint-(?-i)Petersburg`` ``Saint-Petersburg`` but not ``Saint-petersburg``
``(?i)(Saint-)?Petersburg``   ``Saint-petersburg`` and ``saint-petersburg``
``((?i)Saint-)?Petersburg``   ``saint-Petersburg``, but not ``saint-petersburg``
============================= ==================================================

Comments
--------

Syntax is like this: ``(?#text)``. Text inside brackets is ignored.

Note that the comment is closed by the nearest ``)``, so there is no way to put a literal ``)`` in
the comment.

Recursion
---------

Syntax is ``(?R)``, the alias is ``(?0)``.

The regex ``a(?R)?z`` matches one or more letters "a" followed by exactly the same number of letters "z".

The main purpose of recursion is to match balanced constructs or nested constructs. The generic regex is ``b(?:m|(?R))*e`` where "b" is what begins the construct, "m" is what can occur in the middle of the construct, and "e" is what occurs at the end of the construct.

If what may appear in the middle of the balanced construct may also appear on its own without the beginning and ending parts then the generic regex is ``b(?R)*e|m``.

Subroutine calls
----------------

Syntax for call to numbered groups: ``(?1)`` ... ``(?90)`` (maximal index is limited by code).

Syntax for call to named groups: ``(?P>name)``. Also Perl syntax is supported: ``(?&name)``.

This is like recursion but calls only code of capturing group with specified index.

Unicode Categories
------------------

Unicode standard has names for character categories. These are 2-letter strings. For example "Lu" is uppercase letters, "Ll" is lowercase letters. And 1-letter bigger category "L" is all letters.

* Cc - Control
* Cf - Format
* Co - Private Use
* Cs - Surrrogate
* Ll - Lowercase Letter
* Lm - Modifier Letter
* Lo - Other Letter
* Lt - Titlecase Letter
* Lu - Uppercase Letter
* Mc - Spacing Mark
* Me - Enclosing Mark
* Mn - Nonspacing Mark
* Nd - Decimal Number
* Nl - Letter Number
* No - Other Number
* Pc - Connector Punctuation
* Pd - Dash Punctuation
* Pe - Close Punctuation
* Pf - Final Punctuation
* Pi - Initial Punctuation
* Po - Other Punctuation
* Ps - Open Punctuation
* Sc - Currency Symbol
* Sk - Modifier Symbol
* Sm - Math Symbol
* So - Other Symbol
* Zl - Line Separator
* Zp - Paragraph Separator
* Zs - Space Separator

Meta-character ``\p`` denotes one Unicode char of specified category. Syntaxes: ``\pL`` and ``\p{L}`` for 1-letter name, ``\p{Lu}`` for 2-letter names.

Meta-character ``\P`` is inverted, it denotes one Unicode char **not** in the specified category.

These meta-characters are supported withing character classes too.

Afterword
---------

In this `ancient blog post from previous
century <https://sorokin.engineer/posts/en/text_processing_from_birds_eye_view.html>`__
I illustrate some usages of regular expressions.
