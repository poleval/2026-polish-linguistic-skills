## Subtask 6: Polish Comma Placement

### Introduction and motivation

Polish comma placement depends on syntactic structure, including clause boundaries, subordination, coordination and certain participial constructions. Deciding whether a comma is needed at a given boundary often requires understanding the relationship between the surrounding phrases, rather than only the adjacent words.

The task evaluates punctuation decisions at predefined insertion positions, separating comma placement from unrestricted text editing.

### Subtask description

The input is a Polish sentence in the `sentence_with_positions` field. Numbered markers such as `[1]`, `[2]` and `[3]` indicate possible positions for commas. The model must determine which marked boundaries require a comma under standard Polish punctuation rules in that context.

Return the marker indices, not comma characters or a rewritten sentence. A sentence may require commas at several marked positions or at none of them. Unmarked positions are outside the scope of the task. Each number identifies a position within that sentence.

### Dataset

The **test-A** dataset consists of 1,759 sentences based on the Polish CDSCorpus. Each sentence has between six and ten numbered candidate boundaries and includes a range of syntactic constructions. Markers are numbered consecutively within each item, starting at 1.

### Input format

Each record supplies an `id` and the `sentence_with_positions` string:

```json
{
  "id": "a89c964e-6112-4979-8279-75250db45426",
  "sentence_with_positions": "Bańki [1] mydlane [2] są puszczane przez [3] dziewczynkę w sukience [4] na trawie [5] a [6] za [7] nią stoi druga dziewczynka."
}
```

### Model answer

The response is a JSON object with two fields:

- `commas`: an array of integer indices marking required commas;
- `optional`: an array of integer indices identifying positions where a comma is permissible but not required.

For the example above:

```json
{
  "commas": [5],
  "optional": []
}
```

List the indices in ascending order without duplicates. Use empty arrays where appropriate. Do not insert punctuation into the sentence or report character offsets.

### Evaluation

The main metric is Exact Set Accuracy for required comma positions. The `commas` field is correct only when its indices exactly match the reference set. Missing a required position or adding an incorrect required position makes the item incorrect. Evaluation uses the marker numbers, not offsets in the sentence.

The `optional` field records permissible but non-obligatory comma positions separately from required positions.

### Submission format

Submit one JSON object per line, holding `id` (the input item's `id`) followed by the answer fields: `commas` and `optional`. For the input example above, the line is:

```json
{"id": "a89c964e-6112-4979-8279-75250db45426", "commas": [5], "optional": []}
```

The schema of these lines is `output_schema.json`, which is published beside `inputs.jsonl`. On the endpoint route the platform builds these lines from the model's answers, adding the `id`. A participant uploading a file must produce the same lines.
