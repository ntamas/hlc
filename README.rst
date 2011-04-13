About
=====

This is an alternative Python implementation of hierarchical link communities
in complex networks, published by Ahn et al [1]_. The implementation is based
on the igraph_ module of Python and is somewhat faster than the
`original Python implementation`_. Of course it cannot compete with the C++
version yet -- pushing down the Jaccard similarity calculation to igraph_'s
C core would probably help in the long run.

.. _igraph: http://igraph.sourceforge.net

.. _original Python implementation: https://github.com/bagrow/linkcomm/tree/master/python

Usage
=====

Should be self-explanatory, just run ``./hlc.py`` and you should get a
short help message.

To run a single clustering on a graph, selecting the threshold automatically::

    $ ./hlc.py filename

The implementation supports weighted graphs and converts directed graphs to
undirected ones automatically. However, weights are lost when converting a
directed graph to an undirected one unless you are using igraph 0.6 or above.

Results are written to the standard output, one cluster per line. Communities
with two nodes only are excluded. Use the ``-t`` switch to specify the
similarity threshold explicitly and ``-s`` to control the minimum size of
clusters to be reported.

Quick benchmark results
=======================

This implementation on the University of South Florida word association
network (weighted, converted to undirected)::

    $ time ./hlc.py data/freeassoc.txt >freeassoc_clusters.txt
    Processing data/freeassoc.txt
    Calculating clusters, please wait...
    Threshold = 0.200000
    D = 0.041974

    real    0m15.246s
    user    0m15.010s
    sys     0m0.220s

The original Python implementation::

    $ time ./link_clustering.py data/freeassoc.txt -d ' '
    # loading network from edgelist...
    clustering...
    computing similarities...
    # D_max = 0.041974
    # S_max = 0.200000

    real    2m32.575s
    user    2m31.770s
    sys     0m0.750s

Note that both the threshold and the D value is the same, which is
reassuring. Let's comparing the number of clusters as well::

    $ wc -l freeassoc_clusters.txt
    7676 freeassoc_clusters.txt
    $ awk '{ if (NF > 3) print }' freeassoc_maxS0.2*.comm2nodes.txt | wc -l
    7676

(Note that the result file from the original implementation includes
communities with only two nodes as well, and the first column in the result
is the index of the community, that's why we needed that ``awk`` magic to
count the number of communities with at least 3 vertices).

References
==========

.. [1] Ahn YY, Bagrow JP and Lehmann S: Link communities reveal multiscale
   complexity in networks. *Nature* **466**, 761 (2010).

