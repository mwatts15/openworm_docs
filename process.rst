.. _data_requirements:

Requirements for data storage in OpenWorm
=========================================
Our OpenWorm database captures elements of the worm which are shared between all worms. The database serves the function of recording the evidence (metadata) that went into the data underlying the models in OpenWorm. We want to preserve this metadata for the purposes of justifying the models. In addition, consolidating the data offers the possibility of identifying inconsistencies within the data which could affect model generation.

Considering the storage of model data in the results I ask the question of how manage the assertion that such and such a model fits a set of observations. For me, the database is that part of a business which says, at any moment, what the business knows and considers to be true.In this light, how can we have generated models, represented in the first order as model files and in higher orders as simulation recordings, and not keep some record of how these were generated, what data went into them, and what data they are validated against? Clearly, this data is generated in the course of the project and stored, at least temporarily, for the purpose of setting model parameters and evaluating model inaccuracy.

It is not, however, necessary that not all of the data we're concerned with and which is accessed through the database need be modeled as triples in the data store. Rather, our database is primarily a store of metadata about data which are already accessible. This metadata includes citations of reference material -- this has been much discussed. However, it can also preserve a record of what is done internally within the project including the processes of model generation and simulation results, who created the data, and when. One of our tenants as an Open Science project is that we make our actions transparent to the public. This also means making our actions clear and readable to people outside of the project who at least have a basic understanding of what OpenWorm is about. To that end, it should be trivial to create a document, of arbitrary detail, about any datum, which summarizes all those processes which contributed to its origination.

To realize this vision, it is suggested that the database have controlled access by only those with stable identifiers (e.g., email address, github account) which can be authenticated against using a secure asymmetric key protocol. 

Interface
---------
The database is accessed through a Python library which communicates with the database. This library serves the function of providing an object oriented view on the database that can be accessed through the python scripts common used in the project. 

Data modelling
--------------

Nervous system
~~~~~~~~~~~~~~
In the worm's nervous system, we capture a few important data types (listed `below <#datatypes>`__). These correspond primarily to the elements which are necessary for the worm's ability to record external and internal stimuli and activate its body in response to those stimuli. Also included in our model are the relationships between these types. Given our choice of data types, we model do not model the individual interactions between cells as entities in the database. Rather these are described by generic predicates in an `RDF triple <http://stackoverflow.com/a/1122451>`__. For instance, neuron A synapsing with muscle cell B would give a statement (A, synapsesWith, B), but A synapsing with neuron C would also have (A, synapsesWith, C). However, there are data which we associate with relationships, such as synapses, which are unique to the relationship. Often this data will 'belong' to either the subject or object of the relationship. In other cases where the data annotate many relationships simultaneously, we propose the use of RDF named graphs. Named graphs, `as RDF molecules <http://ebiquity.umbc.edu/paper/html/id/240/>`__ have `already been employed <https://github.com/mwatts15/PyOpenWorm/blob/master/PyOpenWorm/data.py#L162>`__ for tracking evidence of relationships in the worm. 

.. _datatypes:

Data types
~~~~~~~~~~
A non-exhaustive list of data types in our C. elegans database

- receptor types identified in the nerve cell
- neurons
- ion channels
- neurotransmitters
- muscle receptors

Development
~~~~~~~~~~~
Caenorhabditis elegans has very stable cell division patterns in the absence of mutations. This means that we capture divisions in our database as static 'descendent-of' relationships. In simulation, we set up correspondence between cells in our database with the cells in simulation to link relevant metadata describing the cell and the understanding of its place in the worms developmental process.

Aging
~~~~~
`Need more info`

Information assurance
---------------------


Reasoning
~~~~~~~~~
To make full use of RDF storage it's recommended to leverage reasoning over our stored data. Encoding rules for the worm requires a good knowledge of both C. elegans and the database schema. More research needs to be done on this going forward. Preliminarily, SPIN, a constraint notation system based on SPARQL looks like a good candidate for `specifying` rules, but an inference engine for `enforcing` the rules still needs to be identified.


Input validation
~~~~~~~~~~~~~~~~

Provenance
~~~~~~~~~~
Data uploaders, on submitting to the database, must be required to "check-in" using a consistent protocol. All other submissions to the database must be rejected. This protocol must include, at least, a time-stamp on the update, linking of the submitted data to the uploader's unique identifier.

Miscellaneous 
-------------
Versioning
~~~~~~~~~~
Experimental methods are constantly improving in biological research. These improvements may require updating the data we reference or store internally. However, in making updates we must not immediately expunge older content, breaking links created by internal and external agents. Ideally we would have a means of deprecating old data and specifying replacements. On the level of single resources, this is a trivial mapping which may be done transparently to all readers. For a more significant change, altering the schema, human intervention may be required to update external readers.

