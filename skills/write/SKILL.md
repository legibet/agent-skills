---
name: write
description: Write and revise prose in Chinese or English. Use when drafting, revising or reviewing documents, READMEs, notes, papers, articles, code comments, UI copy, issues, PRs or messages.
---

# Write

Good text says what its reader needs, in an order the reader can follow, in the language a competent author in the field would use.

## Reader and content

Work out who will read the text, what they need to know or be able to do after reading it, and what order makes it easiest to follow. If you can't tell who the reader is or what the text is for, ask the user.

The material at hand (the conversation, the user's remarks, project documents, code, test logs, references, drafts from others) tells you what is true. Decide what to include, in what order and in what words according to what the reader needs. For example:

- The user's remarks during a discussion are directions for the text. Use their key terms when those are the right terms, and turn their explanations, questions and stated intentions into what the text itself should say.
- A session records work in the order it happened. Organize the text around what the reader wants to know, and leave out details that only mattered while the work was being done, such as the bug that prompted a setting or the options that were later dropped.
- Different kinds of text serve different readers. A UI string tells users what they are looking at, a config comment explains the setting, and a README tells a newcomer what the project is and how to use it.
- When the text continues an existing document, read that document first and keep its voice, terms and conventions.

## Language

For Chinese text, read [references/zh.md](references/zh.md). For English text, read [references/en.md](references/en.md). Each file describes problems that often appear in writing in that language, and its examples show typical forms of each problem. Keep text that reads naturally, even when it contains a listed phrase.

## Revision

Revise for the same reader and genre as the draft. A fix to a sentence in a set of instructions should still read as a short instruction.

When you edit existing text, change what is wrong and keep what works. Edit in place, and keep each change as small as the problem. If one word is wrong, replace that word. If a sentence is muddled, rewrite that sentence. Regenerating a whole paragraph changes sentences nobody questioned and introduces new problems. Keep sentences the user has accepted, and when you revert a change, restore the original wording.

Keep the meaning when you edit. Conditions, numbers, inequalities and the strength of each claim should stay as they were, and the edit should not add claims that the source doesn't support.

When the user corrects something, the correction points at a specific problem. Work out what the problem is and why it fails the reader, then fix it where it occurs. Fix the same problem elsewhere only where it actually occurs. For example, if the user calls one parenthetical unnecessary, the problem is that parenthetical, and other parentheses can stay. Each fix should bring the sentence closer to what a competent author would write. When you expand a compressed sentence, restore the missing parts without adding padding. When you remove a hedge, keep the sentence complete.
