I18N of C Application
#####################

In this section we will see what are important points developers must consider and include during the development of C programming applications.

Data types
**********

As discussed in earlier sections Unicode is utmost important element of i18n. Your application must be capable of processing and saving Unicode text.

- **char** : This is 8-bit Data type and this can be used for UTF-8.

- **wchar_t** : Here w stands for wide char. This datatype was introduced to support Unicode but its size is dependant on compiler. To avoid this confusion now we have char16_t and char32_t.

- **char16_t** : This is for 16-bit code units. Unicode basic multilingual plane i.e. from u+0000 - u+FFFF can be represented by this data type.

- **char32_t** : This is for 32-bit code units. Unicode is of 21-bit, so all Unicode data can be represented by this datatype.

As discussed in Encoding sections thought UTF16 and UTF32 are easy for computation from storage efficiency perspective it’s good to use UTF-8.

.. Tip:: Use UTF-8 as much as possible and convert it to either UTF16 or UTF32 only when need for computation or string operations like uppercase kind conversions.

.. TODO: Add program for convert UTF-8 from lowercase to uppercase

Libraries
*********

Gettext is widely used libraries. One can also use other libraries as per requirement or choice. For Gettext libraries see section about L10N string extraction frameworks.

I18N of "Hello World"
*********************

Non i18n hellworld.c
====================

Most of the developer already familiar with Helloworld.c program.

::

    #include<stdio.h>

    int main()
    {
      printf("Hello World\n");
      return 0;
    }

Internationalized helloworld.c
==============================

Now lets see, How to internationalized above Helloworld.c with Gettext framework.

::

    #include<libintl.h>
    #include<locale.h>
    #include<stdio.h>
    #define _(String) gettext (String)
    
    int main()
    {
      setlocale(LC_ALL,"");
      bindtextdomain("helloworld","/usr/share/locale");
      textdomain("helloworld");
      printf(_("Hello World\n"));
      return 0;
    }


Explainations
-------------

libintl.h
^^^^^^^^^

This header for defines the macros __USE_GNU_GETTEXT, __GNU_GETTEXT_SUPPORTED_REVISION, and declares the functions gettext, dgettext, dcgettext, ngettext, dngettext, dcngettext, textdomain, bindtextdomain,
bind_textdomain_codeset.

Gnulib module: gettext provides this header file.

locale.h
^^^^^^^^

This header file provided the macros required for location specific information. This includes
LC_ALL, LC_COLLATE, LC_CTYPE, LC_MONETARY, LC_NUMERIC, LC_TIME

Provides functions like localeconv and setlocale.

#define _(String) gettext (String)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For code readability this is usual practice of defining #define and calling gettext function just by adding “_” before string.

setlocale(LC_ALL,"");
^^^^^^^^^^^^^^^^^^^^^

As per ISO C all programs start by default in the standard ‘C’ locale. To use the locales specified by the environment, one has to call setlocale. Once you call above function locale environment variables will be set to your system or present locale.

More information about in in Locales section.

Bindtextdomain
^^^^^^^^^^^^^^

This function can be used to specify the directory which contains the message catalogs for domain domainname for the different languages. Good to provide absolute path to avoid confusion.

textdomain("helloworld")
^^^^^^^^^^^^^^^^^^^^^^^^

The textdomain function sets the default domain, which is used in all future gettext calls, to domainname. Before the first call to textdomain the default domain is messages.

printf(_("Hello World\n"));
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Watch “_” here, we are calling gettext function here.

This is your internationalized program capable of extracting string for translation and substitute as per your locale. Now in next section see how to extract string, translate those and actually get translation while running application.

Preparing PO file
=================

From your program folder run following commands

::

   $mkdir po

   $xgettext -d helloworld -o po/helloworld.pot -k_ -s helloworld.c

- xgettext - extract gettext strings from source.

- ‘-d’ domainname, ‘-o’ output filename, ‘-s’ generate sorted output.

::

   $cat po/helloworld.pot

   # SOME DESCRIPTIVE TITLE.
   # Copyright (C) YEAR THE PACKAGE'S COPYRIGHT HOLDER
   # This file is distributed under the same license as the PACKAGE
   package.
   # FIRST AUTHOR <EMAIL@ADDRESS>, YEAR.
   #
   #, fuzzy
   msgid ""
   msgstr ""
   "Project-Id-Version: PACKAGE VERSION\n"
   "Report-Msgid-Bugs-To: \n"
   "POT-Creation-Date: 2017-01-13 15:18+0530\n"
   "PO-Revision-Date: YEAR-MO-DA HO:MI+ZONE\n"
   "Last-Translator: FULL NAME <EMAIL@ADDRESS>\n"
   "Language-Team: LANGUAGE <LL@li.org>\n"
   "Language: \n"
   "MIME-Version: 1.0\n"
   "Content-Type: text/plain; charset=CHARSET\n"
   "Content-Transfer-Encoding: 8bit\n"
   #: i18nhello.c:12
   #, c-format
   msgid "Hello World\n"
   msgstr ""

- helloworld.pot is the Portable Object Template (.pot) file developer suppose to share with translators.

Translations
============

Just rename .pot file to respective language code and provide it for translations.

**Example**

- For Hindi language - hi.po

- For particular language spoken in multiple countries. Like Bengali (Spoken in India as well in Bangladesh) - hi_IN.po or hi_BD.po

- For languages with particular encoding.
  - zh_CN_GB2312.po

Translators need to update header of PO file as per required and add string for particular language as a substitute.

**Example** : In above case

::
 
   msgid "Hello World\n"
   msgstr ""

Here for Hindi

::

  msgid "Hello World\n"
  msgstr "नमस्ते दुनिया "


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

In helloworld.c program, we have given path of “/usr/share/locale” in bindtextdomain function.

To make our translations accessible to program during execution with particular locale those must be kept in this location.

Gettext looks for particular locale code under this directory while searching for translation file.

::

   $sudo mkdir /usr/share/locale/hi/LC_MESSAGES/
   $cp helloworld.mo /usr/share/locale/hi/LC_MESSAGES/

Demonstration of application
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Compile helloworld.c, make sure you are in directory of helloworld.c

::

   $gcc -o helloworld helloworld.c

- Run program

::

   $./helloworld
   Hello Wolrd
   $LANG=hi_IN ./helloworld
   नम ते द ु नया

Automation for i18n
^^^^^^^^^^^^^^^^^^^

In above helloworld.c internationalization we did many step one by one. In real life programming once .POT file is generated developer need to copy it for many languages and distribute them to translators and after collecting build application accordingly.

**Example**:

::

    1  $xgettext -d helloworld -o po/helloworld.pot -k_ -s helloworld.c
    2  cp po/helloworld.pot hi.po
       # Get translated hi.po from Translator
       
    3  $msgfmt helloworld.po -o helloworld.mo
    4  cp helloworld.mo /usr/share/locale/hi/LC_MESSAGES/

One need to generalize above steps using autotools or Makefile as per requirement of program.
