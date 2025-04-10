# Concordance Views

The `view()` method of an `AnalysisTreeNode` returns a dictionary representing a concordance view. This view is a structured, JSON-serializable summary of the concordance data at a given node in the analysis tree. The output is organized into several keys, each providing specific information about the concordance lines and any additional processing (ordering, grouping, ranking, token spans) that has been applied.

Below is the exact specification of the output:

---

## 1. `selected_lines`

- **Type:** `List[int]`
- **Description:**  
  A list of line numbers (indices from the original concordance metadata) that are visible in the view.
    - **If the node defines its own `selected_lines`:** That list is used.
    - **Otherwise:** The view inherits `selected_lines` from the nearest ancestor that has them.
    - **Fallback:** If no such ancestor exists, it defaults to all line numbers in the concordance metadata.

---

## 2. `ordering`

- **Type:** `List[int]`
- **Description:**  
  An ordered list of the visible line numbers. This ordering is determined by:
    - The node’s own `ordering_result["sort_keys"]`, if present.
    - Or, inherited from the nearest ancestor that has an ordering result.
  - **Filtering:** Only line numbers present in the current node’s `selected_lines` are included.
  - **Default:** If no ordering is defined, it falls back to the natural order (using the line number as the sort key).

---

## 3. `grouping` (Optional)

- **Type:** `List[dict]` (for partitions) or a nested dictionary (for clusters)
- **Description:**  
  Included only if a grouping algorithm has been applied to the node. The structure depends on the grouping strategy:
    - **Partitions (Flat List):**  
      Each partition is represented as a dictionary with:
        - **`id`**: Unique integer identifier.
          - **`label`**: Display label.
          - **`line_ids`**: Ordered list of line numbers belonging to the partition (sorted according to the global ordering).
          - *(Optional)* **`prototypes`**: List of prototypical line numbers.
          - *(Optional)* **`info`**: Additional information (as a dictionary).
    - **Clusters (Nested Structure):**  
      Clusters are represented as a hierarchical (tree) structure. Each cluster dictionary contains the same keys as a partition plus:
        - **`children`**: A list of sub-cluster dictionaries.

---

## 4. `global_info` (Optional)

- **Type:** `Dict[str, Any]`
- **Description:**  
  A dictionary containing overall information about the view. For example:
    - Differentiation information from the ordering algorithm (e.g., counts of adjacent line pairs that were differentiated by each ordering algorithm).
      - Any additional information stored in the node (from `self.info`).

---

## 5. `line_info` (Optional)

- **Type:** `Dict[int, dict]`
- **Description:**  
  A mapping from each visible line number to per-line information.
- **Source:** Derived from the `rank_keys` produced by ranking algorithms (under `ordering_result["rank_keys"]`).
- **Filtering:** Only includes entries for line numbers present in `selected_lines`.

---

## 6. `token_spans` (Optional)

- **Type:** `List[dict]`
- **Description:**  
  A list of token span objects used to mark tokens in a KWIC (Key Word In Context) display. Each token span dictionary includes:
    - **`line_id`**: The line number in which the span occurs.
    - **`start_id_in_line`**: The starting token id (inclusive) relative to the line. 
    - **`end_id_in_line`**: The ending token id (inclusive) relative to the line.
    - **`category`**: A string indicating the mark (e.g., `"A"`).
    - **`weight`**: A numerical weight, typically in the range `[0, 1]`.

---

## 7. `node_type`

- **Type:** `str`
- **Description:**  
  A string indicating the type of the node (e.g., `"subset"`, `"arrangement"`).

---

## Additional Notes

- **Serialization:**  
  The entire view is designed to be JSON-serializable.
  
- **Mandatory vs. Optional:**  
    - The keys `selected_lines` and `ordering` are always present.
    - The keys `grouping`, `global_info`, `line_info`, and `token_spans` are optional and are included only if relevant algorithms (grouping, ranking, etc.) have been applied to the node.
  
- **Ordering Details:**  
  The ordering list sorts the lines based on the sort keys computed by ordering algorithms. Ties are handled in a stable manner to maintain consistency.
  
- **Token Spans:**  
  When present, token spans provide precise information for marking specific segments of tokens within each line, enhancing the KWIC display for further visualization.

---

This specification outlines the complete structure and content of a Concordance View as generated by FlexiConc. Use it as a reference for understanding the output and for integrating or visualizing concordance data in your applications.