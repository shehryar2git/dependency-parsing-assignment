# Assignment 1: Dependency Network Analysis of Pride and Prejudice

**Author:** Shehryar Ahmed
**Date:** October 3, 2026

**Public Colab Link:** [https://colab.research.google.com/drive/1vO33wqt2JeyBO0a--oE8lzHLfBveUgMW?usp=sharing]

---

## Repository Contents

- `Assignment1_Pride_and_Prejudice_Dependency_Analysis.ipynb` — The full Colab notebook with all code and text commentary.
- `pride_and_prejudice_cleaned.txt` — The cleaned novel text used for the analysis (Project Gutenberg boilerplate stripped).
- `dependency_network.png` — The dependency network visualization.
- `dependency_network_communities.png` — The community-detection visualization.

---

## 1. Lemmatization (Task 1)

I lemmatized Jane Austen's *Pride and Prejudice* (1813) using spaCy's `en_core_web_lg` model. I chose the large model over the smaller variants because the assignment is graded on annotation quality, and the large model has measurably higher accuracy on both lemmatization and dependency parsing. I disabled the named-entity recognizer (NER) since it is irrelevant to this task. Lemmatization reduced inflected forms to their base form (e.g., *marriages* → *marriage*, *was* → *be*), which is a prerequisite for treating all occurrences of a lexeme as a single node in the network.

## 2. Target Word and Dependency Extraction (Task 2)

**Chosen lemma:** *marriage*.

I selected *marriage* for three reasons:
1. **Statistical frequency:** It appears among the top content lemmas in the novel, giving a rich dependency network.
2. **Thematic centrality:** The opening sentence of the novel establishes marriage as its central concern; the plot turns on courtship and matrimony.
3. **Interpretive potential:** I hypothesized that its syntactic neighborhood would reveal whether Austen's characters treat marriage as an action, a state, a transaction, or a social obligation.

For every token whose lemma is *marriage*, I extracted:
- The token's own head (its governor in the dependency tree),
- All direct children of the token, and
- All children of those children (secondary dependencies).

I excluded self-loops (grandchildren identical to the target) to avoid artificially inflating the target's degree. Each edge was stored as a `(head, dependent, relation)` triple.

## 3. Graph Visualization (Task 3)

I built a **directed** graph in NetworkX, with each edge labeled by its Universal Dependencies relation.

**Parameter justifications:**
- **Directed graph:** Syntactic dependencies are inherently directional. In *her marriage*, *marriage* is the head and *her* the dependent; reversing this would be linguistically meaningless.
- **Node size = degree:** Degree (number of connections) is the most direct measure of a node's syntactic versatility. Larger nodes are syntactically more active.
- **Node color = part of speech:** POS is linguistically interpretable. A viewer can see at a glance whether *marriage* is surrounded by nouns, verbs, adjectives, or pronouns. This transforms the graph from a picture into an analytical tool.
- **Edge width = frequency of the (head, dependent, relation) triple:** Recurring syntactic patterns are more characteristic of the novel's style than one-off accidents and are therefore emphasized visually.
- **Spring layout with fixed seed (42):** The spring algorithm clusters densely connected nodes together, making communities visually apparent; the fixed seed makes the layout reproducible.

## 4. Network Analysis (Task 4)

**Density:** I computed the density of both the directed and undirected versions. Density is the ratio of actual to possible edges. The low density I observed (on the order of 0.02–0.05) confirms that the network is **sparse** — characteristic of syntactic networks, which are locally dense but globally sparse. This confirms the neighborhood is structured, not random.

**Community detection:** I applied the Louvain algorithm (`python-louvain`) to the undirected version of the graph. I chose Louvain because it is the most widely used community-detection method, runs in near-linear time, requires no pre-specified number of communities, and optimizes a well-defined objective (modularity).

The algorithm detected four communities:
- **Relational / possessive:** *her, his, their, sister, daughter, family, fortune* — whose marriage, with what stakes.
- **Evaluative:** *happy, good, advantageous, unhappy, prudent* — what kind of marriage.
- **Action:** *propose, prevent, desire, think, make, have* — what is done about marriage.
- **State:** *be, single, married, wife, husband* — the state of being married or unmarried.

## 5. Conclusions (Task 5)

The dependency network of *marriage* in *Pride and Prejudice* reveals that Austen's characters discuss marriage not as a single concept but as a cluster of distinct concerns. The Louvain partition separates a **relational** dimension (whose marriage), an **evaluative** dimension (what kind of marriage), an **action** dimension (what is done about marriage), and a **state** dimension (what marriage is). This syntactic partitioning mirrors the novel's central thematic tension: marriage is simultaneously a personal relationship, a social evaluation, a strategic action, and a legal state. The fact that these four dimensions are syntactically distinct — rather than blended — suggests Austen's prose systematically separates these concerns, forcing the reader to hold them in tension. This is a linguistic instantiation of the novel's opening irony: marriage is "a truth universally acknowledged" precisely because it is overdetermined by competing social, economic, and emotional meanings.

## Reproducibility

To reproduce this analysis:
1. Open the public Colab link at the top of this README.
2. Run all cells from top to bottom.
3. When prompted by the first cell, upload `pride_and_prejudice_cleaned.txt` (committed in this repository).
