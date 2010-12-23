================
Classifier nodes
================

New in MDP 2.6 is the ``ClassifierNode`` base class which offers a simple
interface for creating classification tasks. Usually, one does not want to use
the classification output in a flow but extract this information independently.
Most classification nodes will therefore simply return the identity function on
``execute``; all classification work is done with the new methods ``label``,
``prob`` and ``rank``.

As a first example, we will use the ``GaussianClassifierNode``.
::

    >>> gc = mdp.nodes.GaussianClassifierNode()
    >>> gc.train(mdp.numx_rand.random((50, 3)), +1)
    >>> gc.train(mdp.numx_rand.random((50, 3)) - 0.8, -1)
	
We have trained the node and assigned the labels +1 and -1 to the sample points.
Note that in this simple case we do not need to give a label to each individual point,
when only a single label is given, it is assigned to the whole batch of features.
However, it is also possible to use the more explicit form::

    >>> gc.train(mdp.numx_rand.random((50, 3)), [+1] * 50)
	
We can then retrieve the most probable labels for some testing data,
::

    >>> test_data = mdp.numx.array([[0.1, 0.2, 0.1], [-0.1, -0.2, -0.1]])
    >>> gc.label(test_data)
    [1, -1]
	
and also get the probability for each label.
::

    >>> gc.prob(test_data)
    [{-1: 0.21013407927789607, 1: 0.78986592072210393},
     {-1: 0.99911458988539714, 1: 0.00088541011460285866}]


Finally, it is possible to get the ranking of the labels, starting with the likeliest.
::

    >>> gc.rank(test_data)
    [[1, -1], [-1, 1]]
	

New nodes should inherit from ``ClassifierNode`` and implement the ``_label`` and ``_prob``
methods. The public ``rank`` method will be created automatically from ``prob``.