# Height vs depth of binary tree

![height vs depth of binary tree](https://i.stack.imgur.com/8yPi9.png)


* height of a complete binary tree with n nodes is at most `log2(N)`

* total number of nodes of tree with height h
`2^h+1 -1` or `2^l - 1`

* number of nodes in each level
`2^l-1`

* number of nodes in each height
`2^h`

* height -> level
`h -> l-1`

# In, pre and post order

![eg](https://www.cs.cmu.edu/~adamchik/15-121/lectures/Trees/pix/tree1.bmp)

```
    PreOrder - 8, 5, 9, 7, 1, 12, 2, 4, 11, 3
    InOrder - 9, 5, 1, 7, 2, 12, 8, 4, 3, 11
    PostOrder - 9, 1, 2, 12, 7, 5, 3, 11, 4, 8
    LevelOrder - 8, 5, 4, 9, 7, 11, 1, 12, 3, 2 
```

![in pre and post](https://www.cs.cmu.edu/~adamchik/15-121/lectures/Trees/pix/traversals.bmp)

# Binary search tree

![binary search tree](https://www.cs.cmu.edu/~adamchik/15-121/lectures/Trees/pix/pix03.bmp)

## Deletion for nodes with two children

* Replace the node being deleted with the largest node in the left subtree and then delete that largest node. By symmetry, the node being deleted can be swapped with the smallest node is the right subtree. 

![deleting a node](https://www.cs.cmu.edu/~adamchik/15-121/lectures/Trees/pix/del02.bmp)

