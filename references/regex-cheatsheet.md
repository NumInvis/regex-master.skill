# Regex Syntax Cheatsheet

## Special Characters (Metacharacters)

| Char | Meaning | Example |
|------|---------|---------|
| `.` | Any character except newline | `a.b` matches "acb", "a b" |
| `\d` | Digit `[0-9]` | `\d+` matches "123" |
| `\D` | Non-digit `[^0-9]` | `\D+` matches "abc" |
| `\w` | Word char `[a-zA-Z0-9_]` | `\w+` matches "hello_world" |
| `\W` | Non-word char | `\W+` matches "!@#" |
| `\s` | Whitespace `[ \t\n\r\f\v]` | `\s+` matches spaces/tabs/newlines |
| `\S` | Non-whitespace | `\S+` matches "word" |
| `\b` | Word boundary | `\bword\b` matches "word" not "sword" |
| `\B` | Non-word boundary | `\Bword` matches "sword" |
| `\n` | Newline | — |
| `\t` | Tab | — |
| `\r` | Carriage return | — |
| `\\` | Literal backslash | — |

## Quantifiers

| Quantifier | Meaning | Greedy | Lazy |
|-----------|---------|--------|------|
| `?` | 0 or 1 | `?` | `??` |
| `*` | 0 or more | `*` | `*?` |
| `+` | 1 or more | `+` | `+?` |
| `{n}` | Exactly n | — | — |
| `{n,}` | n or more | `{n,}` | `{n,}?` |
| `{n,m}` | Between n and m | `{n,m}` | `{n,m}?` |

**Greedy vs Lazy**: Greedy matches as much as possible; Lazy matches as little as possible.
- `".*"` on `"a" "b"` matches `"a" "b"` (greedy)
- `".*?"` on `"a" "b"` matches `"a"` (lazy)

## Anchors

| Anchor | Meaning |
|--------|---------|
| `^` | Start of string (or line with multiline flag) |
| `$` | End of string (or line with multiline flag) |
| `\A` | Start of string (never line) |
| `\Z` | End of string (before final newline) |
| `\z` | Absolute end of string |
| `\b` | Word boundary |
| `\B` | Non-word boundary |

## Character Classes

| Syntax | Meaning |
|--------|---------|
| `[abc]` | Any of a, b, or c |
| `[^abc]` | None of a, b, or c |
| `[a-z]` | Any lowercase letter |
| `[A-Z]` | Any uppercase letter |
| `[0-9]` | Any digit |
| `[a-zA-Z0-9]` | Any alphanumeric |
| `[\-\]]` | Literal hyphen or closing bracket (escape inside class) |

## Groups and Capturing

| Syntax | Meaning |
|--------|---------|
| `(...)` | Capturing group |
| `(?:...)` | Non-capturing group |
| `(?<name>...)` | Named capturing group (JS/Java/C#) |
| `(?P<name>...)` | Named capturing group (Python) |
| `\1` `\2` | Backreference to capture group 1, 2 |
| `\k<name>` | Backreference to named group |

## Lookaround Assertions

| Syntax | Meaning | Type |
|--------|---------|------|
| `(?=...)` | Positive lookahead | Ahead |
| `(?!...)` | Negative lookahead | Ahead |
| `(?<=...)` | Positive lookbehind | Behind |
| `(?<!...)` | Negative lookbehind | Behind |

**Important**: Lookbehind often requires fixed-width in many engines (Python <3.11, Java, JS pre-ES2018).

## Alternation and Logic

| Syntax | Meaning |
|--------|---------|
| `\|` | OR: `cat\|dog` matches "cat" or "dog" |
| `(?(cond)yes\|no)` | Conditional pattern (PCRE, .NET) |

## Modifiers / Flags

| Flag | Python | JavaScript | Java | C# | Meaning |
|------|--------|------------|------|-----|---------|
| Case-insensitive | `re.I` | `i` | `(?i)` | `IgnoreCase` | `i` |
| Multiline | `re.M` | `m` | `(?m)` | `Multiline` | `^$` match lines |
| Dotall | `re.S` | `s` | `(?s)` | `Singleline` | `.` matches newline |
| Verbose | `re.X` | N/A | `(?x)` | `IgnorePatternWhitespace` | Allow comments/spaces |
| Unicode | `re.U` | `u` | Always | N/A | Unicode-aware `\w\d\s` |
| Global | N/A | `g` | N/A | N/A | Find all matches (JS) |
| Sticky | N/A | `y` | N/A | N/A | Match from `lastIndex` (JS) |

## Common Escape Sequences

| To Match | Pattern |
|----------|---------|
| `.` | `\.` |
| `*` | `\*` |
| `+` | `\+` |
| `?` | `\?` |
| `^` | `\^` |
| `$` | `\$` |
| `(` `)` | `\(` `\)` |
| `[` `]` | `\[` `\]` |
| `{` `}` | `\{` `\}` |
| `\` | `\\` |
| `\|` | `\|` |
| `/` | `\/` (only in JS regex literals) |
