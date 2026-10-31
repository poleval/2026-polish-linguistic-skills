## Subtask 1: Polish Morphosyntactic Error Detection and Correction

### Introduction and motivation

Polish has a rich inflectional system in which word forms express grammatical case, number, gender and agreement relations. A small change in an inflectional ending can make a sentence ungrammatical, for example by disrupting adjective–noun agreement, subject–verb agreement or the case required by a preposition. Detecting these errors requires sensitivity to the grammatical structure of the entire sentence rather than to individual words in isolation.

This task evaluates two complementary abilities: determining whether a Polish sentence is morphosyntactically correct and, where necessary, producing a precise correction while preserving its original meaning.

### Subtask description

For each sentence, the system must determine whether it is morphosyntactically correct and provide a correction when needed. The decision is expressed using one of two uppercase Polish labels:

- `POPRAWNE` — the sentence is grammatically correct under the task criteria;
- `NIEPOPRAWNE` — the sentence contains a morphosyntactic error requiring correction.

For `POPRAWNE`, `corrected_sentence` must be an empty string. For `NIEPOPRAWNE`, it must contain the full corrected sentence. The system should change only what is necessary to repair the error or errors, preserving the original vocabulary, meaning, word order, spelling and punctuation wherever possible.

In particular, a stylistic preference does not justify a rewrite of a grammatically correct sentence. The task tests controlled correction rather than unrestricted paraphrasing or general text editing.

### Dataset

The **test-A** dataset consists of 421 Polish sentences based on material from UD Polish-PUD. Morfeusz 2 was involved in preparing or verifying controlled inflectional modifications. The material covers agreement, inflection and government-related phenomena, including nominal, adjectival and verbal forms.

Some sentences require no changes; others contain one or more inflectional errors. Corrections are evaluated at sentence level, which means that a system must identify all relevant errors and avoid unnecessary edits.

### Input format

Each input item contains the fields `id` and `sentence`:

```json
{
  "id": "03f6c5fc-c27b-4d54-82bb-5b1614af8d21",
  "sentence": "Ten rozdźwięk pomiędzy danymi gospodarczymi a dyskursem politycznym są, a przynajmniej powinien być znajomy."
}
```

### Model answer

The response is a JSON object with two fields:

- `answer`: `POPRAWNE` or `NIEPOPRAWNE`;
- `corrected_sentence`: the full corrected sentence for `NIEPOPRAWNE`, or an empty string for `POPRAWNE`.

For the input above, the expected output is:

```json
{
  "answer": "NIEPOPRAWNE",
  "corrected_sentence": "Ten rozdźwięk pomiędzy danymi gospodarczymi a dyskursem politycznym jest, a przynajmniej powinien być znajomy."
}
```

For a grammatically correct sentence, return `POPRAWNE` with an empty string in the `corrected_sentence` field. Otherwise, return `NIEPOPRAWNE` and provide the complete corrected sentence, rather than only the modified words or a list of edits.

### Evaluation

The main metric is full-task accuracy. An item receives credit only if both the correctness label and the `corrected_sentence` value are correct. Missing corrections, unnecessary changes and newly introduced errors result in an incorrect sentence-level prediction.

### Submission format

Submit one JSON object per line, holding `id` (the input item's `id`) followed by the answer fields: `answer` and `corrected_sentence`. For the input example above, the line is:

```json
{"id": "03f6c5fc-c27b-4d54-82bb-5b1614af8d21", "answer": "NIEPOPRAWNE", "corrected_sentence": "Ten rozdźwięk pomiędzy danymi gospodarczymi a dyskursem politycznym jest, a przynajmniej powinien być znajomy."}
```

The schema of these lines is `output_schema.json`, which is published beside `inputs.jsonl`. On the endpoint route the platform builds these lines from the model's answers, adding the `id`. A participant uploading a file must produce the same lines.
