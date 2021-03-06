
.. ipython:: python
   :suppress:

   import numpy as np
   import pandas as pd
   import matplotlib as mpl
   import matplotlib.pyplot as plt

   plt.style.use('default')
   mpl.rcParams['axes.labelsize'] = 'large'
   mpl.rcParams['savefig.bbox'] = 'tight'
   mpl.rcParams['savefig.pad_inches'] = 0.1

   pd.options.display.max_rows = 15

==================
Recall performance
==================

First, load some sample data and create a merged DataFrame:

.. ipython:: python

    from psifr import fr
    df = fr.sample_data('Morton2013')
    data = fr.merge_free_recall(df)

Raster plot
~~~~~~~~~~~

Raster plots can give you a quick overview of a whole dataset. We'll look at
all of the first subject's recalls. This will plot every individual recall,
colored by the serial position of the recalled item in the list. Items near
the end of the list are shown in yellow, and items near the beginning of the
list are shown in purple. Intrusions of items not on the list are shown in red.

.. ipython:: python

    subj = fr.filter_data(data, 1)
    @savefig raster_subject.svg
    g = fr.plot_raster(subj).add_legend()

Serial position curve
~~~~~~~~~~~~~~~~~~~~~

We can calculate average recall for each serial position
using :py:func:`~psifr.fr.spc` and plot using :py:func:`~psifr.fr.plot_spc`.

.. ipython:: python

    recall = fr.spc(data)
    @savefig spc.svg
    g = fr.plot_spc(recall)

Using the same plotting function, we can plot the curve for each
individual subject:

.. ipython:: python

    @savefig spc_indiv.svg
    g = fr.plot_spc(recall, col='subject', col_wrap=5)

Probability of Nth recall
~~~~~~~~~~~~~~~~~~~~~~~~~

We can also split up recalls, to test for example how likely participants
were to initiate recall with the last item on the list.

.. ipython:: python

    prob = fr.pnr(data)
    prob

This gives us the probability of recall by output position (:code:`'output'`)
and serial or input position (:code:`'input'`). This is a lot to look at all
at once, so it may be useful to plot just the first three output positions.
We can plot the curves using :py:func:`~psifr.fr.plot_spc`, which takes an
optional :code:`hue` input to specify a variable to use to split the data
into curves of different colors.

.. ipython:: python

    pfr = prob.query('output <= 3')
    @savefig pnr.svg
    g = fr.plot_spc(pfr, hue='output').add_legend()

This plot shows what items tend to be recalled early in the recall sequence.
