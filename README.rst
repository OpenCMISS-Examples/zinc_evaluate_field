#################################
Load A Model And Evaluate A Field
#################################

A basic-level tutorial example of a script using the OpenCMISS-Zinc library from Python without a user interface. This simple example opens a model and evaluates and prints values of a field in each element of it.

Such non-graphical scripts have many applications for researchers and developers, including:

* extracting very particular results from a model
* constructing or converting models
* batch processing, particularly for long-running operations
* using Zinc as a server, sending graphics to a remote client for display

What You Need
=============

You need to install Python, the OpenCMISS-Zinc library, and PyZinc (Python bindings to Zinc) as described elsewhere.

You require the following two files. Right-click on these links and download the text files to your
machine:

 * :download:`Example PyZinc script <zinc_evaluate_field.py>`
 * :download:`Example Data <triquadratic_heart60.exfile>`

The Script
==========

This example script loads a heart model (a 60 element mesh with a field 'coordinates' giving its geometry), and evaluates and prints the coordinates in the centre of each element:

.. literalinclude:: zinc_evaluate_field.py
  :linenos:
  :start-after: # zinc_evaluate_field start
  :end-before: # zinc_evaluate_field end

The import section of a Zinc application needs to import the context module and construct a *Context* object. From it all other Zinc objects are obtained. We also need to import any symbolic constants we wish to use, such as status ``OK`` (the successful return value for any Zinc function that does not return an object) which we have renamed here to ``ZINC_OK``.

From the context we get the default *Region*. A Region stores *Domains* (*Nodesets* i.e. sets of points, *Meshes* i.e. sets of elements defining an n-D space) which define the space of a model, and *Fields* mapping the domain locations to values such as their geometric coordinates and other quantities. Regions can have named child regions to build a hierarchy of models like a directory tree in a file system, however this example only uses the top region. We read the model file into the region which defines all the elements and fields making it up.

Zinc provides access to the fields and domains making up the model via each region's *Fieldmodule*. To evaluate the coordinates in the elements we need to obtain the coordinate field; we happen to know that the example model defines a field called 'coordinates' for this purpose. To evaluate fields we need to create a *Fieldcache*, where we will specify the location in the domain to evaluate the field at, and which is also responsible for caching intermediate values for more efficient evaluation.

Lines 11..13 obtain the 3-D mesh from the Fieldmodule and create an iterator for looping over its elements, then gets the first element. Note that Zinc regions are currently limited to having exactly one mesh of each dimension up to 3-D; where element faces are defined, the face elements exist in the mesh one dimension lower.

The ``while`` loop iterates over all elements of the mesh, finishing when ``element.isValid()`` returns ``False``. Within the loop the script sets the cache location to be the centre of each element. We know that this model contains cube elements with *xi* space ranging from 0.0 to 1.0 on three axes, hence the centre is ``xi = [0.5, 0.5, 0.5]``. If the evaluation is successful, the element identifier (an integer) and 3-D coordinates are printed out, otherwise the loop is finished early via the ``break`` instruction.

Note in particular the form of the ``evaluateReal`` call::

  result, outValues = field.evaluateReal(cache, 3)

If you are referring the the Zinc C++ API documentation you will see this differs in Python by a consistent pattern. We always pass in the number of values expected (here 3) if the C++ API requires it. Python cannot modify arguments like in C++, hence the output list of 3 coordinate values must be an additional return value after the result.

Running the Script
==================

Now we would like to run this script to check that we get the correct output from Zinc. In each case you must change directory to where you downloaded the Python script and data file, then run the script with ``python``. The exact names of the directories may not match what is on your own computer, so you will need to change them as appropriate. Follow the instructions that are applicable for your platform. 

Windows
-------

From command window cmd.exe::

  cd C:\Users\USERNAME\Downloads
  python zinc_evaluate_field.py

Mac
---

From Terminal application::

  cd Downloads
  python zinc_evaluate_field.py 

Linux
-----

From console::

  cd downloads
  python zinc_evaluate_field.py

Output
======
  
If Zinc is installed and running correctly then you should see output in the console window similar to::

  (1, [-14.192445465853147, 40.94553035358522, -19.09321437109474])
  (2, [-15.233379082455567, 17.209567807542065, -38.65332215873753])
  (3, [-12.682887575729232, -5.278168733236067, -34.292812908937876])
  (4, [-10.432248232119399, -18.121783541482813, -27.640313392674194])
  (5, [-9.267841675693589, -29.899674367217216, -10.882591483919203])
  (6, [-8.95635079795262, -25.745089538739553, 14.270736161785347])
  (7, [-9.50922456299126, -10.92128369506407, 24.24435079357739])
  (8, [-11.028493955449694, 0.1197470610870901, 27.49882902629768])
  (9, [-12.445132736112974, 17.361246257958534, 26.73474098540203])
  (10, [-13.371421200795377, 40.62684792219193, 11.649753023415892])
  ...

If this is your first use of Zinc, *congratulations on getting this far!*

To build on this tutorial, have a look at the Zinc C++/Python API docs and read more about functions it has used. You will see there are many more methods (functions) you can call on Zinc objects. Remember when using the Zinc API that starting from the Context, all other objects are created from existing objects. This means it should be simple to see what you can do or create from the objects you have. When using them the only thing you have to be careful about is when writing data that you don't overwrite important files!
