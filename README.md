# Hyperglot – detecting language support in fonts

**Warning: this work is still in progress and provided AS IS.**

The Hyperglot database and tools try to answer the seemingly simple question of language support in fonts; when can one use a font A to set texts in a language B? We took a pragmatic approach and reduced the answer to finding a standard character set for each orthography used by a language. We also added a brief design note to alert users regarding shaping and positioning requirements that go beyond Unicode character code points. The database currently contains information for over 600 languages.

The Hyperglot database is a work in progress, the validity of the language data varies, but it is clearly marked for each language (some are considered drafts, some have been verified etc.). We have checked the information from various online and offline sources and we are committed to keep on improving. But we will also need help from the respective language users. Admittedly, mapping all languages of the world is beyond our capacity. So, if you see an issue or a language missing, please get in touch and [contribute](README_database.md).

## How to use

There are several ways how to use the database:

- Hyperglot web app at http://hyperglot.rosettatype.com
- command-line tool (`pip install hyperglot`, see usage notes below)
- python packagage (`pip install hyperglot`)
- access the YAML file with the database directly ([database README](README_database.md))

## It is complicated (disclaimer)

A few notes to illustrate why the question of language support is complicated:

1. a single language can be written using different orthographies in one or more scripts,
2. languages are not isolated, there are loan words, names etc. from other languages, thus finding what is an essential character set for a language is largerly a question of convention,
3. what one person considers a dialect, is a language for someone else,
4. different kinds of texts require different vocabulary and hence different characters.

It is important to note that **there is more to language support in fonts than supporting a set of code points**. A font needs to include glyphs with acceptable/readable shapes of the characters for a particular language. Sometimes there are regional or language variations for the same code point which means that different languages pose different requirements on the shape of a character, but identical requirements on the code point of the character. Moreover, glyphs have to interact as expected by the convention of a particular script/orthography. For example, some languages/scripts require (or strongly expect) certain glyph combinations to form ligatures or some glyph combinations require additional spacing correction (kerning) to prevent clashes or gaps. Thus, the report produced by the Hyperglot tools should only be used to detect whether a font can be considered for use with a particular language. It does not say anything about the quality of the design.

## Detecting support

Characters are represented using [Unicode](https://unicode.org) code points in digital texts, e.g. the Latin-script letter `a` has a code point `U+0061`. Digital OpenType fonts map these code points to glyphs, visual representations of characters. In order to find whether one can use a font for texts in a particular language, one needs to know which character code points are required for the language. This is what the Hyperglot database is for.

1. A list of codepoints is obtained from a font.
2. The database can be accessed in two modes:
  - By **default** combinations of a base character with marks are required as single code point where this exists (e.g. encoded `ä`), codepoints for base characters and combining mark characters (e.g. `a` and combining `¨`) from these combinations are also required.
  - Using the `decomposed` flag fonts are required to contain the base character and combining marks for a language (e.g. languages with `ä` will match for fonts that only have `a` and combining `¨` but not `ä` as  encoded glyph).
3. Specified `validity` level is used to filter out language entries according to a user’s preference.
4. If requested, `base` and `aux` (auxiliary) lists of codepoints are combined to achieve more strict criteria by using the `--support` option.
5. When detecting language support (default), code points from **all** primary orthographies for a given language are combined (need to be included to detect support of the language). Orthographies with `deprecated` and `secondary` status are ignored.
6. When detecting orthography support, use `--include-all-orthographies`, all orthographies for a given language are checked individually. Orthographies with `secondary` status are included. Orthographies with `deprecated` are ignored.
7. If the list of code points in the font includes all code points from the list of codepoints from points 5 or 6, the font is considered to support this language/orthography. In listings these are grouped by scripts.

The language-orthography combination means that a language that has multiple orthographies using different scripts (e.g., Serbian or Japanese) is listed under all of these scripts in the tools’ output.

## Command-line tool

A simple CLI tool is provided to output language support data for a passed in font file.

### Installation

You will need to have Python 3 installed. Install via pip:

```shell
pip install hyperglot
```

### Usage

```shell
hyperglot path/to/font.otf
```

or to check several fonts at once, or their combined coverage (with `-m union`)

```shell
hyperglot path/to/font.otf path/to/anotherfont.otf ...
```

**Additional options**:

- `-s, --support`: Specify what level of support to check against (currently options are "base" (default if omitted) or "aux")
- `-d, --decomposed`: Flag to signal a font should be considered supporting a language as long as it has all base glyphs and marks to write a language - by default also encoded precomposed glyphs are required (default is False)
- `-a, --autonyms`: Output the language names in their native language and script
- `-u, --users`: Also output language user count (where available)
- `-o, --output`: Supply a file path to write the output to, in yaml format. For a single input font this will be a subset of the Hyperglot database with the languages and orthographies that the font supports. If several fonts are provided the yaml file will have a top level dict key for each file. If the `-m` option is provided the yaml file will contain the specific intersection or union result
- `-m, --mode`: How to process input if several files are provided (currently options are "individual", "union" and "intersection")
- `--include-all-orthographies`: Check all orthographies of a language, not just its primary one(s)
- `--validity`: Specifiy to filter by the level of validity of the language data (default is "preliminary")
- `--include-historical`: Option to include languages and orthographies marked as historical (default is False)
- `--include-constructed`: Option to include languages and orthographies that are marked as constructed (default is False)
- `--strict-iso`: Display language names and macrolanguage data strictly according to ISO (default is False)
- `-v, --verbose`: More logging information (default is False)
- `-V, --version`: Print the version hyperglot version number (default is False)

Installing the pip package also installed the `hyperglot-validate` and `hyperglot-save` commands, which allow checking and saving the yaml data in a structured and compatible way.

## Database and contributing

The data structure is described in a separate file together with guidelines for contributing.

[Database and contributing](README_database.md)

Contributions are most welcome! So is feedback shared via Issues or email.

## Contributors

The Hyperglot database and tools were originally developed by [Rosetta](http://rosettatype.com), world typography specialists, publishers, and makers of original fonts addressing the needs of global typography. Our goal is to enable people to read better in their native languages.

- David Březina  
- Sérgio Martins  
- Johannes Neumeier
- Toshi Omagari

[Full list of contributors](CONTRIBUTORS.txt)
