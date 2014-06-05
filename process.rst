.. _data_requirements:

Requirements for data storage in OpenWorm
=========================================
Our OpenWorm database captures facts about C. `elegans`. The database stores data for generating model files and together with annotations describing the origins of the data. Below are a set of recommendations for implementation of the database organized around an RDF model.

Updating the data model 

Why RDF?
---------

Interface
---------
Access is through a Python library which communicates with the database. This library serves the function of providing an object oriented view on the database that can be accessed through the Python scripts commonly used in the project. The :ref:`draft api <api>` is described separately.

Data modelling
--------------
Biophysical and anatomical data are included in the database. A sketch of some proposed features of the data model is below. Also included in our model are the relationships between these types. Given our choice of data types, we do not model the individual interactions between cells as entities in the database. Rather these are described by generic predicates in an `RDF triple <http://stackoverflow.com/a/1122451>`__. For instance, neuron A synapsing with muscle cell B would give a statement (A, synapsesWith, B), but A synapsing with neuron C would also have (A, synapsesWith, C). Data which belong to the specific relationship between two nodes is attached to an `rdf:Statement object <http://www.w3.org/TR/rdf-schema/#ch_statement>`__ which points to the statement. [#named-graphs]_

.. [#named-graphs] This approach is chosen over named graphs for two reasons. First, we would like to make statements 3rd order statements (e.g., a said, "b said, 'c did d'"), in particular to keep track of data uploders in the graph. Second, The meaning of the relationship of a graph name to its members may be unclear.

Nervous system
~~~~~~~~~~~~~~
In the worm's nervous system, we capture a few important data types (listed `below <#datatypes>`__). These correspond primarily to the anatomical structures and chemicals which are necessary for the worm to record external and internal stimuli and activate its body in response to those stimuli.

.. _datatypes:

Data types
++++++++++
A non-exhaustive list of data types in our C. elegans database

- receptor types identified in the nerve cell
- neurons
- ion channels
- neurotransmitters
- muscle receptors

Development
~~~~~~~~~~~
Caenorhabditis elegans has very stable cell division patterns in the absence of mutations. This means that we can capture divisions in our database as static 'daughter_of' relationships. 

Aging
~~~~~
The mechanisms for aging in the organism need further discussion before a data model can be proposed.

Information assurance
---------------------

Reasoning
~~~~~~~~~
To make full use of RDF storage it's recommended to leverage reasoning over our stored data. Encoding rules for the worm requires a good knowledge of both C. elegans and the database schema. More research needs to be done on this going forward. Preliminarily, SPIN, a constraint notation system based on SPARQL looks like a good candidate for `specifying` rules, but an inference engine for `enforcing` the rules still needs to be found.

Input validation
~~~~~~~~~~~~~~~~

Provenance
~~~~~~~~~~
As stated in the opening paragraph tracking the origins of our data demands a method of annotating statements in our database. 

Tracking research
++++++++++++++++++

Tracking uploaders
+++++++++++++++++++
To enforce this it is suggested that the database be accessed through an authentication layer. This protocol must include, at least, a time-stamp on the update and linking of the submitted data to the uploader's unique identifier (e.g., email address). 

Miscellaneous 
-------------
Versioning
~~~~~~~~~~
Experimental methods are constantly improving in biological research. These improvements may require updating the data we reference or store internally. However, in making updates we must not immediately expunge older content, breaking links created by internal and external agents. Ideally we would have a means of deprecating old data and specifying replacements. On the level of single resources, this is a trivial mapping which may be done transparently to all readers. For a more significant change, altering the schema, human intervention may be required to update external readers.

