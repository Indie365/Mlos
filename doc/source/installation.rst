Installation
============

Development
-----------

The development environment for MlosCore uses ``conda`` to ease dependency management.

  .. note::
    See Also: `conda install instructions <https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html>`_

    Note: to support Windows we rely on some pre-compiled packages from `conda-forge` channels, which increases the `conda` solver time during environment create/update.

    To work around this the (currently) experimental `libmamba` solver can be used.

    See `<https://github.com/conda-incubator/conda-libmamba-solver#getting-started>`_ for more details.


0. Create the `mlos_core` Conda environment.

  .. code-block:: shell

    conda env create -f conda-envs/mlos_core.yml

  or

  .. code-block:: shell

    # This will also ensure the environment is update to date using "conda env update -f conda-envs/mlos_core.yml"
    make conda-env


1. Initialize the shell environment.

  .. code-block:: shell

    conda activate mlos_core

2. Run the BayesianOptimization.ipynb notebook.

Distributing
------------

1. Build the *wheel* file(s).

  .. code-block:: shell

    make dist

2. Install it (e.g. after copying it somewhere else).

  .. code-block:: shell

    # this will install just the optimizer component with emukit support:
    pip install dist/mlos_core-0.0.4-py3-none-any.whl[emukit]

    # this will install just the optimizer component with skopt support:
    pip install dist/mlos_core-0.0.4-py3-none-any.whl[skopt]

  .. code-block:: shell

    # this will install both the optimizer and the experiment runner:
    pip install dist/mlos_bench-0.0.4-py3-none-any.whl
