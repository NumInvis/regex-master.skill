# Common Regex Patterns

> These are practical, battle-tested patterns. They prioritize readability and correct real-world matching over strict RFC compliance unless noted.

## Email Address

```regex
^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$
```

- **Notes**: Practical but not RFC 5322 compliant. Allows most real-world emails.
- **Stricter**: `^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$`

## URLs

```regex
https?:\/\/(?:[-\w.])+(?:\:[0-9]+)?(?:\/(?:[\w/_.])*(?:\?(?:[\w&=%.])*)?(?:\#(?:[\w.])*)?)?
```

- **Notes**: Matches HTTP/HTTPS URLs. Does not handle all edge cases (uncommon TLDs, auth info, etc.).
- **Simpler**: `https?:\/\/[^\s]+`

## IP Addresses

### IPv4
```regex
^(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$
```

### IPv6 (simplified)
```regex
^(?:[0-9a-fA-F]{1,4}:){7}[0-9a-fA-F]{1,4}$
```

## Phone Numbers

### China Mobile
```regex
^1[3-9]\d{9}$
```

### China ID Card (18 digits)
```regex
^[1-9]\d{5}(?:18|19|20)\d{2}(?:0[1-9]|1[0-2])(?:0[1-9]|[12]\d|3[01])\d{3}[\dXx]$
```
- **Note**: This validates format only. The last character is a checksum (ISO 7064:1983, MOD 11-2) that can be computed separately for full validation.

## Dates and Times

### ISO 8601 Date (YYYY-MM-DD)
```regex
^\d{4}-(?:0[1-9]|1[0-2])-(?:0[1-9]|[12]\d|3[01])$
```

### DateTime (YYYY-MM-DD HH:MM:SS)
```regex
^\d{4}-(?:0[1-9]|1[0-2])-(?:0[1-9]|[12]\d|3[01]) (?:[01]\d|2[0-3]):[0-5]\d:[0-5]\d$
```

## Text Processing

### Markdown Link
```regex
\[([^\]]+)\]\(([^\)]+)\)
```
- Group 1: link text
- Group 2: URL

### HTML Tag (simple, NOT for parsing)
```regex
<([a-zA-Z][a-zA-Z0-9]*)\b[^>]*>(.*?)</\1>
```
- **Warning**: Do not use regex to parse arbitrary HTML. Use a proper parser for production HTML processing.

### UUID
```regex
^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[1-5][0-9a-fA-F]{3}-[89abAB][0-9a-fA-F]{3}-[0-9a-fA-F]{12}$
```

## Password Validation Patterns

### Strong Password (8+, upper, lower, digit, special)
```regex
^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$
```

## Log Parsing Patterns

### Common Log Format (Apache/Nginx)
```regex
^(\S+) (\S+) (\S+) \[([\w:/]+\s[+\-]\d{4})\] "(\S+) (\S+) (\S+)" (\d{3}) (\d+|-)
```

## Notes on Using These Patterns

1. **Remove anchors** `^` `$` if you need to find matches within larger text.
2. **Adjust for your regex engine**: Some syntax (like named groups or lookbehind) varies by language.
3. **Test thoroughly**: These patterns handle common cases but may need tweaking for your specific data.
4. **Unicode**: Add the Unicode flag/modifier if your data contains non-ASCII characters.
