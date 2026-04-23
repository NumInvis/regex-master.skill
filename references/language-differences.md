# Regex Language Differences

A quick reference for regex behavior across major programming languages.

## Quick Comparison Table

| Feature | Python `re` | JavaScript `RegExp` | Java `Pattern` | C# `Regex` | Go `regexp` |
|---------|-------------|---------------------|----------------|------------|-------------|
| **String literal** | `r"..."` | `/.../` or `"..."` | `"..."` | `@"..."` or `"..."` | `` `...` `` or `"..."` |
| **Escape in string** | Raw string preferred | Regex literal: minimal | Double escape `\\d` | Verbatim: none; Normal: double | Raw string preferred |
| **Comments in pattern** | `re.VERBOSE` | ❌ No | `(?x)` / `COMMENTS` | `IgnorePatternWhitespace` | ❌ No |
| **Named capture** | `(?P<name>...)` | `(?<name>...)` `(?<name>...)` | `(?<name>...)` | `(?<name>...)` | ❌ No |
| **Numbered backref** | `\1` | `$1` (replace), `\1` (match) | `\1` | `$1` (replace), `\1` (match) | ❌ No |
| **Lookbehind** | Fixed (3.11+ variable) | Fixed (ES2018+ variable) | Fixed width only | Variable width OK | Fixed width only |
| **Atomic group** | ❌ No (emulate) | ❌ No | `(?>...)` | `(?>...)` | ❌ No |
| **Possessive quantifier** | ❌ No | ❌ No | `++`, `*+`, etc. | `++`, `*+`, etc. | ❌ No |
| **Recursive pattern** | `(?R)` `(?0)` | ❌ No | ❌ No | `(?<n>...(?&n)...)?` | ❌ No |
| **Default** | Case-sensitive | Case-sensitive | Case-sensitive | Case-sensitive | Case-sensitive |
| **Unicode aware** | `re.UNICODE` default | `u` flag default (ES6+) | Always Unicode | .NET 5+ | Always UTF-8 |
| **Global match** | `re.findall` | `g` flag | `Matcher.find()` loop | `Regex.Matches()` | `FindAllString` |

---

## Python (`re` module)

```python
import re

# Basic match
re.search(r'\d+', 'abc123')       # Match object or None
re.match(r'\d+', 'abc123')        # None (match only at start)
re.fullmatch(r'\d+', '123')       # Match object or None
re.findall(r'\d+', 'a1b2c3')     # ['1', '2', '3']
re.finditer(r'\d+', 'a1b2c3')    # Iterator of Match objects

# Substitution
re.sub(r'\d+', '#', 'a1b2')      # 'a#b#'
re.sub(r'(\d+)', r'\1\1', 'a1')  # 'a11'  (backref in replacement)

# Named groups
m = re.search(r'(?P<name>\w+)', 'hello')
m.group('name')                   # 'hello'

# Verbose mode
pattern = re.compile(r'''
    \d{3}      # area code
    -          # separator
    \d{4}      # number
''', re.VERBOSE)

# Flags
re.search('pattern', text, re.IGNORECASE | re.MULTILINE)
```

**Key gotchas:**
- Always use raw strings `r"..."` for patterns
- `re.match()` only matches at the beginning of string; use `re.search()` for anywhere
- No atomic groups; emulate with `(?=(...))\1` trick

---

## JavaScript (`RegExp`)

```javascript
// Literal
const re = /\d+/g;

// Constructor (dynamic)
const re2 = new RegExp('\\d+', 'g');

// Methods
'abc123'.match(/\d+/)       // ['123'] (first match)
'abc123'.match(/\d+/g)      // ['123'] (all matches with g flag)
'a1b2'.replace(/\d+/g, '#') // 'a#b#'
/a(\d+)/.exec('a123')      // ['a123', '123'] (captures in result)

// Named groups (ES2018+)
const m = '2024-04-23'.match(/(?<year>\d{4})-(?<month>\d{2})/);
m.groups.year   // '2024'

// test() and search()
/\d/.test('abc')            // false
'abc123'.search(/\d/)       // 3 (index of first match)
```

**Key gotchas:**
- `g` flag changes `match()` behavior: with `g`, no capture groups returned
- `RegExp` constructor: string escapes are processed twice, so `\\d` becomes `\d` in pattern
- No verbose/comment mode
- Lookbehind requires ES2018+ and fixed width in older engines

---

## Java (`java.util.regex`)

```java
import java.util.regex.*;

Pattern pattern = Pattern.compile("\\d+");
Matcher matcher = pattern.matcher("abc123");

if (matcher.find()) {
    System.out.println(matcher.group());     // "123"
}

// Named groups (Java 7+)
Pattern p = Pattern.compile("(?<name>\\w+)");
Matcher m = p.matcher("hello");
if (m.find()) {
    System.out.println(m.group("name"));     // "hello"
}

// Replacement
String result = "a1b2".replaceAll("\\d+", "#");  // "a#b#"
String result2 = "a1b2".replaceFirst("\\d+", "#"); // "a#b2"

// Flags
Pattern.compile("pattern", Pattern.CASE_INSENSITIVE | Pattern.MULTILINE);
```

**Key gotchas:**
- Double escape in strings: `"\\d"` in Java string = `\d` in regex
- `matches()` requires the ENTIRE string to match (implicit `^...$`)
- `find()` finds next occurrence anywhere
- Lookbehind must be fixed width

---

## C# / .NET (`System.Text.RegularExpressions`)

```csharp
using System.Text.RegularExpressions;

// Basic
Match m = Regex.Match("abc123", @"\d+");
if (m.Success) Console.WriteLine(m.Value);  // "123"

// Named groups
Match m2 = Regex.Match("hello", @"(?<name>\w+)");
Console.WriteLine(m2.Groups["name"].Value); // "hello"

// Multiple matches
foreach (Match match in Regex.Matches("a1b2c3", @"\d+")) {
    Console.WriteLine(match.Value);
}

// Replace
string result = Regex.Replace("a1b2", @"\d+", "#");  // "a#b#"
string result2 = Regex.Replace("a1b2", @"(\d+)", "$1$1"); // "a11b22"

// With options
Regex.IsMatch(text, @"pattern", RegexOptions.IgnoreCase | RegexOptions.Multiline);
```

**Key gotchas:**
- Verbatim strings `@"..."` are preferred; still need to escape `"` as `""`
- .NET supports variable-width lookbehind (rare and powerful)
- `RegexOptions.IgnorePatternWhitespace` enables verbose mode
- `Regex` class is static; compiled regexes can be cached with `RegexOptions.Compiled`

---

## Go (`regexp` package)

```go
package main
import "regexp"

re := regexp.MustCompile(`\d+`)

// Find
re.FindString("abc123")           // "123"
re.FindAllString("a1b2c3", -1)   // ["1" "2" "3"]
re.FindStringIndex("abc123")     // [3 6]

// Replace
re.ReplaceAllString("a1b2", "#") // "a#b#"

// Submatch
re2 := regexp.MustCompile(`(\w+):(\d+)`)
matches := re2.FindStringSubmatch("name:123")
// matches[0] = "name:123", matches[1] = "name", matches[2] = "123"
```

**Key gotchas:**
- Raw string literals `` `...` `` are preferred
- `regexp` uses RE2 syntax: no backreferences, no lookahead/lookbehind
- MustCompile panics on invalid regex; Compile returns error
- `FindAllString` second arg: `-1` means all matches, `n` means at most n

---

## Escaping Reference

| To match | Python/Go raw | Python/Go normal | JS literal | JS `new RegExp` | Java/C# normal |
|----------|--------------|------------------|------------|-----------------|----------------|
| `\d` | `r"\d"` | `"\\d"` | `/\d/` | `"\\d"` | `"\\d"` |
| `\n` | `r"\n"` | `"\n"` or `"\\n"` | `/\n/` | `"\\n"` | `"\\n"` |
| `\\` | `r"\\"` | `"\\\\"` | `/\\/` | `"\\\\"` | `"\\\\"` |
| `"` | `r'"'` | `'"'` | `/"/` | `'"'` | `"\""` |
| `\b` | `r"\b"` | `"\\b"` | `/\b/` | `"\\b"` | `"\\b"` |
| `^$.*+?{}[]\|`() | same rules | same rules | same rules | same rules | same rules |

**Rule of thumb**: Use raw/verbatim strings whenever available (Python `r"`, Go `` ` ``, C# `@"`).
