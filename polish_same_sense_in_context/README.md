## Subtask 3: Same Sense in Context for Polish

### Introduction and motivation

A single Polish word may express different meanings in different contexts. Inflection makes simple string comparison even less reliable: two occurrences of the same lemma can have different surface forms yet express the same sense, while identically spelled forms can have unrelated meanings.

This task evaluates the ability to distinguish word meanings in context by identifying which candidate uses a marked word in the same sense as the reference sentence.

### Subtask description

Each example contains a `reference_sentence` and four candidate passages in a single `candidates` object, labeled `A`, `B`, `C` and `D`. The target word is enclosed in `[[TARGET]]` and `[[/TARGET]]` markers in the reference sentence and in each candidate. The model must select the candidate in which the marked word has the same meaning as in the reference sentence.

The comparison concerns the meaning of the marked word, not the overall topic, topical association or similarity between the surrounding texts. Different inflected forms of the same lemma can convey the same meaning. Exactly one candidate is designated as the correct choice for each item.

### Dataset

The **test-A** dataset consists of 139 examples covering 119 different target lemmas and several parts of speech, including nouns, adjectives, verbs and adverbs. Related Polish word-sense-disambiguation resources available through CLARIN-PL include KPWr, KPWr-100, Sherlock, Składnica, WikiGLEX, EmoGLEX and Walenty.

Candidate passages vary in style and length. Some contain multiple sentences or dictionary-style fragments. In every case, the target markers identify the word whose meaning must be compared.

### Input format

The input fields are `id`, `reference_sentence` and `candidates`. The abbreviated example below illustrates the markers and the four-choice format:

```json
{
  "id": "dc6694f5-68f7-57cd-95f4-bc2b9052ea8c",
  "reference_sentence": "Kiedy udało mi się jakoś przemęczyć tę drugą [[TARGET]]klasę[[/TARGET]]…",
  "candidates": {
    "A": "…w każdej pierwszej [[TARGET]]klasie[[/TARGET]] szkoły podstawowej…",
    "B": "Podróżujący pierwszą [[TARGET]]klasą[[/TARGET]] będą odtąd mogli wypoczywać podczas lotu…",
    "C": "…obiekt stworzony na podstawie danej [[TARGET]]klasy[[/TARGET]] nazywany jest jej instancją.",
    "D": "…premiery tego gniota [[TARGET]]klasy[[/TARGET]] B?"
  }
}
```

The ellipses in this example shorten the passages for presentation; the full passages are provided in the task input.

### Model answer

The output is a single uppercase letter: `A`, `B`, `C` or `D`.

For the illustrated case:

```text
A
```

### Evaluation

The primary metric is macro-F1 over the four response labels. Precision and recall are calculated separately for each label, and the resulting F1 scores are averaged with equal weight. Each item must receive exactly one valid label.

### Submission format

Submit one JSON object per line, holding `id` (the input item's `id`) followed by the answer fields: `answer`. For the input example above, the line is:

```json
{"id": "dc6694f5-68f7-57cd-95f4-bc2b9052ea8c", "answer": "A"}
```

The schema of these lines is `output_schema.json`, which is published beside `inputs.jsonl`. On the endpoint route the platform builds these lines from the model's answers; for this bare-text task the answer goes under `"answer"`. A participant uploading a file must produce the same lines.
