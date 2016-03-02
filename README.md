## unicode/display_width [<img src="https://travis-ci.org/janlelis/unicode-display_width.png" />](https://travis-ci.org/janlelis/unicode-display_width)

Determines the monospace display width of a string in Ruby. Implementation based on [EastAsianWidth.txt](http://www.unicode.org/Public/UNIDATA/EastAsianWidth.txt) and other data, 100% in Ruby. You can also use [wcswidth-ruby](https://github.com/janlelis/wcswidth-ruby) for this purpose, but it is less often updated by OS vendors, so results may differ.

## Introduction to Character Widths

Guesing the correct space a character will consume on terminals is not easy. There is no single standard. Most implementations combine data from [East Asian Width](http://www.unicode.org/reports/tr11/), some [General Categories](https://en.wikipedia.org/wiki/Unicode_character_property#General_Category) and hand-picked adjustments.

### How this Library Handles Widths

As of version 1.0.0. Please expect changes to this algorithm with every MINOR version update ('1.X.0')

Further above means higher precedence:

Width  | Characters                   | Comment
-------|------------------------------|--------------------------------------------------
X      | (user defined)               | Overwrites any other values
-1     | "\b"                         | Backspace (total width never below 0)
0      | "\0", "\x05", "\a", "\n", "\v", "\f", "\r", "\x0E", "\x0F" | [C0 control codes](https://en.wikipedia.org/wiki/C0_and_C1_control_codes#C0_.28ASCII_and_derivatives.29) that do not change horizontal width
1      | "\u{0x00AD}"                 | SOFT HYPHEN
2      | "\u{0x2E3A}"                 | TWO-EM DASH
3      | "\u{0x2E3B}"                 | THREE-EM DASH
0      | General Categories: Mn, Me, Cf (non-arabic) | Excludes ARABIC format characters
0      | "\u{0x1160}".."\u{0x11FF}"   | HANGUL JUNGSEONG
2      | East Asian Width: F, W       | Full-width characters
1 or 2 | East Asian Width: A          | Ambiguous characters, user defined, by default: 1
1      | All other                    | -

## Install

Install the gem with:

    gem install unicode-display_width

Or add to your Gemfile:

    gem 'unicode-display_width'

## Usage

```ruby
require 'unicode/display_width'

Unicode::DisplayWidth.of("⚀") # => 1
Unicode::DisplayWidth.of("一") # => 2
```

### Ambiguous Characters

The second parameter defines the value returned by characterrs defined as ambiguous:

```ruby
Unicode::DisplayWidth.of("·", 1) # => 1
Unicode::DisplayWidth.of("·", 2) # => 2
```

### Custom Overwrites

You can overwrite how to handle specific code points by passing a hash (or even a proc) as third parameter:

```ruby
Unicode::DisplayWidth.of("a\tb", 1, 0x09 => 10)) # => 12
```

### Usage with String Extension

Activated by default. Will be deactivated in version 2.0:

```ruby
require 'unicode/display_width/string_ext'

"⚀".display_width #=> 1
'一'.display_width #=> 2
```

You can actively opt-out from the string extension with: `require 'unicode/display_width/no_string_ext'`

### Usage From the CLI

If you are not a Ruby developer, but want to use the library:

```
$ gem install unicode-display_width
$ ruby -r unicode/display_width -e 'puts Unicode::DisplayWidth.of $*[0]' -- "一"
```
Replace "一" with your actual String to measure

## Other Implementations & Discussion

- Python: https://github.com/jquast/wcwidth
- JavaScript: https://github.com/mycoboco/wcwidth.js
- C: http://www.cl.cam.ac.uk/~mgk25/ucs/wcwidth.c
- C for Julia: https://github.com/JuliaLang/utf8proc/issues/2

## Copyright & Info

- Copyright (c) 2011, 2015-2016 Jan Lelis, http://janlelis.com, released under the MIT
license
- Early versions based on runpaint's unicode-data interface: Copyright (c) 2009 Run Paint Run Run
- Unicode data: http://www.unicode.org/copyright.html#Exhibit1
