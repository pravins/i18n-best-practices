Important points for i18n
#########################

When developing internationalized application developer need to consider two important aspects as following:

1 Application should accept system’s locale or one provided by user and change its behaviour accordingly.
  Example:
  - **Website**: If you are developing website and end user is viewing it in browser running in Japanese locale. Website dates, time and currency format should get changed into Japanese locale.

  - **Desktop application**: It’s behaviour should change based on locale format of desktop.

2 Applications UI should get change according to system locale.

This is important aspect of internationalized application. Converting whole UI into target language without any major changes into application. Just by selecting appropriate system locale or browser locale. We will see this with example in coming few sections how can we achieve it.

Core i18n support on platform
*****************************

Target language must have been enabled on target platform. If target language is not available on platform.

1 User might not get required locales.

2 Fonts required for rendering target script might be not available. (With Webfonts this can be handled for Websites as discussed in Wen fonts section)

Application should accept system locale
***************************************

Non internationalized application starts in system’s default locale irrespective of active locale. Like in C program its  “C” locale. To get application use system locale or one which user expecting program must be initialized by setting locale. We will see how to do it with different programming language in coming sections.

Strings and Code separation
***************************

One of the important goal of internationalization is minimal code changes while moving products in between different languages. Before technology getting matured developer were maintaining different code bases for different language. I.e. Build for Japanese, English etc.

But this approach was not realistic for supporting many languages and also it was not dynamic.

Also other important issue comes, when we design buttons of application. Translating few words from one language to another results in translated word either bit lengthy or tiny. It affects app design drastically and it must be auto adjustable with overall design.

Example: English equivalent of Jet Black is Rabenschwarz

Separating code and string has been became much simpler now a days with the introductions of a frameworks like gettext.

Framework like gettext not only help to extract string elements from codebase but also help to select exact language for replacement of strings depending upon locale.


