# Ordering Algorithms in FlexiConc

FlexiConc provides several algorithms to order concordance lines based on different criteria. These algorithms include both classic sorting methods and ranking methods (sometimes referred to as "rank guys") that compute scores used to sequence the lines. Below is a detailed, structured list of the available ordering algorithms.

---

## 1. Sort by Corpus Position

- **Description**:  
  Orders concordance lines by their inherent position in the corpus.  
  - Uses the natural order of line identifiers (usually from metadata) to assign sort keys.

- **Algorithm Type**:  
  - **Sorting**

- **Parameters**:  
  - _None_

- **Output**:  
  - **sort_keys**:  
    - A mapping of each line ID to its rank based on corpus position.

---

## 2. Random Sort

- **Description**:  
  Provides a pseudo-random yet stable ordering of lines.  
  - A fixed seed ensures that the relative order of any two lines remains consistent across runs.

- **Algorithm Type**:  
  - **Sorting**

- **Parameters**:  
  - **seed** (integer):  
    - **Description**: Optional seed for generating the pseudo-random order.  
    - **Default**: 42

- **Output**:  
  - **sort_keys**:  
    - A mapping of each line ID to a stable pseudo-random rank.

---

## 3. Sort by Token-Level Attribute

- **Description**:  
  Sorts concordance lines based on a specified token-level attribute. It can operate in different modes:

  - **Token Mode**:  
    - Sorts using a single token at a specified offset.

  - **Left Mode**:  
    - Sorts using the entire left context (tokens with negative offsets joined from right to left).

  - **Right Mode**:  
    - Sorts using the entire right context (tokens with positive offsets joined in natural order).

- **Algorithm Type**:  
  - **Sorting**

- **Parameters**:  

  - **tokens_attribute** (string):  
    - **Description**: Token attribute to use for sorting.  
    - **Default**: `"word"`

  - **sorting_scope** (string):  
    - **Description**: Specifies which context to sort by.  
    - **Allowed Values**: `"token"`, `"left"`, `"right"`  
    - **Default**: `"token"`

  - **offset** (integer):  
    - **Description**: When using token mode, the token offset to consider.  
    - **Default**: `0`

  - **case_sensitive** (boolean):  
    - **Description**: If `True`, the sort is case-sensitive.  
    - **Default**: `False`

  - **reverse** (boolean):  
    - **Description**: If `True`, sorts in descending order.  
    - **Default**: `False`

  - **backwards** (boolean):  
    - **Description**: If `True`, reverses the string (useful for right-to-left sorting).  
    - **Default**: `False`

  - **locale_str** (string):  
    - **Description**: ICU locale string for language-specific sorting.  
    - **Default**: `"en"`

- **Output**:  
  - **sort_keys**:  
    - A mapping of each line ID to its computed sort rank.
    
  - **token_spans**:  
    - A DataFrame indicating the token spans (using token indices) used to generate the sort keys.

---

## 4. KWIC Grouper Ranker

- **Description**:  
  Ranks concordance lines based on the frequency of a search term within a specified token attribute across a defined window (KWIC).  
  - Although designed primarily for ranking, its output (rank keys) can also be used for ordering.

- **Algorithm Type**:  
  - **Ranking (Ordering)**

- **Parameters**:  

  - **search_term** (string):  
    - **Description**: The term to search for within the tokens.

  - **tokens_attribute** (string):  
    - **Description**: Token attribute to search within.  
    - **Default**: `"word"`

  - **regex** (boolean):  
    - **Description**: If `True`, uses regular expression matching.  
    - **Default**: `False`

  - **case_sensitive** (boolean):  
    - **Description**: If `True`, performs a case-sensitive search.  
    - **Default**: `False`

  - **include_node** (boolean):  
    - **Description**: If `True`, includes tokens at the node (offset 0) in the search.  
    - **Default**: `False`

  - **window_start** (integer):  
    - **Description**: Lower bound of the window for token selection.

  - **window_end** (integer):  
    - **Description**: Upper bound of the window for token selection.

  - **count_types** (boolean):  
    - **Description**: If `True`, counts unique token types per line; otherwise, counts all matching tokens.  
    - **Default**: `True`

- **Output**:  
  - **rank_keys**:  
    - A mapping from each line ID to its ranking value (reflecting the occurrence count of the search term).

  - **token_spans**:  
    - A DataFrame identifying the token spans used in computing the rank.

---
