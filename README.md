[![Build Status](https://travis-ci.org/asottile/dumbconf.svg?branch=master)](https://travis-ci.org/asottile/dumbconf)
[![Coverage Status](https://coveralls.io/repos/github/asottile/dumbconf/badge.svg?branch=master)](https://coveralls.io/github/asottile/dumbconf?branch=master)

# dumbconf - A WIP spec

Goal: define a "simple" configuration language (yet another) which is easy to
write but does not give too much freedom.  The configuration language must
support a json-like syntax but also be able to load "prettier" structures
similar to yaml.
The language will be strict in its indentation and form to encourage
uniformity.

The python implementation intends to provide the following things:
- A `load`, `loads`, `dump`, `dumps` interface similar to `json`
- The ability to retrieve a mutable ast representation which can be used for
  automatic refactoring of dumbconf files

## Encoding

dumbconf files are invariantly UTF-8 encoded.

## Comments
- Empty lines are ignored
- Comments start with a `#` character followed by a space and a comment
- Inline comments must have a whitespace character before and after the `#`
- (Suggested) It is suggested to put two space characters before the `#` as in
  PEP8
```yaml
# A comment
true  # an inline comment
```

## Primitives

### Quoted strings
- Both single and quoted strings are appropriate
- Escape sequences in quoted strings will be interpreted according to python
  rules.
```yaml
['foo', "foo", 'foo\'bar', "foo\"bar"]
```

### Bare word keys
- Maps may contain bare words keys
- Keys which would otherwise be interpreted as another value must be quoted
```yaml
{
    im_a_bare_word_key: 'value',
    'true': 'indeed, my key needed quoting',
}
```

### Boolean
- There are 2 tokens interpreted as booleans
```yaml
[true, false]
```

### Integers

There are four supported forms of integers:

```yaml
[
    # hexadecimal
    0xdeadBEEF,
    # binary
    0b10101010,
    # octal
    0o755,
    # decimal
    -1234,
]
```

### Floats

```yaml
[
    # integers with exponents
    0e5, 1e-10,
    # numbers with a decimal point
    0., .5, 1.5,
    # numbers with a decimal point and exponent
    6.02E23,
]
```

### null
- There is one toke interpreted as null
```yaml
[null]
```

## Lists

### Inline list

```yaml
[]
```
```yaml
['value']
```
```yaml
['value', 'value', 'value']
```

### Multiline list

- Trailing commas are *required*

```yaml
[
    'value',
    'value',
    'value',
]
```

- Closing bracket matches starting indentation
- For instance in a mapping type:
```yaml
{
    key: [
        'value',
        'value',
        'value',
    ],
}
```

### Hybrid multiline list

- Values may appear inline (to improve readability)

```yaml
[
    'value', 'value', 'value',
    'value', 'value',
]
```

## Maps

- Keys may be any of the primitive types

### Inline map

```yaml
{}
```
```yaml
{key: 'value'}
```
```yaml
{key: 'value', other_key: 'other value'}
```

### Multiline map

- Trailing commas are *required*

```yaml
{
    key1: 'value1',
    key2: 'value2',
    key3: 'value3',
}
```

- Closing bracket matches starting indentation
- For instance in a mapping type:
```yaml
{
    key: {
        key1: 'value1',
        key2: 'value2',
        key3: 'value3',
    },
}
```

### Hybrid multiline map

- Pairs may appear inline (to improve readability)

```yaml
{
    key1: 'value1', key2: 'value2',
    key3: 'value3', key4: 'value4',
}
```

### Top level maps

- A top level map may omit brackets and commas

```yaml
True: ['I', 'am', 'a', 'map']
False: ['I', 'have', 'brackets']
```

## Complete syntax example:

```yaml
# A comment followed by a blank line

scalars: {
    bool_values: [true, false],  # An inline comment
    null_value: [null],
    strings: ["double quoted", 'single quoted', 'unicode: \u2603'],
    ints: [0xDEADBEEF, 0b101010, 0o755, 0, 1234],
    floats: [1., 1e5, .142857, 6.02e23],
}

'a json style map': {"key": "value", "other key": "other value"}
'a json style list': ["i", "am", "a", "list"]

'a python style map': {'key': 'value', 'other key': 'other value'}
'a python style list': ['i', 'am', 'a', 'list']

'a bare words map': {key: 'value', other_key: 'other value'}
```
