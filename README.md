# Polish Linguistic Skills

This task evaluates linguistic competence in Polish across six complementary subtasks covering morphosyntax, lexical semantics, reference, discourse relations and punctuation. Systems are evaluated on morphosyntactic error detection and correction, contextual numeral inflection, word-sense discrimination, coreference antecedent selection, universal discourse parsing and comma placement. Together, the subtasks assess the ability to use grammatical, semantic and discourse context to make precise linguistic decisions at the word, sentence and multi-sentence levels.

- [Subtask 1: Polish Morphosyntactic Error Detection and Correction](#subtask-1-polish-morphosyntactic-error-detection-and-correction)
- [Subtask 2: Contextual Numeral Inflection in Polish](#subtask-2-contextual-numeral-inflection-in-polish)
- [Subtask 3: Same Sense in Context for Polish](#subtask-3-same-sense-in-context-for-polish)
- [Subtask 4: Polish Coreference Antecedent Selection](#subtask-4-polish-coreference-antecedent-selection)
- [Subtask 5: Polish Universal Discourse Parsing](#subtask-5-polish-universal-discourse-parsing)
- [Subtask 6: Polish Comma Placement](#subtask-6-polish-comma-placement)

## Model size limit

Only models with at most 12B parameters may be used in this task. The limit is meant to encourage creative adaptation of models to the task, e.g. through fine-tuning, rather than reliance on the largest available models.

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

### Output format and example

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

The main metric is full-task accuracy. An item receives credit only if both the grammaticality label and the `corrected_sentence` value are correct. Unnecessary rewriting, missing corrections and newly introduced errors result in an incorrect sentence-level prediction.

### Submission format

Provide one JSON object containing `answer` and `corrected_sentence` for each input item. Do not include explanations or intermediate editing steps.

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

### Output format and example

For this sentence, the answer is the plain-text numeral phrase:

```text
stu dwudziestu
```

The output must not include surrounding words, quotation marks, the original digits or the `[[...]]` delimiters. Separate words in multiword numeral expressions with standard spaces.

### Evaluation

The primary metric is exact-match accuracy. The output for each item is compared with accepted grammatical realizations of the marked numeral; an item counts as correct if the output matches an accepted form. Each sentence contributes one item to the score, and partly correct numeral expressions do not earn partial credit.

### Submission format

Provide one written-out numeral phrase per input item, with no surrounding sentence and no explanatory text.

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

### Output format and example

The output is a single uppercase letter: `A`, `B`, `C` or `D`.

For the illustrated case:

```text
A
```

### Evaluation

The primary metric is macro-F1 over the four response labels. Precision and recall are calculated separately for each label, and the resulting F1 scores are averaged with equal weight. Each item must receive exactly one valid label.

### Submission format

Return only the selected letter for each item. Do not include the full candidate passage, a confidence explanation or multiple possible labels.

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

### Output format and example

Return a single uppercase letter identifying the antecedent. For the example above:

```text
A
```

### Evaluation

The primary metric is macro-F1 over labels `A`, `B`, `C` and `D`, with one selected candidate per item. The metric gives equal weight to performance on each label. A response that does not identify one of the four candidates cannot be scored as a valid selection.

### Submission format

Provide exactly one candidate label per input item, without rewriting the passage or explaining the decision.

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

### Output format and example

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

Produce one JSON object per topic passage containing only the predicted `discourse_units`. Do not repeat the input text or add explanations.

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

### Output format and example

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

Provide one JSON object per sentence containing `commas` and `optional`. Both fields must contain arrays of marker indices, with no explanations, original sentences or unrelated fields.
