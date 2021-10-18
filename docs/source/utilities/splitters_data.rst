.. # Copyright (C) 2020-2021 Intel Corporation
.. # SPDX-License-Identifier: Apache-2.0

.. _data_splitting:

*****************
Dataset Splitters
*****************


|productName| allows you to specify custom data splits **for simulation runs on a single dataset**.

You may apply data splitters differently depending on the |productName| workflow that you follow. 


STEP 1: Define How to Split the Data with Native Python API Functions
=====================================================================

Predefined |productName| data splitters functions are as follows:

- ``openfl.plugins.data_splitters.EqualNumPyDataSplitter`` (default)
- ``openfl.plugins.data_splitters.RandomNumPyDataSplitter``
- ``openfl.component.aggregation_functions.LogNormalNumPyDataSplitter`` , which assumes the ``data`` argument as ``np.ndarray`` of integers (labels)
- ``openfl.component.aggregation_functions.DirichletNumPyDataSplitter`` , which assumes the ``data`` argument as ``np.ndarray`` of integers (labels)

Alternatively, you can create an implementation of :class:`openfl.plugins.data_splitters.NumPyDataSplitter` (`link <https://github.com/intel/openfl/blob/develop/openfl/utilities/data_splitters/numpy.py>`_) and pass it to the :code:`FederatedDataset` function as either ``train_splitter`` or ``valid_splitter`` keyword argument.


STEP 2: Define the Shard Descriptor
===================================

Apply the splitting function on your data to perform a simulation. 

``NumPyDataSplitter`` requires a single ``split`` function. The :code:`split` function returns a list of indices which represents the collaborator-wise indices groups.

This function receives ``data`` - NumPy array required to build the subsets of data indices. It could be the whole dataset, or labels only, or anything else.


.. code-block:: python

    X_train, y_train = ... # train set
    X_valid, y_valid = ... # valid set
    train_splitter = RandomNumPyDataSplitter()
    valid_splitter = RandomNumPyDataSplitter()
    # collaborator_count value is passed to DataLoader constructor
    # shard_num can be evaluated from data_path
    train_idx = train_splitter.split(y_train, collaborator_count)[shard_num]
    valid_idx = valid_splitter.split(y_valid, collaborator_count)[shard_num]
    X_train_shard = X_train[train_idx]
    X_valid_shard = X_valid[valid_idx]

.. note::

    By default, the data is shuffled and split equally. See :class:`openfl.plugins.data_splitters.EqualNumPyDataSplitter` (`link <https://github.com/intel/openfl/blob/develop/openfl/utilities/data_splitters/numpy.py>`_) for details.
