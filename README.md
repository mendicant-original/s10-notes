General
=======
_inspirational words from Greg on how writing functional code is only
half the battle_ __GI JOE__

We've compiled a list of the common quality issues seen during the first checkpoint and how you can work to remedy them; along with why it's important to do so. Please take your time to careful read this document and apply it to your code bases. Once you have solicite your fellow students and mentors for final feedback before asking for a formal Quality Review.

Configuration
=============
  - Don't check in passwords!
  - Provide default config files
  - Congfiguration Objects vs Configuration Globals

Style
=====
  - Follow the style guide
  - Don't checkin debugging code or commented code
  - Don't cargo cult style, think why something is done

Project Layout
==============
  - Make sure to include a Gemfile (but have it reference just the gemspec unless there is a reason to do otherwise)
  - Proper project layout what goes under bin, lib, assets (data,config), test, spec, etc

Code Structure
==============
  - Use namespaces
  - No need for empty module definition
  - Try not to put anything outside of requires in "lib/my_library.rb". Often times what we want to put there should really be extracted into it's own object.
  - Don't require using Dir globbing

Design
======
  - Don't use a singleton just because there is usually only one of something in your system.
  - Don't use class variables, ever!
  - Prefer modules inheritance to subclass inheritance

Documentation
=============
  - Provide and example of how to use your library (share with them your latest practice ruby that shows how to build good examples)
  - Replace the instructions README with one describing your project

Contributing to OSS
===================
  - Don't introduce new testing/environment dependencies within functional commits to someone else's project (or really, they should always be isolated)