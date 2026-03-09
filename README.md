# mIHC Panel Planner

A lightweight computational tool for designing multiplex immunohistochemistry (mIHC) panels and optimizing fluorophore channel assignment.

Multiplex IHC experiments often require careful planning of fluorophore combinations to minimize spectral overlap while preserving reliable signal detection. This tool helps researchers organize marker information and generate suggested Opal channel assignments based on practical experimental constraints.

The goal is not to replace experimental optimization but to provide a structured starting point for panel design.

---

# Background

Multiplex immunohistochemistry (mIHC) enables simultaneous detection of multiple markers within a single tissue section. However, designing a multiplex panel can be challenging due to several biological and technical constraints.

Researchers must consider:

* differences in marker expression strength
* subcellular localization (nucleus, cytoplasm, membrane)
* spectral separation between fluorophores
* segmentation requirements for image analysis
* previously validated channel-marker combinations
* marker pairs that require clear co-expression interpretation

In practice, these decisions are often made manually and rely heavily on experience and trial-and-error. As the number of markers increases, panel design becomes increasingly complex.

This tool was created to make this reasoning more structured and reproducible, allowing users to quickly explore channel combinations while incorporating practical experimental considerations.

---

# Features

* interactive marker input
* automatic fluorophore channel suggestions
* spectral spacing optimization
* expression-strength-aware channel assignment
* segmentation marker prioritization
* fixed channel constraints
* checkpoint / co-expression marker pair handling
* morphology-aware channel tolerance

Supported Opal channels:

480, 520, 540, 570, 620, 650, 690, 780

---

# Design Principles

This tool was designed to reflect practical reasoning used in real multiplex immunohistochemistry panel design rather than relying only on marker intensity.

## Segmentation-aware channel assignment

Opal 480 is often the most suitable channel for segmentation-related markers because it provides clear signal and can be used to help define tissue structure or cellular boundaries during image analysis. For this reason, segmentation markers are preferentially assigned to Opal 480 whenever possible.

In rare cases, Opal 780 may also be considered as an alternative segmentation channel.

---

## Fixed channel support

Some markers consistently perform best in a specific channel based on previous optimization experiments. To preserve validated staining performance, the tool allows fixed channel assignment.

This means that if a marker has already been confirmed to work best in a particular fluorophore channel, it can be locked to that channel instead of being reassigned automatically.

For example, CD3 is often experimentally validated to perform well in Opal 620. In such cases the channel can be fixed to maintain consistency across experiments.

---

## Co-expression sensitive marker pairs

Certain marker pairs are biologically interpreted together, and placing them too close spectrally may lead to signal leakage or bleed-through. This can compromise interpretation of co-expression patterns.

For example, markers such as F4/80 and iNOS may be used to characterize macrophage states. If these markers are assigned to nearby channels, spectral leakage could falsely suggest co-expression or obscure true expression patterns.

When such marker pairs are specified, the tool prioritizes assigning them to channels that are further apart in the spectrum.

---

## Morphology-aware relaxation

Markers with similar subcellular localization do not always require strict spectral separation if they label cell types with clearly distinguishable morphology.

For instance, a macrophage membrane marker and CD3 are both membrane-associated markers, but macrophages and T cells often have distinct morphology and spatial organization. Because of this, nearby channel assignment may still be interpretable.

The tool therefore allows relaxation of channel-spacing penalties for such morphology-distinguishable marker pairs.

---

# Algorithm Overview

The panel suggestion process follows several steps:

1. Collect marker information including name, expression strength, and cellular localization.

2. Apply fixed-channel constraints for markers that must remain in specific channels.

3. Prioritize segmentation markers for suitable channels (typically Opal 480).

4. Evaluate spectral distance between candidate channel combinations.

5. Apply penalties for:

   * channels placed too close together
   * sensitive co-expression marker pairs
   * markers with similar localization that may interfere with interpretation

6. Apply relaxation rules when morphology differences allow nearby channels.

7. Rank candidate channel combinations based on overall spectral separation and rule satisfaction.


# Usage

Run the panel planner:

```
python mIHC_panel_planner.py
```

Steps:

1. Define the number of markers
2. Enter marker information
3. Specify optional constraints such as fixed channels or checkpoint pairs
4. Generate candidate multiplex panels

The tool will output channel combinations that maximize spectral spacing while respecting biological constraints.

---

# Example

Example markers:

| Marker | Strength | Location  |
| ------ | -------- | --------- |
| PanCK  | strong   | cytoplasm |
| CD3    | medium   | membrane  |
| CD68   | strong   | cytoplasm |
| MMP9   | strong   | cytoplasm |

Possible suggested panel:

540 – 620 – 690 – 520

This configuration distributes strong signals and maximizes spectral distance between channels.

---

# Recommendation

This tool becomes particularly useful when designing panels with **more than five markers**, where manual channel assignment becomes difficult and the risk of spectral interference increases.

---

# Limitations

The tool provides logical suggestions based on common experimental reasoning, but final panel validation should always be confirmed experimentally.

Factors such as antibody affinity, staining order, tissue autofluorescence, and imaging settings may still influence the final result.

---

# Author

Long Nguyen
Institute of Biotechnology
National Taiwan University

Research interests:

Spatial biology
Multiplex tissue imaging
Tumor immune microenvironment
Translational biomarker discovery
