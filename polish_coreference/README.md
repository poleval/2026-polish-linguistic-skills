## Subtask 4: Polish Coreference Antecedent Selection

### Introduction and motivation

Understanding a connected text requires identifying expressions that refer to the same people, objects or other entities. Pronouns and noun phrases can refer back to earlier mentions in a passage. In Polish, rich inflection, flexible word order and long-distance dependencies make this a challenging contextual task.

This task evaluates coreference resolution as a four-choice decision: identifying which earlier mention refers to the same entity as the marked target.

### Subtask description

Each example provides a multi-sentence context with one target expression marked as `[T]...[/T]`. Four candidate expressions are marked as `[A]...[/A]`, `[B]...[/B]`, `[C]...[/C]` and `[D]...[/D]`. The `candidates` object associates each letter with the corresponding candidate text.

The model must select the candidate that refers to the same entity as the target mention. The target may be a pronoun or a noun phrase, and identifying its referent may require information from several sentences. The choice should be based on referential identity rather than similarity in meaning or grammatical form.

### Dataset

The **test-A** dataset comprises 348 examples derived from the Polish Coreference Corpus (PCC) in the CorefUD representation. The passages contain pronominal and nominal target mentions, vary in length and present four marked candidate mentions. Solving the task requires interpreting referential relations in Polish context.

### Input format

Each item contains `id`, `context` and a `candidates` object whose keys are `A`, `B`, `C` and `D`:

```json
{
  "id": "726dfc46-7762-5be0-9477-38535b538f9b",
  "context": "Kluczem do sukcesu jest zatem \" właściwa postawa umysłowa w dowolnej sytuacji \" (s. 39), nawet w czasach najgorszego kryzysu życiowego. [A]Człowiek z pozytywnym nastawieniem[/A] to człowiek uśmiechnięty, z odpowiednią postawą i tonem [C]głosu[/C], to człowiek pewny [D]swojego sukcesu[/D], a więc człowiek atrakcyjny dla innych i samego sukcesu. [B]Człowiek negatywny i nieatrakcyjny[/B] to [T]jego[/T] przeciwieństwo.",
  "candidates": {
    "A": "Człowiek z pozytywnym nastawieniem",
    "B": "Człowiek negatywny i nieatrakcyjny",
    "C": "głosu",
    "D": "swojego sukcesu"
  }
}
```

The markers identify the relevant mentions in the context, while the `candidates` object provides the text associated with each marked candidate.

### Model answer

Return a single uppercase letter identifying the antecedent. For the example above:

```text
A
```

### Evaluation

The primary metric is macro-F1 over labels `A`, `B`, `C` and `D`, with one selected candidate per item. The metric gives equal weight to performance on each label. A response that does not identify one of the four candidates cannot be scored as a valid selection.

### Submission format

Submit one JSON object per line, holding `id` (the input item's `id`) followed by the answer fields: `answer`. For the input example above, the line is:

```json
{"id": "726dfc46-7762-5be0-9477-38535b538f9b", "answer": "A"}
```

The schema of these lines is `output_schema.json`, which is published beside `inputs.jsonl`. On the endpoint route the platform builds these lines from the model's answers; for this bare-text task the answer goes under `"answer"`. A participant uploading a file must produce the same lines.
