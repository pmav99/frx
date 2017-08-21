# fxr

[![Travis](https://travis-ci.org/pmav99/fxr.svg?branch=master)](https://travis-ci.org/pmav99/fxr)
[![coveralls](https://coveralls.io/repos/pmav99/fxr/badge.svg?branch=master&service=github)](https://coveralls.io/r/pmav99/fxr)

`fxr` (pronounced "fixer") is a CLI utility that can be used to find matching patterns in text files
and:

* append/prepend lines
* delete lines before/after the matching line.
* replace text

In other words, you can consider it as a replacement for `ag`, `xargs` and `sed` (with a nicer API!)

## Rationale

There were two compelling arguments that made me write this:

1. `sed` regex engine is quite limited. E.g. there is no support for look-aheads and other more
   advanced features
   ([link](https://www.gnu.org/software/sed/manual/html_node/Regular-Expressions.html)).

2. The API for combining `ag`, `sed` and `xargs` is clunky at best (e.g. repeating `<pattern>` both
   in `ag` and `sed` etc). E.g.:
   `ag <pattern> -l | xargs sed -i 's/<pattern>/<replacement>/g'`

## Installation

You can install `fxr` from pypi:

`pip install fxr`

Current version is `0.1.0`:

## Modes of operation

### `fxr add`

In this mode you search for lines matching `<pattern>` and you append/prepend text to them.

```
fxr add <pattern> <added_text>              # Appends text to lines matching pattern
fxr add --prepend <pattern> <added_text>    # Prepends text to lines matching pattern
```

### `fxr delete`

In this mode you can:

1. delete N lines preceding the matching line
2. delete M lines following the matching line
3. delete the matching line itself
4. or any combination of the above!

E.g. to delete 3 lines before the line matching pattern, 2 lines after it and the matching line
itself:

```
fxr delete --before 3 --after 2 --include_match <pattern>
```

### `fxr replace`

In this mode you can replace text on a single line. This is more or less equivalent to:

```
ag <pattern> -l | xargs sed -i 's/<pattern>/<replacement>/g'
```

You can use it like this:

```
fxr replace <pattern> <replacement>
```

## Common interface

All the above subcommands share the following flags/arguments:

* `--literal`: When you set this flag, the pattern will not be parsed as a regex.
* `--raise-if-no-change`: When you set this flag, an exception will be raised if there were no
  changes in the file.
* `--single <filename>`: When you specify this argument, fxr will try to do its magic on the
  specified file. I.e. `ag` will not be used.
* `--search_prog`: If you don't like `ag` you can specify  an alternate program (e.g. `rg`).
* `--search_args`: You can specify additional arguments for the search program.

## Requirements

You need Python `2.7+/3.3+` and a search program like
[`ag`](https://github.com/ggreer/the_silver_searcher).  If you wish to use a different program that
`ag` you can do so by using the appropriate `CLI` argument.

## Notes

### Performance

The script has not been written with performance in mind.  Since the search for matching files is
being done using `ag`, performance shouldn't be too bad, but keep in mind that the main use case
is to make changes to source code and configuration files; not a few gigabytes CSV file.
