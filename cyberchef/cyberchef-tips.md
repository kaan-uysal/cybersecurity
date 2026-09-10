# CyberChef Pro-Tips

> **Overview:** A curated collection of essential tips, hidden nuances, and workflow shortcuts for using CyberChef in SOC triage, malware analysis, and log decoding.

---

## 1. Essential Workflow Shortcuts & Features

* **Disable Auto-Bake for Large Inputs:**
  * When pasting heavy logs or large malware payloads, **uncheck "Auto-Bake"** at the bottom right. Otherwise, CyberChef will attempt to run operations on every keystroke, freezing the browser.
* **Use Magic (The Swiss Army Knife):**
  * If you encounter an unidentified encoded string, use the **`Magic`** operation before trying manually. It attempts automated brute-force decoding using depth-based pattern recognition.
* **Control Flow Operations:**
  * **`Fork`:** Splits input by a delimiter (e.g., new lines) and applies the recipe to each line individually.
  * **`Merge`:** Combines split streams back into a single output.
  * **`Subsection`:** Applies a recipe *only* to a specific regex match within the text without altering the rest.

---

## 2. Critical Nuances

### Base64 Padding & URL-Safe Encoding
* **Standard Base64 vs. Base64 URL Safe:**
  * URL-safe Base64 replaces `+` with `-` and `/` with `_`.
  * Standard Base64 requires `=` padding to make the string length a multiple of 4. If CyberChef throws an error, try enabling **"Alphabet: A-Za-z0-9-_="** or adding padding manually.

### Character Encodings (UTF-16)
* PowerShell encoded commands are typically **UTF-16LE (Little Endian)**, not ASCII or UTF-8.
* **Correct Recipe Chain:**
  1. `From Base64`
  2. `Decode Text` ➔ Select **`UTF-16LE (1200)`**
  * *Pitfall:* Skiping `Decode Text` results in null bytes (`\x00`) separating every character in your output.

### URL Decoding (Multiple Layers)
* Attackers often double-encode payloads in URLs (e.g., `%2520` instead of `%20`).
* Use **`URL Decode`** multiple times or combine it with **`Find / Replace`** to handle nested URL encoding.

---

## 3. High-Value SOC Recipes

### De-obfuscating Malicious Command Lines
```text
Input: Base64 Encoded PowerShell Command
Recipe:
  1. From Base64 (A-Za-z0-9+/=)
  2. Decode Text ('UTF-16LE (1200)')
  3. Generic Revert / Remove Null Bytes (if needed)
  4. Find / Replace (Remove tick marks '`' used for evasion)
