# Decision Tree Classifier - A New Look - Using Kmeans Clustering Inertia as a Evaluation Metric

In this IPython Notebook, a decision tree classifier is implemented and tested on the [Avila dataset](https://archive.ics.uci.edu/ml/datasets/Avila) obtained from [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/index.php). BUT, a ***completely different evaluation criterion*** is used at each non-leaf node of the decision tree when choosing a decision attribute at that node.

There are a few **key** differences from a traditional decision tree.

## Creation of the Node

At each node, 
*   First, check the number of unique classes present in the data that is supplied to that node (the whole dataset if the node is root or a subset of the data after splitting the parent node).
    *   If the number of unique classes is 1, then the node is pure and is a leaf node. The class that will be classified by this leaf node will be that single unique class.
    *   Else if the number of unique classes is more than 1, then the node is impure and can be split. The class that will be classified by this non-leaf node will be the class with the maximum frequency (this information is required in case the node is pruned later and is made a leaf node).
        *    Let the number of unique classes be n where n > 1
        *    Additionally, there is another parameter δ which denotes the ***number of decision attributes*** to be used at any node. This is an important key difference between this decision tree and traditional decision trees. In a traditional decision tree only one attribute could be used as a deciding attribute at any node, but in this decision tree which uses a new evaluation metric, any number of attributes can be used to collectively make a decision. This ***increases the power of the decision tree***.  
        *    The new evaluation metric is nothing but the ***inertia*** of the KMeans Clustering.

```
# This is a pseudo-code to explain the working of this evaluation metric
for all possible combinations of attributes of the data of size δ:
    # This means that if the data has attributes say x, y, z and δ = 3=2,
    # then all possible combinations of attributes of size 2 would be xy, yz, and zx.

    Perform KMeans Clustering on the data using a combination of attributes with n being the number of clusters.

    # From the clustering model we can obtain the inertia and cluster centres.
    Keep track of the combination of attributes and the corresponding KMeans clustering model for which the inertia is the minimum.

# Now we have obtained a clustering model for a combination of attributes for which the inertia is minimum.
Using this model obtain the cluster centres and the corresponding data points which belong to each cluster centre.
Store this information with the node (to be used later when the node is split).
```

## Splitting of the Node

After the node is constructed, as usual if the node is impure it has to be split. BUT, in this decision tree the splitting is done based on the clusters obtained when the node was formed.

We split the impure node into n(number of clusters, also the number of unique classes at the node) nodes. Each child node is sent the data points corresponding a particular cluster centre and then the procedure is repeated for all the children. This constructs the decision tree. 

## Tree traversal for Decision Making

Once the tree is constructed, the traversal is very simple.

To classify a instance of the data say K,

1.   Starting at the root, look at the children of the root. We have already stored the decision attributes combination to be used at the root and the cluster centres corresponding to each child.
2.   Using the values of decision attributes combination in I, check which cluster centre is closest to K.
3.   Then the data point K will be sent to that child whose cluster centre is closest to K. Now go back to Step 1 and repeat.
4.   This process is repeated until a leaf is reached and then K is said to belong to the class corresponding to that leaf.
