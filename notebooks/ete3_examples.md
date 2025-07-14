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

## Additional Manipulation

### Root an unrooted tree

```py
# 1/ on a specific node set as outgroup
t.set_outgroup('2025.EXT.0529.1200.500')

# 2/ middlepoint rooting
midpoint = t.get_midpoint_outgroup()
t.set_outgroup(midpoint)
```

### Pruning and Detaching Leaves

```py
leaves_to_drop = ['2025.EXT.0529.1200.500', '2024.EXT.0725.2046.110', '2024.EXT.0725.2137.140']
# Remove leaves
for leaf_name in leaves_to_drop:
	node = t.search_nodes(name=leaf_name)
	if node:
		node[0].detach()
```

## In-Depth Guide

A tree is a graph connected in an acyclic way. In ETE3, trees are data structures represented with nodes. The top-most node is the **root**, terminal nodes are **leaves**, while inner or **internal nodes** are all nodes that have **child nodes**.

Any tree topology can be represented as a succession of nodes connected in a hierarchical way. Thus, ETE3 makes no distinction between nodes and the full tree, since the whole tree can be represented with its root node. This means that:

- any internal node can be treated as the root of a subtree, allowing easy tree manipulation, partitioning and concatenation;
- `Tree` and `TreeNode` classes are synonymous;
- attributes and methods are the same for both.

> In this document I'll use the "Tree node" expression to refer to the Python object, without differences between nodes and whole trees. 

> When a tree is loaded from external sources, a pointer to the top-most node is returned. This is called the "tree root", and it will exist even if the tree is conceptually considered as unrooted.

### Tree Attributes

Tree nodes have a total of 5 basic attributes, 2 of which are used to establish their position in the tree.

| Attribute           | Description                          |
| ------------------- | ------------------------------------ |
| `TreeNode.up`       | pointer to a parent node (if any)    |
| `TreeNode.children` | returns a **list** of children nodes |
| `TreeNode.dist`     | node distance from its parent        |
| `TreeNode.support`  | support value (bootstrap)            |
| `TreeNode.name`     | custom node name (label/sample)      |

### Tree Methods

| Method               | Description                            |
| -------------------- | -------------------------------------- |
| `TreeNode.is_leaf()` | returns `True` if node has no children |
| `TreeNode.is_root()` | returns `True` if node has no parent   |
| `TreeNode.get_tree_root()` | returns the root node (top-most node within the same tree structure of the node) |
| `TreeNode.show()`    | Explore node graphically using a GUI   |

Additionally:

- `print(node)` will print a text-based representation of the tree topology under `node`;

```py
>>> t = Tree("(A:1,(B:1,(E:1,D:1)Internal_1:0.5)Internal_2:0.5)Root;", format=1)
>>> print(t)

   /-A
--|
  |   /-B
   \-|
     |   /-E
      \-|
         \-D
```

- `len(TreeNode)` returns the total number of leaves under `node`;
- the statement `if node in tree` returns `True` if `node` is a leaf under `tree`;
- the statement `for leaf in node` iterates over all leaves under `node`.

### Tree Browsing / Traversing

"Tree Browsing" (or "Tree Traversing") refers to the basic operation of visiting nodes within a tree (tree-visiting algorithms).

There are different ways to traverse a tree structure, depending on the order in which children nodes are visited. ETE3 implements the 3 most common strategies: "preorder", "levelorder" and "postorder". In all cases, this operation will traverse the whole tree.

- **preorder**:
	1. Visit the root
	2. Traverse the left subtree
	3. Traverse the right subtree
- **postorder**:
	1. Traverse the left subtree
	2. Traverse the right subtree
	3. Visit the root
- **levelorder** (default): all nodes present at the same level are traversed completely before traversing the lower level

**Examples:**

```py
>>> t = Tree("((C:1,F:1)Internal_3:0.5,(B:1,(E:1,D:1)Internal_1:0.5)Internal_2:0.5)Root;", format=1)
>>> print(t)

      /-C
   /-|
  |   \-F
--|
  |   /-B
   \-|
     |   /-E
      \-|
         \-D
>>> for node in t.traverse("preorder"): print(node.name)
... 
Root
Internal_3
C
F
Internal_2
B
Internal_1
E
D
>>> for node in t.traverse("postorder"): print(node.name)
... 
C
F
Internal_3
B
E
D
Internal_1
Internal_2
Root
>>> for node in t.traverse(): print(node.name)
... 
Root
Internal_3
Internal_2
C
F
B
Internal_1
E
D
```

> Additional traversing functions can be implemented by using Tree node attributes to visit the Tree node.

#### Advanced Traversing

##### `is_leaf_fn` Argument

Most traversing functions support the `is_leaf_fn` argument, *i.e.* a pointer to a user-defined function which:

- accepts a node object as first argument;
- returns a Boolean value (`True` if node should be considered a leaf node).

This allows the user to provide a custom function to decide whether a node is a match or not.

An example usage of this approach is to find the first matching nodes in a given tree that match a custom set of criteria, without browsing the whole tree structure.

**Example:**

To get all the deepest nodes with branch length > 1:

```py
def distant_node(node):
	if node.dist > 1:
		return True
	else:
		return False

for leaf in t.iter_leaves(is_leaf_fn=distant_node):
	print(leaf)
```

##### Iterators

Methods starting with `get_` return results as a list. This means that the whole tree structure will be browsed before returning the final list. In large trees, the performance of loop functions can be increased by performing the browsing process using iterators (`iter_` methods) instead of `get_` methods. In fact, most `get_` methods have their homologous iterator functions.

Iterators are only applicable for looping and process one step at a time, returning one value per iteration. This makes no differences in the final result, but it may increase performace in loops.

##### Search Nodes by Attribute

Some methods allow to search nodes in the tree structure based on node attributes:

| Method                            | Description |
| --------------------------------- | ----------- |
| `t.search_nodes(attr=value)`      | Returns a list of nodes in which `attr` is equal to `value` (example syntax: `name='A'`) |
| `t.iter_search_nodes(attr=value)` | Corresponding iterator function of `t.search_nodes()` |
| `t.get_leaves_by_name(name)`      | Returns a list of leaf nodes matching a given name. Only leaves are browsed |
| `t.get_common_ancestor([node1, node2, node3])` | Return the first internal node grouping the list of nodes |

> The shortcut syntax `t&"name"` returns the first node whose name is "`name`" and that is under the tree "`t`", which is one of the most common tasks in tree traversing.

Monophyly is the condition that makes a taxonomic group a "clade", *i.e.* a group of organisms that:

- contains all descendants of a common ancestor, without exception;
- includes its most recent ancestor and excludes all non-descendants from that ancestor.

### Check Monophyly

ETE3 provides the `TreeNode.check_monophyly()` method to check if an argument group is monophyletic:

```py
>>> t =  Tree("((((((a, e), i), o),h), u), ((f, g), j));")
>>> print(t)

                  /-a
               /-|
            /-|   \-e
           |  |
         /-|   \-i
        |  |
      /-|   \-o
     |  |
   /-|   \-h
  |  |
  |   \-u
--|
  |      /-f
  |   /-|
   \-|   \-g
     |
      \-j
>>> print(t.check_monophyly(values=["a", "e", "i", "o", "u"], target_attr="name"))
(False, 'polyphyletic', {Tree node 'h' (0x7f272fa718a)})
>>> print(t.check_monophyly(values=["a", "e", "i", "o"], target_attr="name"))
(True, 'monophyletic', set())
```

> In the first example the returned value is `False`, because the "h" letter is not in the group being checked for monophyly, *i.e.* the first condition is not respected.
>
> The second attempt returns `True` because **all** the items in the list have a common ancestor, **included** in the group (unnamed node) and all non-descendants from that ancestor are **excluded**.




