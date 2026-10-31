## Subtask 1: Polish Language Error Detection and Correction

### Introduction and motivation

Polish is a linguistically complex language in which correctness depends on morphological, syntactic, lexical, orthographic and punctuation-related constraints. Errors may occur at different levels of linguistic structure. For example, a small change in an inflectional ending can disrupt adjective–noun agreement, subject–verb agreement or the case required by a preposition, while other errors involve word choice, spelling or punctuation. Detecting these errors requires sensitivity to the structure of the entire sentence rather than to individual words in isolation.

This task evaluates two complementary abilities: determining whether a Polish sentence contains a linguistic error and, where necessary, producing a precise correction while preserving its original meaning.

### Subtask description

For each sentence, the system must determine whether it is linguistically correct and provide a correction when needed. A sentence may contain one or more linguistic errors of any type, including, but not limited to, errors involving inflection, agreement, government, syntax, lexical choice, spelling or punctuation.

The decision is expressed using one of two uppercase Polish labels:

- `POPRAWNE` — the sentence contains no linguistic errors requiring correction;
- `NIEPOPRAWNE` — the sentence contains one or more linguistic errors requiring correction.

For `POPRAWNE`, `corrected_sentence` must be an empty string. For `NIEPOPRAWNE`, it must contain the full corrected sentence.

The system should change only what is necessary to correct the identified error or errors and should preserve the original meaning, vocabulary and word order wherever possible. Parts of the sentence that do not require correction, including their spelling and punctuation, should remain unchanged.

A purely stylistic preference does not justify rewriting a linguistically correct sentence. The task evaluates error detection and correction rather than unrestricted paraphrasing or general text rewriting.

### Dataset

The **test-A** dataset consists of 421 Polish sentences based on material from UD Polish-PUD. Morfeusz 2 was involved in preparing or verifying controlled inflectional modifications. Errors are not restricted to a predefined linguistic category and may occur at different levels of language, including morphology, syntax, lexis, orthography and punctuation.

Some sentences require no changes; others contain one or more errors of the same or different types. Corrections are evaluated at sentence level, which means that a system must identify and correct all errors while avoiding unnecessary changes.

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

For a linguistically correct sentence, return `POPRAWNE` with an empty string in the `corrected_sentence` field. Otherwise, return `NIEPOPRAWNE` and provide the complete corrected sentence, rather than only the modified words or a list of edits.

### Evaluation

The main metric is full-task accuracy. An item receives credit only if both the correctness label and the `corrected_sentence` value are correct. Missing corrections, unnecessary changes and newly introduced errors result in an incorrect sentence-level prediction.

### Submission format

Submit one JSON object per line, holding `id` (the input item's `id`) followed by the answer fields: `answer` and `corrected_sentence`. For the input example above, the line is:

```json
{"id": "03f6c5fc-c27b-4d54-82bb-5b1614af8d21", "answer": "NIEPOPRAWNE", "corrected_sentence": "Ten rozdźwięk pomiędzy danymi gospodarczymi a dyskursem politycznym jest, a przynajmniej powinien być znajomy."}
```

The schema of these lines is `output_schema.json`, which is published beside `inputs.jsonl`. On the endpoint route the platform builds these lines from the model's answers, adding the `id`. A participant uploading a file must produce the same lines.
