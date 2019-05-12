===========
akinator.py
===========

**An API wrapper for the online game, Akinator, written in Python**

.. image:: https://img.shields.io/badge/pypi-v1.1.1-blue.svg
    :target: https://pypi.python.org/pypi/akinator.py/

.. image:: https://img.shields.io/badge/python-%E2%89%A53.5.3-yellow.svg
    :target: https://www.python.org/downloads/

"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Copyright © 2019 NinjaSnail1080

Licensed under the MIT License (see ``LICENSE.txt`` for details).

`Akinator.com <https://www.akinator.com>`_ is an online game where you think of a character, real or fiction, and by asking you questions the site will try to guess who you're thinking of. This library allows for easy access to the Akinator API and makes writing programs that use it much simpler.

"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

**********
Installing
**********

To install the regular library without async support, just run the following command::

  python3 -m pip install -U akinator.py

Otherwise, to get asynchronous support, do::

  python3 -m pip install -U akinator.py[async]

To get async support plus faster performance (via the ``aiodns`` and ``cchardet`` libraries), do::

  python3 -m pip install -U akinator.py[fast_async]

Requirements
============

- Python ≥3.5.3

- ``requests``

- ``aiohttp`` (Optional, for async)

- ``aiodns`` and ``cchardet`` (Optional, for faster performance with async)

Usually ``pip`` will handle these for you.

**************
Quick Examples
**************

Here's a quick little example of the library being used to make a simple, text-based Akinator game:

.. code-block:: python

  import akinator

  aki = akinator.Akinator()

  def main():
      q = aki.start_game()
      while aki.progression <= 85:
          a = input(q + "\n\t")
          if a == "b":
              try:
                  q = aki.back()
              except akinator.CantGoBackAnyFurther:
                  pass
          else:
              q = aki.answer(a)
      aki.win()
      correct = input(f"It's {aki.name} ({aki.description})! Was I correct?\n{aki.picture}\n\t")
      if correct.lower() == "yes" or correct.lower() == "y":
          print("Yay\n")
      else:
          print("Oof\n")

  main()

Here's the same game as above, but using the async version of the library instead:

.. code-block:: python

  from akinator.async_aki import Akinator
  import akinator
  import asyncio

  aki = Akinator()

  async def main():
      q = await aki.start_game()
      while aki.progression <= 85:
          a = input(q + "\n\t")
          if a == "b":
              try:
                  q = await aki.back()
              except akinator.CantGoBackAnyFurther:
                  pass
          else:
              q = await aki.answer(a)
      await aki.win()
      correct = input(f"It's {aki.name} ({aki.description})! Was I correct?\n{aki.picture}\n\t")
      if correct.lower() == "yes" or correct.lower() == "y":
          print("Yay\n")
      else:
          print("Oof\n")

  loop = asyncio.get_event_loop()
  loop.run_until_complete(main())
  loop.close()

*************
Documentation
*************

Because this library is relatively simple and only has a few functions to keep track of, all the documentation is going to go here in the README, instead of on a separate site like `readthedocs.io <https://readthedocs.org/>`_ or something.

The async version of this library works almost exactly the same as the regular, non-async one. Both have the same classes, names of functions, etc. Any differences will be noted.

**Version Information**::

  >>> import akinator
  >>> akinator.__version__

Alternatively, you can view the ``VERSION.txt`` file

*class* Akinator()
==================

A class that represents an Akinator game.

The first thing you want to do after calling an instance of this class is to call ``Akinator.start_game()``.

To get the regular Akinator class, make sure you've put ``import akinator`` at the top of your code. From there you can easily access the class via ``aki = akinator.Akinator()``. To get the async version of the class, make sure you have ``import akinator.async_aki`` or ``from akinator.async_aki import Akinator`` in your code and you'll be able to get the async Akinator class just as easily (Refer to the code examples above).

Functions
=========

**Note**: In the async version, all the below functions are coroutines and must be awaited

Akinator.start_game(language=None)
  Start an Akinator game. Run this function first before the others. Returns a string containing the first question

  The ``language`` parameter can be left as None for English, the default language, or it can be set to one of these (case-insensitive):

  - ``en``: English
  - ``en2``: Second English server. Use if the main one is down
  - ``ar``: Arabic
  - ``cn``: Chinese
  - ``de``: German
  - ``es``: Spanish
  - ``fr``: French
  - ``fr2``: Second French server. Use if the main one is down
  - ``il``: Hebrew
  - ``it``: Italian
  - ``jp``: Japanese
  - ``kr``: Korean
  - ``nl``: Dutch
  - ``pl``: Polish
  - ``pt``: Portuguese
  - ``ru``: Russian
  - ``tr``: Turkish

  You can also put the name of the language spelled out, like ``spanish``, ``korean``, etc.

  If you put something else entirely, then then the ``InvalidLanguageError`` exception will be raised

Akinator.answer(ans)
  Answer the current question, which you can find with ``Akinator.question``. Returns a string containing the next question

  The ``ans`` parameter must be one of these (case-insensitive):

  - ``yes`` or ``y`` or ``0`` for YES
  - ``no`` or ``n`` or ``1`` for NO
  - ``i`` or ``idk`` or ``i dont know`` or ``i don't know`` or ``2`` for I DON'T KNOW
  - ``probably`` or ``p`` or ``3`` for PROBABLY
  - ``probably not`` or ``pn`` or ``4`` for PROBABLY NOT

  If it's something else, then the ``InvalidAnswerError`` exception will be raised

Akinator.back()
  Goes back to the previous question. Returns a string containing that question

  If you're on the first question and you try to go back, the ``CantGoBackAnyFurther`` exception will be raised

Akinator.win()
  Get Aki's first guess for who the character you're thinking of is based on your answers to the questions so far.

  This function defines 3 new variables:

  - ``Akinator.name``: The name of the character Aki guessed
  - ``Akinator.description``: A short description of that character
  - ``Akinator.picture``: A direct link to an image of the character

  This function will also return a dictionary containing the above values plus some additional ones. Here's an example of what the dict looks like:

  .. code-block:: javascript

    {'absolute_picture_path': 'https://photos.clarinea.fr/BL_25_en/600/partenaire/j/2367495__1915365987.jpg',
    'corrupt': '0',
    'description': 'Entrepreneur',
    'flag_photo': 0,
    'id': '50369',
    'id_base': '2367495',
    'name': 'Elon Musk',
    'picture_path': 'partenaire/j/2367495__1915365987.jpg',
    'proba': '0.881377',
    'pseudo': 'XYZSug4r',
    'ranking': '482',
    'relative': '0',
    'valide_contrainte': '1'}

  It's recommended that you call this function when Aki's progression is above 85%. You can get his current progression via ``Akinator.progression``

Variables
=========

These variables contain important information about the Akinator game. Please don't change any of these values in your program. It'll definitely break things.

Akinator.server
  The server this Akinator game is using. Depends on what you put for the language param in ``Akinator.start_game()`` (e.g., ``"srv11.akinator.com:9152"``, ``"srv11.akinator.com:9150"``, etc.)

Akinator.session
  A number, usually in between 0 and 100, that represents the game's session

Akinator.signature
  A usually 9 or 10 digit number that represents the game's signature

Akinator.uid
  The game's UID (unique identifier) for authentication purposes

Akinator.frontaddr
  An IP address encoded in Base64; also for authentication purposes

Akinator.question
  The current question that Akinator is asking the user. Examples of questions asked by Aki include: ``Is your character's gender female?``, ``Is your character more than 40 years old?``, ``Does your character create music?``, ``Is your character real?``, ``Is your character from a TV series?``, etc.

Akinator.progression
  A floating point number that represents a percentage showing how close Aki thinks he is to guessing your character. I recommend keeping track of this value and calling ``Akinator.win()`` when it's above 85 or 90. In most cases, this is about when Aki will have it narrowed down to one choice, which will hopefully be the correct one.

Akinator.step
  An integer that tells you what question Akinator is on. This will be 0 on the first question, 1 on the second question, 2 on the third, 3 on the fourth, etc.

The first 5 variables—``server``, ``session``, ``signature``, ``uid``, and ``frontaddr``—will remain unchanged, but the last 3—``question``, ``progression``, and ``step``—will change as you go on.

**Note**: There are 3 more variables that will be defined when the function ``Akinator.win()`` is called for the first time. These variables are documented above, underneath that function in the **Functions** section

Exceptions
==========

Exceptions that are thrown by the library

InvalidAnswerError
  Raised when the user inputs an invalid answer into ``Akinator.answer(ans)``

InvalidLanguageError
  Raised when the user inputs an invalid language into ``Akinator.start_game(language=None)``

AkiConnectionFailure
  Raised if the Akinator API fails to connect for some reason. Base class for AkiTimedOut, AkiNoQuestions, AkiServerDown, and AkiTechnicalError

AkiTimedOut
  Raised if the Akinator session times out. Derived from ``AkiConnectionFailure``

AkiNoQuestions
  Raised if the Akinator API runs out of questions to ask. This will happen once ``Akinator.step`` reaches 80. Derived from ``AkiConnectionFailure``

AkiServerDown
  Raised if Akinator's servers are down for the region you're running on. If this happens, try again later or use a different language. Derived from ``AkiConnectionFailure``

AkiTechnicalError
  Raised if Aki's servers had a technical error. If this happens, try again later or use a different language. Derived from ``AkiConnectionFailure``

CantGoBackAnyFurther:
  Raised when the user is on the first question and tries to go back further by calling ``Akinator.back()``

"""""""""""""""""

.. image:: https://img.shields.io/badge/Enjoy%20this%20library%3F-Say%20Thanks!-brightgreen.svg
    :target: https://saythanks.io/to/NinjaSnail1080

.. image:: https://img.shields.io/badge/Having%20problems%3F-Issues%20Tracker-blueviolet.svg
    :target: https://github.com/NinjaSnail1080/akinator.py/issues

.. image:: https://img.shields.io/badge/License-MIT-red.svg
    :target: https://opensource.org/licenses/MIT