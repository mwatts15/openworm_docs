.. _data_requirements:

Requirements for data storage in OpenWorm
=========================================

Our OpenWorm database captures elements of the worm which are shared between all worms. 

Interface
---------
The database is accessed through a Python library which communicates with the database. This library serves the function of providing an object oriented view on the database that can be accessed through the python scripts common used in the project. 

Nervous system
--------------
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
-----------
Caenorhabditis elegans has very stable cell division patterns in the absence of mutations. This means that we capture divisions in our database as static 'descendent-of' relationships. In simulation, we set up correspondence between cells in our database with the cells in simulation to link relevant metadata describing the cell and the understanding of its place in the worms developmental process.

Aging
-----
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

Draft objects needed
~~~~~~~~~~~~~~~~~~~~

Cell object

Connection object

Draft method signatures
~~~~~~~~~~~~~~~~~~~~~~~

Cell.getLineageName()

Cell.getDaughters()

Cell.getParentCell()

Cell.getReferences()

Cell.getReferences([arguments])

Cell.getDivisionVolume()

Neuron.add_reference()

Neuron.get_connections()

Neuron.get_neighbors()

"""Get a NeuroML object that represents this neuron  
		
		   :param type: How verbose of a neuroml object do you want  
		                0=full morphology+biophysics, 1=cell body only+biophysics, 2=full morphology only
		   :returns: Computational model of this neuron encoded in NeuroML
		   :rtype: neuroml object
"""
Neuron.as_neuroml([arguments])

Connection.get_synapse_type()

Connection.get_neurotransmitter()

Connection.get_strength()

Adding data for existing methods that aren't populated yet
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Neuron.receptors()

Neuron.get_reference()

