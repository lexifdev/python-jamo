.. _Jamo:

============================
A Guide to using Python-Jamo
============================

`Hangul <https://en.wikipedia.org/wiki/Hangul>`_ is a wonderful writing system.
Originating in 1443 to represent the Korean language, it is an alphabet of 24
consonants and vowels, each of which are called **jamo** (자모 aka 字母).

Let's analyze it's phonemes by decomposing some Hangul. Maybe even construct
some Hangul afterwards.


Hangul Decomposition
--------------------

The python jamo library aims to provide a straightforward interface to Hangul
decomposition::

    >>> from jamo import h2j
    >>> h2j("한굴")
    '한굴'

Oops! What is that? Notice that the characters may not have rendered correctly.
This is because there are actually two sets of jamo in Unicode. We printed the
U+11xx set. The set that computers actually use for **rendering** individual
jamo characters is called **Hangul Compatibility Jamo**, or **HCJ**. What we
probably want is::

    >>> from jamo import h2j, j2hcj
    >>> j2hcj(h2j("한굴"))
    'ㅎㅏㄴㄱㅜㄹ'
    >>> j2hcj(h2j("자모=字母"))
    'ㅈㅏ\x00ㅁㅗ\x00=字母'

Here we convert the Hangul characters to U+11xx jamo characters, then convert
those characters to HCJ for proper display. The `\x00` characters are the null
tails of the hangul characters. This feature may be changed in future releases.

This documentation has a short writeup on `Hangul Compatibility Jamo` (soon).
Also related, Gernot Katzers has an excellent writeup on
`Hangul representation in unicode`_ that is well worth a read.


Hangul Synthesis
----------------

There are two functions for Hangul synthesis. The first combines a lead, vowel,
and optional tail::
    
    >>> from jamo import j2h
    >>> j2h('ㅇ', 'ㅕ', 'ㅇ')
    영
    >>> j2h('ㅇ', 'ㅓ')
    어

Ambiguity may arise when there is a long string of jamo. The library does its
best to guess what the original Hangul was::

    >>> from jamo import synth_hangul
    >>> synth_hangul("...")
    (True, ...)
    >>> synth_hangul("...")
    (False, ...)

Note that when there are ambiguous cases, `False` is returned as the status.
This `synth_hangul` feature has not come out yet.


Large Texts
------------

When working with large files, we will end up with lots of output. To handle
large files, it is recommended to use the generator functions::

    >>> from jamo import hangul_to_jamo
    >>> long_story = open("구운몽.txt", 'r').read()
    >>> hangul_to_jamo(long_story)
    <generator at 0x7f31baf89cc0>

and for HCJ::

    >>> from jamo import hangul_to_hcj
    >>> long_story = open("구운몽.txt", 'r').read()
    >>> hangul_to_hcj(long_story)
    <generator at 0x7f31baf89cc0>


.. _Hangul representation in unicode: http://gernot-katzers-spice-pages.com/var/korean_hangul.html
