## Subtask 2: Contextual Numeral Inflection in Polish

### Introduction and motivation

The form of a Polish numeral expressed in words depends on its grammatical context. Numerals can vary according to case, gender, animacy and syntactic function, while complex numerals require coordination among several inflected components. Reading a number in running text therefore involves more than replacing its digits with a fixed dictionary form.

This task measures the ability to convert a numerical expression into a fully written Polish numeral that fits the surrounding sentence.

### Subtask description

Each sentence contains one target numerical expression marked with double square brackets, for example `[[120]]`. The model must write this expression in words, using the grammatical form required by the surrounding context. Other numerical expressions in the sentence serve as contextual information and should not appear in the output.

The response consists solely of the written-out numeral. Do not repeat the sentence, include the original digits or provide grammatical explanations.

### Dataset

The **test-A** dataset comprises 2,653 sentences drawn from Polish dependency-treebank resources: NKJP1M-UD, PDB-UD, PUD-PL and UD Polish LFG. Each sentence contains exactly one marked target expression. The examples include numerals of different lengths in a range of grammatical constructions.

### Input format

Each item contains `id` and `sentence`. The target expression is enclosed in double square brackets:

```json
{
  "id": "bba62d3e-d863-4a78-aea5-0caf450dbc22",
  "sentence": "'Reutz' płynący z Maroka do Szczecina i przewożący koncentrat rudy miedzi zatonął 13 lipca 1998, ok. [[120]] mil od wybrzeży Portugalii."
}
```

### Model answer

For this sentence, the answer is the plain-text numeral phrase:

```text
stu dwudziestu
```

The output must not include surrounding words, quotation marks, the original digits or the `[[...]]` delimiters. Separate words in multiword numeral expressions with standard spaces.

### Evaluation

The primary metric is exact-match accuracy. The output for each item is compared with accepted grammatical realizations of the marked numeral; an item counts as correct if the output matches an accepted form. Each sentence contributes one item to the score, and partly correct numeral expressions do not earn partial credit.

### Submission format

Submit one JSON object per line, holding `id` (the input item's `id`) followed by the answer fields: `answer`. For the input example above, the line is:

```json
{"id": "bba62d3e-d863-4a78-aea5-0caf450dbc22", "answer": "stu dwudziestu"}
```

The schema of these lines is `output_schema.json`, which is published beside `inputs.jsonl`. On the endpoint route the platform builds these lines from the model's answers; for this bare-text task the answer goes under `"answer"`. A participant uploading a file must produce the same lines.
