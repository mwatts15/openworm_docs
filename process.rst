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

Draft API
---------

Objects needed
~~~~~~~~~~~~~~

Cell object

Connection object

This API is based on the principle that most statements correspond to some action on the database. Some of these actions may be complex, but intuitively ``a.B()`` (the Query form) will query ``$a $B ?x`` against the database, where ``$a`` and ``$B`` signify identifiers associated with ``a`` and ``B``; on the other hand, ``a.B(c)`` (the Update form) will return a triple from the database, adding it if it isn't already there. However, this is `only` to give an intuition -- for instance, insertions may be refused where they contradict some facts, and the objects returned from either the Query or Update forms may be complex objects.

Notes

 -  Of course, when these methods communicate with an external database, they may fail due to the database being unavailable and the user should be notified if a connection cannot be established in a reasonable time. Also, some objects are created by querying the database; these may be made out-of-date in that case.


Evidence

A representation of some document which provides evidence for something.  

Evidence.asserts(relationship)

    State that the Evidence asserts that relationship is true. A Relationship can be provided or a triple ( . , Relationship, . )

    Example::

        import bibtex
        bt = bibtex.parse("my.bib")
        n1 = Neuron("AVAL")
        n2 = Neuron("DA3")
        c = Connection(pre=n1,post=n2,class="synapse")
        e = Evidence(bt['white86'])
        e.asserts(c)

    Or::

        c = Connection()
        e = Evidence(bt['white86'])
        e.asserts((n1,c,n2))
    
    A number of methods return objects which asserts accepts
    Example::

        n1 = Neuron("AVAL")
        c = n1.neighbor(
        e = Evidence(bt['white86'])
        e.asserts((n1,c,n2))

Evidence.asserts()

    Returns sequence of statements (triples) asserted by this evidence

    Example::

        import bibtex
        bt = bibtex.parse("my.bib")
        n1 = Neuron("AVAL")
        n2 = Neuron("DA3")
        c = Connection(pre=n1,post=n2,class="synapse")
        e = Evidence(bt['white86'])
        e.asserts(c)
        list(e.asserts()) # Returns a list [..., d, ...] such that d==e

Cell.lineageName()

    Return the lineage name

    Example::

        c = Cell(name="ADAL")
        c.lineageName() # Returns "AB plapaaaapp"

Cell.parentOf()

    Return the direct children of the cell
    
    Example::

        c = Cell(lineageName="AB plapaaaap")
        c.parentOf() # Returns ["AB plapaaaapp","AB plapaaaapa"]

Cell.daughterOf()

    Return the parent(s) of the cell (multiplicity may result from uncertainty)

    Example::

        c = Cell(lineageName="AB plapaaaap")
        c.daughterOf() # Returns ["AB plapaaaa"]

Cell.divisionVolume()

    Return the volume of the cell at division

    Example::

        c = Cell(lineageName="AB plapaaaap")
        c.divisionVolume() # Returns a Quantity representing the volume of AB plapaaaap 

Cell.divisionVolume(volume)

    Set the volume of the cell at division

    Example::

        v = Quantity("600","um")
        c = Cell(lineageName="AB plapaaaap")
        c.divisionVolume(v)

Neuron.connection()

    Get a set of Connection objects describing synapses between this neuron and others

Neuron.neighbor()

    Get the neighboring Neurons

Neuron.as_neuroml([arguments])
Get a NeuroML object that represents this neuron  
		
		   :param type: How verbose of a neuroml object do you want  
		                0=full morphology+biophysics, 1=cell body only+biophysics, 2=full morphology only
		   :returns: Computational model of this neuron encoded in NeuroML
		   :rtype: neuroml object

Connection.get_synapse_type()

Connection.get_neurotransmitter()

Connection.get_strength()

Adding data for existing methods that aren't populated yet
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Neuron.receptors()

Neuron.get_reference()
