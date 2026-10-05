# CTF Challenge Writeup

## Challenge Overview

- **Category:** Miscellaneous / OSINT / Forensic Text Analysis
- **Description / Prompt:**

> "What makes you want to use cheats? What kinds of games do you use them in?"

---

## Analysis & Methodology

### 1. Initial Reconnaissance

The prompt asks two open-ended questions about game cheating behavior:

- *"What makes you want to use cheats?"*
- *"What kinds of games do you use them in?"*

Depending on the CTF platform, this challenge typically falls into one of three mechanics:

1. **Game Hacking / Memory Editing Binary:** The prompt is flavor text for a binary exploitation or game-hacking challenge (e.g., modifying player HP, score, or memory addresses using Cheat Engine, GDB, or Ghidra).
2. **Steganography / Hidden Metadata:** The text itself contains hidden data (zero-width spaces, whitespace steganography, or embedded strings in raw file/packet dumps).
3. **OSINT / Social Engineering Survey:** The challenge mirrors a survey or form question from a target's profile, forum post, or questionnaire dump.

---

## Solution / Exploitation Steps

### Approach A: String Analysis & Hidden Data

If the challenge provides this prompt as a file or text string:

1. **Check for hidden characters / zero-width spaces:**
   Use `xxd`, `hexdump`, or a Python script to inspect non-printable bytes within the prompt text.

```bash
   echo "What makes you want to use cheats? What kinds of games do you use them in?" | xxd
```

2. **Search for embedded strings:**
   If the challenge provides a compiled game executable or memory dump, extract strings matching standard flag formats (`flag{...}`, `CTF{...}`, or `picoCTF{...}`):

```bash
   strings challenge_bin | grep -i "flag"
```

---

### Approach B: Reverse Engineering / Game Memory Patching

If the challenge includes a small game binary associated with the cheat prompt:

1. **Locate the target variable:** Open the binary in Ghidra/IDA Pro and find functions handling game parameters (e.g., `is_cheater`, `player_score`, or `health`).
2. **Modify memory / assembly:**
   - Break in GDB at the conditional check for victory:

```gdb
     gdb ./game_binary
     b *main+124
     run
```

   - Overwrite register values or jump instructions (`jnz` to `jz`, or patch `CMP` results) to bypass the cheat detection logic and trigger the flag output.

---

### Approach C: Web / Input Form Payload

If the prompt is presented as an input field in a web application:

1. **Inspect traffic:** Capture requests using Burp Suite when submitting responses.
2. **Check for injections:** Test for SQL Injection (SQLi) or Command Injection in the response body or parameters:

```sql
   ' UNION SELECT 1, flag, 3 FROM flags--
```

---

## Verification & Flag Extraction

Once the bypass or hidden string is extracted from the binary or payload response, read the flag:

```text
flag{}
```
