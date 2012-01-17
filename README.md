# 1. General
  - Stress that quality can sometimes be harder than writing strictly functional code

# 2. Configuration
  - Don't check in passwords! Along with this, provide default config files

# 3. Basic Style
  - Follow the style guide
  - Don't checkin commented out debugging code

# 4. Project Layout
  - Make sure to include a Gemfile (but have it reference just the gemspec unless there is a reason to do otherwise)
  - Proper project layout what goes under bin, lib, assets (data,config), test, spec, etc

# 5. Code Structure
  - Use namespaces
  - No need for empty module definition
  - Try not to put anything outside of requires in "lib/my_library.rb". Often times what we want to put there should really be extracted into it's own object.
  - Don't require using Dir globbing

# 6. Design
  - Don't use a singleton just because there is usually only one of something in your system.
  - Don't use class variables, ever!

# 7. Documentation
  - Provide and example of how to use your library (share with them your latest practice ruby that shows how to build good examples)
  - Replace the instructions README with one describing your project

# 8. Contributing to OSS
  - Don't introduce new testing/environment dependencies within functional commits to someone else's project (or really, they should always be isolated)
