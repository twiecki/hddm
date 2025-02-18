.. index:: Tutorial
.. _chap_tutorial_python:

Specifying Models in Python
============================

As an alternative to the configuration file, HDDM offers model
specification directly from Python. For this, you first import hddm:

.. literalinclude :: ../../hddm/examples/simple_model.py
   :lines: 1

Next, we have to load the data into Python. HDDM expects a NumPy
structured array or a pandas_ DataFrame. You can load the data from a
csv files as follows:

.. literalinclude :: ../../hddm/examples/simple_model.py
   :lines: 4

HDDM requires the data frame to be in a specific format. It has to
have an RT column called 'rt', a column for which response was made
called 'response' and, if you have multiple subjects, a column
containing subject ids (these have to be integers) called 'subj_idx'.

After you loaded the data you can create the model object:

.. literalinclude :: ../../hddm/examples/simple_model.py
   :lines: 7

This model object contains your data, the corresponding model
specification and a number of methods to perform inference, do model
analysis etc. You may then sample from the posterior distribution by
calling:

.. literalinclude :: ../../hddm/examples/simple_model.py
   :lines: 10

Depending on the model and amount of data this can take some
time. After enough samples were generated, you may want to print some
statistics on the screen:

.. literalinclude :: ../../hddm/examples/simple_model.py
   :lines: 13

You can currently generate two plots to examine model fit. To see if your chains converged and what the posteriors for each
parameter looks like you can call:

.. literalinclude :: ../../hddm/examples/simple_model.py
   :lines: 16

To see how well the model fits the RT distributions we analyze the
posterior predictive pdf:

.. literalinclude :: ../../hddm/examples/simple_model.py
   :lines: 17

This function evaluates the DDM likelihood function from samples of
the posterior and plots it on top of the observed RTs. The solid blue
line thus represents the mean expected RT distribution and its width
(in transparent light blue) the model uncertainty in that region of
the RT distribution. The closer the two distributions look like, the
better the fit. Note that the RT distribution for the second response
is mirrored on the y-axis.

The final program then looks as follows:

.. literalinclude :: ../../hddm/examples/simple_model.py

More complex models can be generated by specifying different
parameters during model creation. Say we wanted to create a model where
each subject receives its own set of parameters which are themselves
sampled from a group parameter distribution. Moreover, as in the
example above, we have two trial types in our data, easy and
hard. Based on previous research, we assume that difficulty affects
drift-rate 'v'. Thus, we want to fit different drift rate parameters
for those two conditions while keeping the other parameters fixed
across conditions. Finally, we want to use the full DDM with
inter-trial variability for drift, non-decision time ter and starting
point z. The full model requires integration of these variability
parameters and is hence much slower. The model creation and sampling
then might look like this (assuming we imported hddm and loaded the
data as above):

>>> model = hddm.HDDM(data, include=('sv', 'sz', 'st'), bias=True, depends_on={'v':'difficulty'})
>>> model.sample(10000, burn=5000)

.. _pandas: http://pandas.pydata.org/
