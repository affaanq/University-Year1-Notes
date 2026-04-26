# JavaScript Regular Expressions & Project Audits

## Part 1: The Core Reference (Deep Dive)

### 1. The RegExp Engine & Creation

- **Definition:** A pattern matching engine. In JS, it's an object.
    
- **Creation:**
    
    - _Literal:_ `const re = /ab+c/i;` (Compiled at script load. Faster. Use when pattern is constant.)
        
    - _Constructor:_ `const re = new RegExp("ab+c", "i");` (Compiled at runtime. Use when pattern comes from dynamic input, like a user typing.)

### 2. Critical Methods 

- **`exec():**
    
    - _Why you need it:_ Unlike `match()`, `exec()` works with the `g` flag to iterate over matches one by one, keeping state via `lastIndex`. It gives you the full match details (index, input, groups) for _every_ match in a loop.
        
    - _Usage:_ `while ((match = regex.exec(str)) !== null) { ... }`
        
- **`search()`:**
    
    - _Why you need it:_ fast way to find the _index_ of the first match. Returns `-1` if failed. Like `indexOf` but for patterns.
        
- **`split()`:**
    
    - _Why you need it:_ Breaks a string into an array based on a pattern.
        
    - _Example:_ `str.split(/\s*,\s*/)` (Split by comma, ignoring surrounding whitespace).
        

#  Revision of Concepts

## 1. The Core Methods (The Engine)

### `test()`

- **Definition:** The most lightweight method. It simply asks "Does this pattern exist inside this string?"
    
- **Returns:** `Boolean` (`true` or `false`).
    
- **Deep Dive:** Use this when you don't care _what_ the match is, only _that_ it exists. It is generally faster than `match()` because it stops scanning as soon as it finds a hit.
    
- **Gotcha:** If you use the `g` (global) flag, `test()` becomes "stateful." It remembers the `lastIndex` and resumes from there on the next call. This can cause bugs if you don't reset the regex.
    

### `match()`

- **Definition:** Retrieves the result of matching a string against a regex.
    
- **Returns:**
    
    - If `g` flag is **OFF**: Returns an Array with the match, index, input, and capturing groups.
        
    - If `g` flag is **ON**: Returns an Array of _strings_ (all matches) but **discards** capturing groups and index details.
        
    - No match: Returns `null`.
        
- **Critical Warning:** Because it returns `null` on failure, trying to access `.length` or iterate over the result without checking for `null` first will crash your program.
    

### `matchAll()`

- **Definition:** The modern, superior version of `match()` for global searches.
    
- **Returns:** An `Iterator` (not an array) containing all matches, _including_ capturing groups and index details for every single match.
    
- **Use Case:** When you need to scrape data globally (e.g., finding all image URLs in a document) and you need the specific data inside the parentheses `()`.
    

### `replace()`

- **Definition:** Searches for a pattern and swaps it with a replacement.
    
- **Deep Dive:** The replacement argument can be a string _or_ a callback function.
    
    - _String:_ `'Hello World'.replace(/World/, 'Universe')`
        
    - _Function:_ `str.replace(/[A-Z]/, (char) => char.toLowerCase())`. This allows dynamic logic during replacement.
        

### `replaceAll()`

- **Definition:** Replaces _every_ occurrence of the pattern.
    
- **Constraint:** If you pass a Regex to `replaceAll`, you **must** use the `g` flag. If you forget `g`, it throws a `TypeError`.
    

---

## 2. Modifiers (Flags)

### `i` (Case Insensitive)

- **Definition:** Ignores casing (A == a).
    
- **Performance:** Slightly slower than case-sensitive matching, but usually negligible.
    

### `g` (Global)

- **Definition:** Don't stop at the first match; search the whole string.
    
- **Mechanism:** When used, the regex object keeps track of `lastIndex` (where it left off).
    

### `m` (Multi-line)
    
- **Technical Definition:** It changes the behavior of Anchors (`^` and `$`).
    
    - Default: `^` matches start of string, `$` matches end of string.
        
    - With `m`: `^` matches start of _any line_ (after a `\n`), `$` matches end of _any line_ (before a `\n`).
        

### `s` (DotAll)

- **Definition:** Allows the dot `.` wildcard to match newline characters (`\n`).
    
- **Why it exists:** By default, `.` stops at the end of a line. If you are scraping HTML code across multiple lines, you need this flag.
    

### `u` (Unicode)

- **Definition:** Enables full Unicode support.
    
- **Critical:** Without `u`, JavaScript treats Emoji (like 💩) as two separate characters (surrogate pairs). With `u`, it treats them as one character. Always use this when processing user-generated text.
    

### `y` (Sticky)

- **Definition:** Matches _only_ at the exact `lastIndex`.
    
- **Difference from `g`:** The `g` flag scans ahead until it finds a match. The `y` flag attempts to match exactly at the current position; if it fails there, it stops immediately. Used for writing tokenizers/compilers.
    

---

## 3. Character Classes (The Building Blocks)

### The Wildcard `.`

- **Definition:** Matches any single character _except_ line terminators (unless `s` flag is used).
    

### `\d` (Digit) vs `\D` (Non-Digit)

- **Definition:** Matches `[0-9]`.
    
- **Note:** Does _not_ match localized digits (like Arabic or Hindi numerals) unless the regex e22ngine is Unicode-aware (varies by environment).
    

### `\w` (Word Character) vs `\W` (Non-Word)

- **Definition:** Matches `[A-Za-z0-9_]`.
    
- **Important:** It includes the **underscore** `_`. It does _not_ include hyphens `-` or spaces.
    

### `\s` (Whitespace) vs `\S` (Non-Whitespace)

- **Definition:** Matches spaces, tabs (`\t`), newlines (`\n`), and carriage returns (`\r`).
    

### Custom Classes `[]`

- **Definition:** A set of allowed characters. `[abc]` matches "a" OR "b" OR "c".
    
- **Ranges:** `[a-z]` matches any lowercase letter.
    
- **Negation:** `[^abc]` matches any character that is **NOT** a, b, or c. (Note: The `^` inside brackets means "NOT", outside it means "Start of string").
    

---

## 4. Anchors (Positioning)

- **`^` (Caret):** Asserts position at the start of the string (or line, if `m` flag is on).
    
- **`$` (Dollar):** Asserts position at the end of the string (or line, if `m` flag is on).
    
- **`\b` (Word Boundary):** A zero-width assertion. It matches the invisible line between a word character (`\w`) and a non-word character (`\W`). Use this to match "whole words only".
    

---

## 5. Quantifiers (Counting)

- **`*` (Star):** 0 or more times. (Optional, repeated).
    
- **`+` (Plus):** 1 or more times. (Required, repeated).
    
- **`?` (Question):** 0 or 1 time. (Optional).
    
- **`{n}`:** Exactly `n` times.
    
- **`{n,}`:** `n` or more times.
    
- **`{n,m}`:** Between `n` and `m` times.
    

### Concept: Greedy vs. Lazy

- **Greedy (Default):** `.*` will eat as much text as possible while still allowing the pattern to match.
    
- **Lazy:** `.*?` (adding a `?` after a quantifier) makes it eat as _little_ as possible.
    
- **Your Markdown Error:** You correctly used `(.*?)` in your project. If you had used `(.*)`, it would have swallowed everything from the first bold tag to the last one on the page.
    

---

## 6. Assertions (Lookarounds)

_Definition:_ These check the surroundings without consuming characters. The cursor stays put.

### Lookaheads `(?=...)` and `(?!...)`

- **Positive `(?=code)`:** "Is the next thing 'code'? If yes, match the current thing, but don't include 'code' in the result."
    
- **Negative `(?!code)`:** "Is the next thing _not_ 'code'?"
    

### Lookbehinds `(?<=...)` and `(?<!...)`

- **Positive `(?<=free)`:** "Is the thing before me 'free'?"
    
- **Negative `(?<!free)`:** "Is the thing before me _not_ 'free'?"
    
- **Browser Support:** Lookbehinds are newer features (ES2018). Older browsers (like old Safari) will crash if you use these.
    

---

## 7. Groups

### Capturing Groups `(...)`

- **Function:** Groups parts of the regex and "remembers" the match.
    
- **Access:** Can be accessed via `$1`, `$2` in `replace()`, or index `[1]`, `[2]` in `match()`.
    

### Non-Capturing Groups `(?:...)`

- **Function:** Groups parts for logic (e.g., `(?:ab)+` to repeat "ab") but **does not** remember the match.
    
- **Why use it?** Performance and cleanliness. If you don't need to extract the text, don't capture it.

---

## Part 2: Code Audit

### Project 1: Regex Tester

```javascript
const htmlInput = document.querySelector("#markdown-input");

const htmlPreview = document.querySelector("#preview");

const htmlOutput = document.querySelector("#html-output");

  

htmlInput.addEventListener("input", convertMarkdown);

  

function convertMarkdown() {


    let result = htmlInput.value;

  

    result = result.replace(/^### (.*$)/gim, '<h3>$1</h3>');

    result = result.replace(/^## (.*$)/gim, '<h2>$1</h2>');

    result = result.replace(/^# (.*$)/gim, '<h1>$1</h1>');

  

    result = result.replace(/^\s*> (.*$)/gim, '<blockquote>$1</blockquote>');

  

     result = result.replace(/\*\*(.*?)\*\*/gim, '<strong>$1</strong>');

     result = result.replace(/__(.*?)__/gim, '<strong>$1</strong>');

  

     result = result.replace(/\*(.*?)\*/gim, '<em>$1</em>');

     result = result.replace(/_(.*?)_/gim, '<em>$1</em>');

  

     result = result.replace(/!\[(.*?)\]\((.*?)\)/gim, '<img alt="$1" src="$2">');

     result = result.replace(/\[(.*?)\]\((.*?)\)/gim, '<a href="$2">$1</a>');

  

    htmlOutput.textContent = result;

    htmlPreview.innerHTML = result;

    return result

}

```

### Project 2: Markdown Converter

```javascript

const regexPattern = document.querySelector("#pattern")
const stringToTest = document.querySelector("#test-string")
const testButton = document.querySelector("#test-btn")
const testResult = document.querySelector("#result")
let caseInsensitiveFlag = document.querySelector("#i")
let globalFlag = document.querySelector("#g")

function getFlags() {

  if (!caseInsensitiveFlag.checked && !globalFlag.checked) {
    return ``

  } else if (caseInsensitiveFlag.checked && !globalFlag.checked) {

  return `i`

} else if (!caseInsensitiveFlag.checked && globalFlag.checked) {

  return `g`
} else {

  return `ig`

}
}
testButton.addEventListener("click", () => {

    let pattern = regexPattern.value;
    let flags = getFlags();
    let reg = new RegExp(pattern, flags);
    let giveResult = stringToTest.textContent.match(reg)

    if (giveResult === null) {

    let nullCondition = testResult.innerHTML = `no match`

    return nullCondition

  }

  else {

    let highlights = stringToTest.textContent.replace(reg, (match) => `<span class="highlight">${match}</span>`)

    let joingArray = giveResult.join(", ")

    testResult.innerHTML = `${joingArray}`;

    stringToTest.innerHTML = highlights;

  }})
```