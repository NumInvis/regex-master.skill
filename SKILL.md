---
name: regex-master
description: |
  Comprehensive regex expertise for writing, explaining, and debugging regular expressions across all major programming languages.
  Use this skill whenever the user mentions regex, regular expressions, pattern matching, text extraction, text validation,
  string parsing, pattern search, or needs to match/find/replace text based on patterns.
  Also use when the user asks about escaping characters, capture groups, lookahead/lookbehind, greedy vs lazy matching,
  or regex performance issues. Trigger even if the user doesn't explicitly say "regex" — words like "pattern", "match",
  "extract emails/URLs/numbers", "validate format", or "text parsing" should activate this skill.
---

# Regex Master

A comprehensive skill for regular expression authoring, explanation, and debugging across programming languages.

## When to Use This Skill

Activate this skill for any task involving regular expressions, including but not limited to:

- **Writing regex**: Creating patterns for validation, extraction, substitution, or splitting
- **Explaining regex**: Decoding what an existing pattern does, step by step
- **Debugging regex**: Fixing patterns that don't match correctly, perform poorly, or cause catastrophic backtracking
- **Translating regex**: Converting patterns between programming languages (Python ↔ JavaScript ↔ Java ↔ C#, etc.)
- **Optimizing regex**: Improving performance, readability, or robustness of existing patterns
- **Common pattern lookup**: Retrieving battle-tested patterns for emails, URLs, phones, IDs, dates, etc.

## Core Workflows

### 1. Writing Regex (构建正则)

Before writing a pattern, gather requirements:

- What is the **input text** like? (provide examples of both matching and non-matching strings)
- What is the **desired output**? (full match, capture groups, split positions, replacement?)
- What **language/environment** will run this regex?
- Are there **performance constraints**? (large files, real-time processing)
- Should it be **strict or permissive**?

Follow these principles when authoring:

1. **Start simple, then refine.** Begin with a broad pattern, then add constraints.
2. **Prefer character classes over alternation.** `[a-z]` is cleaner than `(a|b|c|...|z)`.
3. **Be explicit about boundaries.** Use `^` `$` \b \B when appropriate to avoid partial matches.
4. **Use non-capturing groups** `(?:...)` when you don't need the group value.
5. **Prefer lazy quantifiers** `*?` `+?` `??` for text between delimiters to avoid over-matching.
6. **Avoid catastrophic backtracking.** Nested quantifiers like `(a+)+` or `(.*)*` can cause exponential time. Refactor to atomic groups or possessive quantifiers if the language supports them.
7. **Comment complex patterns.** Use the language's verbose mode (Python's `re.VERBOSE`, Java's `COMMENTS`, etc.).

Always provide:
- The regex pattern itself
- Example inputs with expected match results
- A brief explanation of how the pattern works
- Language-specific implementation code
- Edge cases that might break the pattern
- **A minimal test suite** (positive cases, negative cases, and boundary cases)

### 2. Explaining Regex (解释正则)

When asked to explain an existing pattern:

1. **Pretty-print the pattern.** Break it into logical segments with spacing/comments.
2. **Explain each segment** in plain language, from left to right.
3. **Show the execution flow** on example strings — what matches first, what backtracks, etc.
4. **Identify capture groups** by number/name and what each captures.
5. **Highlight non-obvious tricks**: lookahead/lookbehind, atomic groups, conditional patterns, recursion.
6. **Rate the pattern's quality**: Is it robust? Readable? Efficient? Suggest improvements if applicable.

Use the format:
```
Pattern: /.../

Breakdown:
  [segment] → explanation
  ...

Example execution:
  Input: "..."
  Step 1: ... matches "..."
  Step 2: ... fails, backtracks to ...
  Result: ...

Capture groups:
  Group 1: ...
  Group 2: ...
```

### 3. Debugging Regex (调试正则)

When a regex doesn't work as expected, follow this systematic approach:

1. **Reproduce the issue.** Get the exact pattern, input string, language, and actual vs expected output.
2. **Check for common mistakes:**
   - Unescaped special characters: `.` `*` `+` `?` `()` `[]` `{}` `^` `$` `\` `|` in the pattern
   - Wrong escaping rules for the language (see `references/language-differences.md`)
   - Missing anchors causing partial matches
   - Greedy quantifiers consuming too much
   - Character class negation confusion: `[^abc]` means "not a, b, or c", not "not abc"
   - Unicode/encoding mismatches
3. **Simplify the pattern.** Remove parts until it works, then add back incrementally.
4. **Use a regex debugger** or visualizer (regex101.com, debuggex.com) and recommend one.
5. **Check for backtracking issues.** If matching hangs on certain inputs, nested quantifiers are likely the culprit.
6. **Provide a fixed pattern** with test cases demonstrating correctness.

## Language-Specific Quick Reference

Read `references/language-differences.md` for detailed differences. Key reminders:

| Feature | Python `re` | JavaScript `RegExp` | Java `Pattern` | C# `Regex` |
|---------|-------------|---------------------|----------------|------------|
| Raw string | `r"..."` | `/.../` or `new RegExp("...")` | `"..."` (double escape) | `@"..."` or `"..."` |
| Verbose mode | `re.VERBOSE` | N/A | `Pattern.COMMENTS` | `RegexOptions.IgnorePatternWhitespace` |
| Named group | `(?P<name>...)` | `(?<name>...)` or `(?<name>...)` | `(?<name>...)` | `(?<name>...)` |
| Lookbehind width | Fixed only (3.11+ variable) | Fixed only (ES2018+ variable) | Fixed only | Fixed or variable |
| Atomic group | No (use `(?=...)\1` trick) | No | Yes `(?>...)` | Yes `(?>...)` |
| Default | Case-sensitive | Case-sensitive | Case-sensitive | Case-sensitive |

## Reference Files

| File | When to Read |
|------|--------------|
| `references/regex-cheatsheet.md` | Need a syntax reminder for metacharacters, quantifiers, anchors, assertions, or flags |
| `references/common-patterns.md` | Need a battle-tested pattern for common validation/extraction tasks (email, URL, phone, ID, etc.) |
| `references/language-differences.md` | Converting regex between languages, or unsure about escaping/rules in a specific language |

## Performance & Backtracking

When a regex is slow or hangs:

1. **Identify nested quantifiers.** Patterns like `(a+)+`, `(.*)*`, or `(\d+)+` on long input can cause exponential backtracking.
2. **Refactor to atomic groups or possessive quantifiers** when the engine supports them (Java/C#):
   - Bad: `(?>\d+)+` is redundant, but `(\d+)+` on `"1234567890!"` backtracks badly.
   - Better: Use a single pass or limit repetition, e.g., `\d{1,10}` instead of `(\d+)+`.
3. **Use non-capturing groups** `(?:...)` to reduce overhead when you don't need the value.
4. **Benchmark with realistic data sizes.** A fast regex on 1 KB may choke on 100 MB.

## Common Traps (Quick Reference)

| Trap | Example | Fix |
|------|---------|-----|
| Greedy over-match | `.*` in `"foo" "bar"` matches across both | Use `.*?` or `[^"]*` |
| Unescaped dot | `www.site.com` matches `wwwXsiteYcom` | Use `www\.site\.com` |
| Missing anchors | `\d+` matches `"abc123"` partially | Use `^\d+$` for full validation |
| Character class confusion | `[^abc]` means "not a, b, or c" | Use negative lookahead `(?!abc)` to exclude the sequence |
| Double escaping (Java/C#) | `"\d"` is a backspace, not a digit | Use `"\\d"` or raw/verbatim strings |
| JS `g` flag hides captures | `"a1b2".match(/(\d)/g)` → `["1","2"]` | Remove `g` to get capture groups, or use `matchAll` |

## Best Practices Summary

1. **Always test with both positive and negative examples.** A regex that matches what it should but also matches what it shouldn't is wrong.
2. **Include a minimal test suite with every regex you write.** At minimum: 2 positive cases, 2 negative cases, and 1 boundary case.
3. **Don't over-engineer email/URL regexes.** Perfect RFC-compliant regexes are unreadable and often unnecessary. Use practical approximations.
4. **Consider readability.** Complex patterns should be broken into named groups or commented verbose mode.
5. **Beware ReDoS.** Never run user-provided regexes on production servers without timeouts. Avoid nested quantifiers on untrusted input.
6. **Use online tools for debugging.** Recommend regex101.com (supports Python/JS/Java/Go/PCRE), debuggex.com (visualization), or rubular.com.
   - Paste the pattern and test cases into regex101; share the permalink so the user can interact with it.
