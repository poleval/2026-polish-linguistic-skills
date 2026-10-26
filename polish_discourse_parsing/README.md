## Subtask 5: Polish Universal Discourse Parsing

### Introduction and motivation

Discourse comprehension requires identifying relationships among statements, not just interpreting individual sentences. Elaboration, cause and contrast can connect discourse units across sentence boundaries, organizing a text into a coherent hierarchy.

The task evaluates reconstruction of hierarchical, labeled discourse structures in Polish according to the formalism proposed by the [Universal Discourse project](https://udisc.org/).

### Subtask description

Each input is a topic passage segmented into discourse units (DUs), which together cover the passage without overlap. The model selects a parent (head) and a relation label for every DU except the root. The root has no parent or relation; all dependencies must form one directed rooted tree.

A head may appear before or after its dependent. A DU may contain non-contiguous parts, whose `order` values indicate where they occur in the original text.

The 21 possible relation labels are: Alternative, Attribution, Cause, Co-occurence, Comment, Concession, Condition, Contrast, Elaboration, Entity-elaboration, Exemplification, Feedback dependence, Formatting, Manner, Metatext, Purpose, Question-Answer, Repetition, Sequence-Elaboration, Similarity and Substitution.

### Dataset

The **test-A** dataset contains 63 topic passages from 49 Polish documents.

### Input format

Each JSON item contains `id`, `document_id`, `passage_id` and `discourse_units`. Each DU provides `du_id`, `orig_id`, `sent_id` and ordered text `parts`:

```json
{
  "id": "be592975-95e8-4eea-a1be-37c409d1b26b",
  "document_id": "PCC-short-18", "passage_id": "PCC-short-18:topic-02",
  "discourse_units": [
    {"du_id":"14.1", "orig_id":"PCC-short-18_p-5_0", "sent_id":"14",
     "parts":[{"order":0,"text":"Pomorze"}]},
    {"du_id":"15.1", "orig_id":"PCC-short-18_p-6_0", "sent_id":"15",
     "parts":[{"order":1,"text":"W ciągu 5 lat PAA przyjęła jedno zgłoszenie poparzenia środkiem radioaktywnym."}]}
  ]
}
```

### Model answer

Return a JSON object with a `discourse_units` array. Supply `du_id`, `head` and `relation` for each DU; use `null` for the root's `head` and `relation`. For the example above:

```json
{
  "discourse_units": [
    {"du_id": "14.1", "head": null, "relation": null},
    {"du_id": "15.1", "head": "14.1", "relation": "Metatext"}
  ]
}
```

### Evaluation

The primary metric, Labeled Attachment Score (LAS), requires both the parent and relation to match gold for each non-root DU. Unlabeled Attachment Score (UAS) measures parent accuracy alone; root accuracy and tree validity may be reported separately.

Every DU must appear once in a connected, acyclic tree with one root. Invalid heads, cross-passage links and labels outside the inventory are errors.

### Submission format

Submit one JSON object per line, holding `id` (the input item's `id`) followed by the answer fields: `discourse_units`. For the input example above, the line is:

```json
{"id": "be592975-95e8-4eea-a1be-37c409d1b26b", "discourse_units": [{"du_id": "14.1", "head": null, "relation": null}, {"du_id": "15.1", "head": "14.1", "relation": "Metatext"}]}
```

The schema of these lines is `output_schema.json`, which is published beside `inputs.jsonl`. On the endpoint route the platform builds these lines from the model's answers, adding the `id`. A participant uploading a file must produce the same lines.
