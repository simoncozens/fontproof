# FontProof - a font design testing class for SILE

FontProof enables you to produce PDF font test documents without fiddling with InDesign or other manual page layout or word processing programs. You can apply one of the predesigned test documents (to be added later) or use FontProof to build your own custom font test document.

_Note that FontProof is very much a work-in-progress, so there's a lot that doesn't work yet, or doesn't work very elegantly. Use at your own risk, and help us make it better._

## Installation

FontProof requires [SILE](https://github.com/simoncozens/sile). You'll need to install SILE according to that package's instructions, and then verify that it works by compiling the _examples/test.sil_ document.

_Note that SILE is changing rapidly. The current class was developed for an unreleased version (1.9.4). I will try to keep the class up to date for future released versions._

To install FontProof download this project (or better yet, _git clone_) into any folder on your drive. From within that directory you should then be able to compile the basic FontProof test doc:

    $ sile fpTest.sil

As long as you run all your SILE files from within the original installation folder this is all you need to do. If you wish to be able to run FontProof from any location, then:

- Find your SILE installation directory,
- Copy the file _classes/fontproof.lua_ into the _sile/classes_ folder, and
- Copy the folder _packages/fontproofsupport_ into your installation _sile/packages_ folder.

## Using the templates

To use one of the provided templates, open it in a text editor (maybe make a copy of it first) and change the `\setTestFont` line to point to your font. The font doesn't even have to be installed. If it is installed you can also specify font by family name. Either way you can set the default size:

```
\setTestFont[family="Georgia", size=13pt]
```

Then run SILE on the file with `sile filename`.

At this point there is only one template to use - _fpFull.sil_ - but more will follow. That template will show you almost all that FontProof can do. SILE itself is capable of far, far, more, and you're very welcome to play around with it.

## Adding or modifying tests

Each template can contain a full selection of tests. Each test is specified using a command in this general format:

```
\commandname[set of parameters]{text to be typeset}
```

Some tests may have only parameters, or only text, or neither, as in this example that is natively supported within SILE itself:

```
\pangrams
```

More details on running commands in SILE can be found in the SILE documentation.

## Headings and sections

By default, the running head lists the filename, font (family or filename), date, and time. The content is not (currently) configurable, but you can set the font and size with the `\setRunHeadStyle` command. Note that this has no relation to the test font.

```
%\setRunHeadStyle[filename="packages/fontproofsupport/Lato2OFL/Lato-Bold.ttf", size="12pt"]
```

To add a section and subsection headings:

```
\section{Heading text}
\subsection{Heading text}
```

The style of these is not currently configurable, but you can manually tweak them for all FontProof docs by changing the settings in _classes/fontproof.lua_.

## Test types

The following kinds of tests are supported, and are illustrated in _fpFull.sil_ :

- Simple test text
- Waterfall (a text repeated in multiple sizes)
- Patterns (where a text is repeated with one or more substituted characters)
- Lorem ipsum
- Pangrams
- Full glyph repertoire

Details on the commands used to specify these are below.

## Contributing to the project

FontProof is Copyright © 2016 [SIL International](http://www.sil.org) and licensed under the [MIT license](http://en.wikipedia.org/wiki/MIT_License). BTW - Despite the name similarity, SILE itself is not developed by SIL International, though we like the project very much. You're very welcome to contribute to both FontProof and SILE.

---

# Proof Tests and Options

## Commands provided by FontProof

These commands are currently supported.

#### __\proof[]{}__

This is the main FontProof command, and can be used to set both simple test texts and waterfalls. Optional parameters include font (by family or filename) and size(s). You can also set a test heading here rather than using a separate `\section` command.

```
\proof{This is basic proof text using the test font}

\proof[family="Gentium Plus",heading=A basic family test]{This is basic text with a font defined by family}

\proof[size="10pt",filename="packages/fontproofsupport/Lato2OFL/Lato-Light.ttf",heading=A basic filename test]{This is another basic text with common parameters}

\proof[size="10pt,11pt,12pt,16pt"]{This is basic proof text in a range of sizes}

\proof[size="10,11,12,13,20"]{This is basic proof text in a range of sizes with numbers only}
```

#### __\pattern[]{}__

This produces multiple copies of a text with a range of characters substituted in key places. These are sometimes referred to as 'frame tests'. Traditionally a common test of this type was the 'hobonop' test. A 'hobonop' for the letters a b c d would look like:

_haoabaoanaoap hbobbbobnbobp hcocbcocncocp hdodbdodndodp_

The command to produce this would be:

```
\pattern[chars="@",reps="abcd"]{h@o@b@o@n@o@p}
```

This defines a placeholder character, then the set of letters used to replace it, then the text pattern containing the placeholder(s).

You can also use multiple placeholders to create all combinations, as in:

_ooaxoo oobxoo oocxoo_   
_ooayoo oobyoo oocyoo_

The command for this would be: (Be sure to follow the syntax of this exactly, or you may get very confusing errors!)

```
\pattern[chars="@,#",reps="abc,xy"]{oo@#oo}
```

There are some characters that are special to SILE and Lua and shouldn't be used as _chars_. (A list of suggested safe ones would be a good addition here!)

There is one more optional parameter to the `\pattern` command:

- _format = "table"_ (the default) will typeset patterns with multiple placeholders grouped into one paragraph per second placeholder replacement.
- _format = "list"_ will typeset each pattern in a separate line (paragraph).
- _format = "para"_ will group all results into a single paragraph.

It's easier to demonstrate this than to explain. First the formatted example, then the command to produce it:

_ooaxoo oobxoo oocxoo_   
_ooayoo oobyoo oocyoo_

```
\pattern[chars="@,#",reps="abc,xy",format="table"]{oo@#oo}
```
_ooaxoo_   
_oobxoo_   
_oocxoo_   
_ooayoo_   
_oobyoo_   
_oocyoo_

```
\pattern[chars="@,#",reps="abc,xy",format="list"]{oo@#oo}
```
_ooaxoo oobxoo oocxoo ooayoo oobyoo oocyoo_

```
\pattern[chars="@,#",reps="abc,xy",format="para"]{oo@#oo}
```

#### __\patterngroup[]{}__

This is cool! Say that you have a group of letters that you want to use in multiple pattern tests, but don't want to have to define them over and over again. You can define these as a pattern group, with a specific name. Note that you have to define these groups in your document before you refer to them. Also note that this command doesn't really produce anything on the page by itself. It's only useful for later `\pattern` commands.

```
\patterngroup[name="vowels"]{aeiou}
```

To refer to this in a `\pattern` command prefix the group name with "group_" and use in the `reps` parameter. For example:

```
\pattern[chars="@,#",reps="group_vowels,xy",format="table"]{oo@#oo}
```

## Commands provided natively by SILE

These work in SILE even without FontProof, although you would then need to load them with `\script[src=packages/specimen]`.

#### __\pangrams__

This produces a set of predefined pangrams.

#### __\lorem[words=#]__

This produces garbage _lorem ipsum_ text, with the number of words indicated.

#### __\repertoire__

This produces a nicely arranged table of all the glyphs in the font.

## Future features

Here are some commands we hope to someday support, with suggested command formats. Some may never be implemented. Suggestions welcome!

#### Enhancements to __\proof__

We'd love to see even more potential parameters to `\proof`:

```
\proof[
  size = "10, 11/13, 12/16",
  features = "Ligatures=Rare, Ligatures=Discretionary"
  featuresraw = "+dlig,+hlig",
  language = "grk",
  direction = "rtl",
  columns = 3,
  lines = 12,
  color = #999999,
  shaper = "OT"
]{Text}
```

#### __\adhesion__ or __\hamburgerfonts__

These would use dictionaries to produce garbage text made up of real words, similar to `\lorem`. However the words would only contain letters from a defined set. Ideally, it could look in to the font, see what letters are there, and produce words that use them.

#### More preset texts in different languages

These would be like `\pangrams` but be configurable by language. They would also contain pages and pages of real (public domain) texts.

#### Tests for numbers and punctuation

`\pattern[chars="@",reps="0123456789",format="list"]{0@1@2@3@4@5@6@7@8@9@0}` is already useful. `\pi[digits=100]` would be fun.

#### Support for multiple fonts with a single test type

Oooooh!

#### _What else would you like?_