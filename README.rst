=====================
Xerte online Toolkits
=====================

Xerte Online toolkits is an authoringtool for interactive Electronic Learning Objects. The Xerte Project aims to provide high quality free software to educators all over the world, and to build a global community of users and developers. We believe that simple things should be simple and that anything should be possible.

The Xerte Project has built a welcoming, supportive and friendly community of users and developers all over the world. Our users are the most important members of our community. The Xerte Project is a meritocratic, consensus-based community project, and anyone with an interest in the project can join the community, contribute to the project design and participate in the decision making process. If you would like to get involved in a successful and growing open-source community - and you do not need to have technical skills.

If you would like to contribute to the documentation of the Xerte project, here are some guidlines:

How to contribute
=================

The easiest way to contribute is by forking and editing the repository directly on GitHub:

* Create a GitHub account if you don't already have it
* Go to https://github.com/Xerte/docs and fork the project
* You can now edit any page using GitHub web interface and see a live preview of the output
* When you're done, simply create a new pull request
* A new automatic build is launched after the pull request is merged by a developer

You can also use the traditional way by cloning the ``docs``
repository (https://github.com/nethesis/nethserver-docs ) to your
machine and sending patches to the mailing list.

While editing, please follow the guidelines below.

Editing guidelines
------------------

The document must start with a title such as ::

    ==============
    Document title
    ==============

Make sure to add only *one* first-level title for each rst file.

Next headers levels are::

    First level
    ===========

    Second level
    ------------

    Third level
    ^^^^^^^^^^^

    Fourth level
    ~~~~~~~~~~~~


To create cross-references set a label before headers, with ``-section`` suffix::

    .. _mytitle-section:

    My title
    --------

In other documents refer to "My title" with the ``:ref:`` command::
    
    More informations are explained on :ref:`mytitle-section`
    

Use the \* character for unordered list ::
 
    * First element
    * Second element

Use a definition list when describing fields ::

    My field
        This is my description

A field description can also contain a bullet list ::

    My field
        This is my description

        * First element
        * Second element

Highlight important words ::
   
    This is and *important* word.
    
Add notes with ::
    
    .. note:: Highlight this text

Add warnings with ::

    .. warning:: Warning text fragments


    
You can find more info about **RST Inline Markup** here: rst-cheatsheet_

.. _rst-cheatsheet: https://github.com/ralsina/rst-cheatsheet/blob/master/rst-cheatsheet.rst
 

Do not use *Xerte* name inside documentation. Any time you need to add the product name, 
use the *product* macro::

  |product| has this amazing feature!

Output will be::

  Xerte has this amazing feature!

The same applies to *download_site* macro.

Use semantic markup whenever possible. Recommended RST roles are:

* guilabel
* file
* command
* menuselection

Remember to emphasize system object with *:dfn:*, only the first time you mention them inside a section.
For example if you are naming a system user::

 The :dfn:`admin` user is mighty powerful.

Also take care of indexing important content. You must index a word only one time per section::
 
 The :dfn:`admin` user is mighty powerful.
 Remember to change the :index:`admin` password.

The output will be a paragraph where the first *admin* word will be italic, the latter will use standard font
but it will be indexed.

See also: http://sphinx-doc.org/markup/inline.html

Use a spell checker program before submitting a pull request. For instance run ::

  hunspell -d en_US <filename>

License
-------

The project is licensed under the Apache 2 license
