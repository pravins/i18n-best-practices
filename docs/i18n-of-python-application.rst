I18N of Python Application
##########################

In this section we will see important points developers must consider and include during the development of Python applications.

Data types
**********

As discussed earlier, Unicode is utmost important element of i18n and your application must be capable of processing and saving Unicode text.

Python 3 onwards, strings are always `unicode` whereas in Python 2 `str` type is different from `unicode`. So, in Python 2 textual literals should be marked with a `u` prefix. As in

::

    u'きたないのよりきれいな方がいい\n'

Reading from files
******************

In Python 2 the `open()` function returns a `str` object, which makes it compulsory to mention `encoding='UTF-8'` while opening. Whereas in Python 3 opening a file with `t` flag returns an `unicode` object and it will be decoded from the system default encoding. Generally its always safe to mention `encoding='UTF-8'`.

Libraries
*********

Python `gettext` module provides i18n services, which depends on underline `gettext` library only. One can also use other libraries as per requirement or choice like `Babel`.

I18N of "Hello World"
*********************

Non i18n helloworld.py
======================

Most of the developer already familiar with printing `'Hello World.'` in Python.

::

    #!/usr/bin/env python

    print('Hello World.')


Internationalized helloworld.py
===============================

Now lets see, How to internationalized above Helloworld.py with Gettext framework.

.. code:: python

    import gettext

    # Set domain, attach base dir, enable fallback
    t = gettext.translation('helloworld', 'locale', fallback=True)
    _ = t.ugettext

    print (_('Hello World.'))



Explainations
-------------
::

    import gettext

This imports APIs provided by `gettext` python module.

::

    t = gettext.translation('helloworld', 'locale', fallback=True)

This creates a Translations instance `t` based on the domain `('helloworld')` , localedir `('.mo files path')`, and languages. Here, the program binds `helloworld` domain and sets `locale` dir for the path where `.mo` files will be searched. If no `.mo` file is found, this raises `IOError` if fallback is false `(which is the default)`, and returns a `NullTranslations` instance if `fallback` is `true`.

::

    _ = t.ugettext**

This creates an instance of `ugettext` which returns the translated message as a `Unicode` string.

::

    printf(_("Hello World\n"));

Notice “_” here, it calls ugettext function.

This is your internationalized program capable of extracting string for translation and substitute as per your locale. Now in next section see how to extract string, translate those and actually get translation while running application.

Preparing PO file
=================

From your program folder run following commands

::


   $xgettext -d helloworld -o helloworld.pot -s helloworld.py

- xgettext - extract gettext strings from source.

- ‘-d’ domainname, ‘-o’ output filename, ‘-s’ generate sorted output.

::

    # SOME DESCRIPTIVE TITLE.
    # Copyright (C) YEAR THE PACKAGE'S COPYRIGHT HOLDER
    # This file is distributed under the same license as the PACKAGE package.
    # FIRST AUTHOR <EMAIL@ADDRESS>, YEAR.
    #
    #, fuzzy
    ""
    "Project-Id-Version: PACKAGE VERSION\n"
    "Report-Msgid-Bugs-To: \n"
    "POT-Creation-Date: 2017-02-14 17:06+0530\n"
    "PO-Revision-Date: YEAR-MO-DA HO:MI+ZONE\n"
    "Last-Translator: FULL NAME <EMAIL@ADDRESS>\n"
    "Language-Team: LANGUAGE <LL@li.org>\n"
    "Language: \n"
    "MIME-Version: 1.0\n"
    "Content-Type: text/plain; charset=CHARSET\n"
    "Content-Transfer-Encoding: 8bit\n"

    #: helloworld.py:7
    msgid   "Hello World."
    msgstr  ""

- helloworld.pot is the Portable Object Template (.pot) file developer suppose to share with translators.

Translations
============

Just rename .pot file to respective language code and provide it for translations.

**Example**

- For French language - fr.po

Translators need to update header of PO file as per required and add string for particular language as a substitute.

**Example** : In above case

::
 
   msgid "Hello World\n"
   msgstr ""

Here for French

::

  msgid "Hello World."
  msgstr "Bonjour le monde"


Execution of program
====================

Creating .mo file from .po file
-------------------------------

MO - Machine Object file
^^^^^^^^^^^^^^^^^^^^^^^^
Mo file is compiled binary version of .po file and it is more efficient for processing by computers.

::

   $msgfmt helloworld.po -o helloworld.mo

Msgfmt is program to compile message catalog to binary format.

Placing .mo file in folder
^^^^^^^^^^^^^^^^^^^^^^^^^^

In helloworld.py program, we have given path of “./locale” to bind textdomain.

To make our translations accessible to program during execution with particular locale those must be kept in this location.

Gettext looks for particular locale code under this directory while searching for translation file.

::

   $sudo mkdir -p ./locale/fr/LC_MESSAGES/
   $cp helloworld.mo ./locale/fr/LC_MESSAGES/

Demonstration of application
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Make sure you are in directory of helloworld.py

- Run program

::

   $python helloworld.py
   Hello World
   $LANGUAGE=fr python helloworld.py
   Bonjour le monde

Automation for i18n
^^^^^^^^^^^^^^^^^^^

In above helloworld.py internationalization we did many step one by one. In real life programming once .POT file is generated developer need to copy it for many languages and distribute them to translators and after collecting build application accordingly.

**Example**:

::

    1  $xgettext -d helloworld -o helloworld.pot -s helloworld.py
    2  cp helloworld.pot fr.po
       # Get translated fr.po from Translator
       
    3  $msgfmt helloworld.po -o helloworld.mo
    4  cp helloworld.mo ./locale/fr/LC_MESSAGES/

One may generalize above steps using autotools or Makefile as per requirement of program.
