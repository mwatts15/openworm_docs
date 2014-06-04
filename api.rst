.. _api:

Draft Updates and Modifications to the PyOpenWorm API
---------------------------------------------------------

This API will augment the existing API of `PyOpenWorm <https://github.com/openworm/PyOpenWorm/>`_, which is centered around a simple means of accessing data about the c. elegans using an object model that is based on its anatomy.   

Many of these new API calls are designed around the principle that most statements correspond to some action on the database. Some of these actions may be complex, but intuitively ``a.B()`` (the Query form) will query ``$a $B ?x`` against the database, where ``$a`` and ``$B`` signify identifiers associated with ``a`` and ``B``; on the other hand, ``a.B(c)`` (the Update form) will return a triple from the database, adding it if it isn't already there. However, this is `only` to give an intuition -- for instance, insertions may be refused where they contradict some facts, and the objects returned from either the Query or Update forms may be complex objects.

The API is for working with biological entities, relationships between entities, and evidence for the relationships.

Notes

- Of course, when these methods communicate with an external database, they may fail due to the database being unavailable and the user should be notified if a connection cannot be established in a reasonable time. Also, some objects are created by querying the database; these may be made out-of-date in that case.
- Some terms may be unexplained
- ``a : {x_0,...,x_n}`` means a could have the value of any one of ``x_0`` through ``x_n``


Evidence(bibtex : BibtexEntry)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A representation of some document which provides evidence for something.  

Evidence.asserts(relationship : Relationship)
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

State that the Evidence asserts that relationship is true. A Relationship can be provided or a triple ( . , Relationship, . )

Example::

    import bibtex
    bt = bibtex.parse("my.bib")
    n1 = Neuron("AVAL")
    n2 = Neuron("DA3")
    c = Connection(pre=n1,post=n2,class="synapse")
    e = Evidence(bibtex=bt['white86'])
    e.asserts(c)

Or::

    c = Connection()
    e = Evidence(bibtex=bt['white86'])
    e.asserts((n1,c,n2))

A number of methods return objects which asserts accepts.

Example::

    n1 = Neuron("AVAL")
    r = n1.neighbor("DA3")
    e = Evidence(bibtex=bt['white86'])
    e.asserts(r)

Evidence.asserts()
++++++++++++++++++

Returns sequence of statements (triples) asserted by this evidence

Example::

    import bibtex
    bt = bibtex.parse("my.bib")
    n1 = Neuron("AVAL")
    n2 = Neuron("DA3")
    c = Connection(pre=n1,post=n2,class="synapse")
    e = Evidence(bibtex=bt['white86'])
    e.asserts(c)
    list(e.asserts()) # Returns a list [..., d, ...] such that d==c

Cell(name : String)
~~~~~~~~~~~~~~~~~~~

A biological cell


Cell.lineageName() : String
++++++++++++++++++++++++++++++++++++++

Return the lineage name. Multiplicity may result from developmental differences

Example::

    c = Cell(name="ADAL")
    c.lineageName() # Returns ["AB plapaaaapp"]    c.divisionVolume() # Returns a Quantity representing the volume of AB plapaaaap 
Cell(lineageName="AB plapaaaa")]

Cell.parentOf() : ListOf(Cell)
++++++++++++++++++++++++++++++++

Return the direct children of the cell in terms of developmental lineage.

Example::

    c = Cell(lineageName="AB plapaaaap")
    c.parentOf() # Returns [Cell(lineageName="AB plapaaaapp"),Cell(lineageName="AB plapaaaapa")]

Cell.daughterOf() : ListOf(Cell)
++++++++++++++++++++++++++++++++++

Return the parent(s) of the cell in terms of developmental lineage.  

Example::

    c = Cell(lineageName="AB plapaaaap")
    c.daughterOf() # Returns [

Cell.divisionVolume() : Quantity
++++++++++++++++++++++++++++++++++++++

Return the volume of the cell at division during development

Example::

    c = Cell(lineageName="AB plapaaaap")

Cell.divisionVolume(volume : Quantity) : Relationship
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Set the volume of the cell at division

Example::

    v = Quantity("600","(um)^3")
    c = Cell(lineageName="AB plapaaaap")
    c.divisionVolume(v)

Cell.morphology() : Morphology
+++++++++++++++++++++++++++++++++++

Return the morphology of the cell


Neuron(name : String)
~~~~~~~~~~~~~~~~~~~~~

A subclass of Cell

Neuron.connection() : ListOf(Connection)
+++++++++++++++++++++++++++++++++++++++++++

Get a set of Connection objects describing synapses or gap junctions between this neuron and others

Neuron.neighbor() : ListOf(Neuron)
+++++++++++++++++++++++++++++++++++

Get the neighboring Neurons

Neuron.neighbor(neuronName : String) : Connection
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Get the Connection object that defines that the neuron has the named neuron argument as a neighbor

Muscle(name : String) 
~~~~~~~~~~~~~~~~~~~~~~
A representation of a muscle cell

Muscle.receptor() : ListOf(Receptor)
++++++++++++++++++++++++++++++++++++++

Get a list of receptors for this muscle

Muscle.innervatedBy() : ListOf(Neuron)
++++++++++++++++++++++++++++++++++++++++

Get a list of neurons that synapse on this muscle cell

Muscle.innervatedBy(n : Neuron) : Relationship
+++++++++++++++++++++++++++++++++++++++++++++++

State that the muscle is innervated by n and return the Relationship object that captures that.

Population : SetOf(Cell)
~~~~~~~~~~~~~~~~~~~~~~~~

Any group of cells.

Worm.filterCells(prop1 : String, prop2 : String) : Population
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Allows for groups of cells to be created based on shared properties including neurotransmitter, anatomical location or region, cell type.  

Example::
    p1 = Worm.filterCells("Glutamate", "neurotransmitter")
    p2 = Worm.filterCells("Muscle", "DL")
    p3 = Worm.filterCells("Neurons", "nerve ring")


Connection(pre : Neuron, post : Neuron, [strength : Integer, ntrans : Neurotransmitter, type : {'gap junction', 'synapse'} ] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A representation of the connection between neurons. Either a gap junction or a chemical synapse

Connection.type() : {'gap junction', 'synapse'}
+++++++++++++++++++++++++++++++++++++++++++++++++++++

Returns the type of connection: 'gap junction' or 'synapse' as a String

Connection.type({'gap junction', 'synapse'}) : Relationship
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Returns the type of connection: 'gap junction' or 'synapse' as a Relationship

Connection.neurotransmitter() : String
+++++++++++++++++++++++++++++++++++++++++++++++++
Returns the type of neurotransmitter used in the connection as a String

Connection.strength() : Integer
++++++++++++++++++++++++++++++++
Returns the connection strength, the number of synapses and / or gap junctions made between the neurons

NeuroML
~~~~~~~~

A utility for generating NeuroML files from other objects. The semantics described `above <#draft-api>`__ do not apply here.

NeuroML.generate(object, type : {0,1,2}) : neuroml.NeuroMLDocument
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Get a NeuroML object that represents the given object. The ``type`` determines what content is included in the NeuroML object:
0=full morphology+biophysics, 1=cell body only+biophysics, 2=full morphology only

NeuroML.write(document : neuroml.NeuroMLDocument, filename : String) 
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Write out a NeuroMLDocument



