# English

## Sentences

### Compressed sentences

Write full sentences, with their articles, subjects and conjunctions. When a sentence is a string of fragments or noun phrases joined by colons and semicolons, rewrite it as one or more full sentences. To be concise, cut content the reader doesn't need, and keep the remaining sentences whole.

- Lapse: Root cause: stale cache; fix: restart.
- Better: The cause was a stale cache, so restarting the service fixed it.
- Lapse: Tested locally; no regressions.
- Better: I tested the change locally and found no regressions.

### Abstract subjects and unclear references

Make the person or thing that acts the subject, and put the action in a verb. Sentences built on an abstract noun, such as "The value of X lies in …", "What remains is …" or "The implementation of X resulted in …", leave the reader to work out who did what. When "this", "it" or "the same X" could point to more than one thing, to something several sentences back, or to something the text never introduced, name the thing.

- Lapse: The implementation of caching resulted in a reduction in latency.
- Better: Caching reduced latency.
- Lapse: The accumulated conversation provides the agent with a record of previous revisions, experiments and evaluation results on which to base subsequent decisions.
- Better: The agent bases each decision on the earlier revisions, experiments and evaluation results in its conversation.
- Lapse: We moved the config into the repository and removed the old loader. This broke the deploy script.
- Better: Removing the old loader broke the deploy script.

### Long sentences

Put the subject and the main verb early. When a long subject or a chain of "which" and "that" clauses comes before the main verb, the reader has to hold all of it before learning what the sentence says. A long sentence reads well when its main clause comes first.

- Lapse: Developers who maintain projects that depend on libraries which change their interfaces often spend more time on updates than on new features.
- Better: Some libraries change their interfaces often. Developers whose projects depend on them spend more time on updates than on new features.

## Framing

Each pattern in this group adds a stance, a setup or a defense around the content. To judge one, ask what the reader would lose without it.

### Hedges and defensive sentences

When the evidence supports a claim, state it plainly. Save "may", "can help" and "potentially" for claims that are actually uncertain. When the evidence doesn't support a conclusion, leave out the conclusion and any sentence saying the evidence "does not establish" it. Address an objection only when a reader would actually raise it. If the reader asked whether something holds, "the evidence doesn't support it" is the answer, so say it.

- Lapse: This change may potentially help reduce latency in some cases.
- Better: This change reduces latency.
- Lapse: These results do not show that the method works on every dataset.
- Better: (delete the sentence)

### Sentences about the text

Write the content itself. Delete sentences that announce the structure, preview or recap it, or label what comes next. When a sentence points at a reason without giving it, write the reason.

- Lapse: Let's break this down.
- Lapse: Here's the thing.
- Lapse: Three decisions shape the design.
- Lapse: The reasons are structural.

### "X, not Y"

This construction rejects an idea the reader never had, so that the real point sounds sharper. The same move appears as "not X but Y", "X rather than Y", "It's not X, it's Y" and "The question isn't X. The question is Y." State Y directly. Use the contrast only where a reader might actually believe X.

- Lapse: Judge by observed output, not by reading the diff.
- Better: Judge by the observed output.
- Lapse: The question isn't speed. The question is cost.
- Better: The question is cost.

## Word choice

Use plain, ordinary words. Use the terms the field uses. Do not coin labels or shorthand of your own, because readers take them for established terms and cannot look them up. Write "use" instead of "leverage" or "utilize", "is" instead of "serves as" or "stands as", and "examine" instead of "delve into". Cut adverbs that add only emphasis, such as "quietly", "genuinely", "fundamentally" and "arguably". Words like "robust", "seamless" and "game-changer" usually stand in for a specific claim, so make that claim. In technical text, state the literal meaning instead of an idiom. In papers and documentation, avoid casual verbs.

- Lapse: A config option earns its place only if users actually change it.
- Better: Add a config option only if users actually need to change it.
- Lapse: The approach gets expensive fast as the input grows.
- Better: The cost of the approach rises quickly as the input grows.

## Punctuation

A semicolon joins two closely related independent clauses. Put loosely related facts in separate sentences.

Quotation marks enclose direct quotations, program output and words discussed as words. Scare quotes leave the reader to guess what the writer means, so say it directly.

- Lapse: The "fix" broke two other tests.
- Better: The change was meant to fix the bug, but it broke two other tests.

Replace em dashes in prose with a comma, a colon, parentheses or a new sentence, whichever fits.
