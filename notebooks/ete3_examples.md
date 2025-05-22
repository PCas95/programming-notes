# Notes on Phylogeny Tree Visualisation using Python3's ETE3 Library

> Personal code and notes plus ChatGPT-generated examples

*How to visualise a maximum likelyhood phylogeny tree with ETE3 python*

To visualize a maximum likelihood (ML) phylogenetic tree using ETE3 in Python, you'll need to have your tree (usually in Newick format) already inferred — typically from a program like RAxML, IQ-TREE, or PhyML.

## Step-by-Step Guide

1. **Install ETE3**

```sh
pip install ete3
```

You may also need additional dependencies, like PyQt5 for GUI visualization (recommended):

```sh
pip install PyQt5
```

2. **Load Your ML Tree (Newick Format)**

```py
from ete3 import Tree

# Load the tree from a Newick file
t = Tree("ml_tree.nwk")
```

3. **Basic Visualization (Interactive GUI)**

```py
t.show()
```

This pops up a GUI window where you can interact with the tree.

4. **Add Node Styles (Optional, for ML trees)**

Maximum likelihood trees often come with bootstrap values or branch lengths — you can display these.

```py
from ete3 import TreeStyle, NodeStyle, TextFace

# Customize the look of the tree
ts = TreeStyle()
ts.show_leaf_name = True
ts.show_branch_length = True
ts.show_branch_support = True  # Show support values (like bootstrap)

# Example: Style internal nodes
for node in t.traverse():
    nstyle = NodeStyle()
    nstyle["size"] = 0  # No circle on nodes
    node.set_style(nstyle)
    
    # If bootstrap values are present
    if not node.is_leaf() and node.support:
        face = TextFace(f"{node.support:.1f}", fsize=10)
        node.add_face(face, column=0, position="branch-top")

# Show the tree
t.show(tree_style=ts)
```

5. **Export to Image File**

```py
t.render("ml_tree.png", w=800, tree_style=ts)
```

##  Pro Tips

If your ML tree is very large (hundreds or thousands of taxa), rendering in `svg` is often more efficient than `PNG`:

```py
t.render("ml_tree.svg", tree_style=ts)
```

You can root, unroot, ladderize, collapse branches, or even manipulate node annotations using ETE3.

> Note that rooting and other extensive tree manipulation would be better managed using an automatic standadised command from an established tool, like IQ-TREE.

## Real World Example

I've noted down below the commands used to visualise 4 pruned ML trees. I've used a Conda environment originally prepared for the Treemmer tool, which contained the ETE3 library, and added `PyQt5`.

```bash
$ conda activate treemmer
$ python3
```

```py
>>> from ete3 import Tree
>>> ml070 = Tree("/home/IZSNT/p.castelli/Documents/work/canonical_SNPs-20240521/iqtree-ML-20250515_131126504.nwk_trimmed_tree_RTL_0.7")
>>> ml080 = Tree("/home/IZSNT/p.castelli/Documents/work/canonical_SNPs-20240521/iqtree-ML-20250515_131126504.nwk_trimmed_tree_RTL_0.8")
>>> ml090 = Tree("/home/IZSNT/p.castelli/Documents/work/canonical_SNPs-20240521/iqtree-ML-20250515_131126504.nwk_trimmed_tree_RTL_0.9")
>>> ml095 = Tree("/home/IZSNT/p.castelli/Documents/work/canonical_SNPs-20240521/iqtree-ML-20250515_131126504.nwk_trimmed_tree_RTL_0.95")
>>> ml070.show()
>>> ml080.show()
>>> ml090.show()
>>> ml095.show()
```

