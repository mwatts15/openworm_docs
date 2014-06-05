.. _api:

Draft Updates and Modifications to the PyOpenWorm API
------------------------------------------------------


The API 
This API will augment the existing API of `PyOpenWorm <https://github.com/openworm/PyOpenWorm/>`_, which is centered around a simple means of accessing data about the C. elegans using an object model that is based on its anatomy.  In particular it is for making statements about biological entities in the worm or querying previously made statements. In addition, statements concerning the evidence for statements (called relationships below).

Many of these new API calls work


. Some of these actions may be complex, but intuitively ``a.B()`` (the Query form) will query ``$a $B ?x`` against the database, where ``$a`` and ``$B`` signify identifiers associated with ``a`` and ``B``; on the other hand, ``a.B(c)`` (the Update form) will return a triple from the database, adding it if it isn't already there. However, this is `only` to give an intuition -- for instance, insertions may be refused where they contradict some facts, and the objects returned from either the Query or Update forms may be complex objects.


Notes

- Of course, when these methods communicate with an external database, they may fail due to the database being unavailable and the user should be notified if a connection cannot be established in a reasonable time. Also, some objects are created by querying the database; these may be made out-of-date in that case.
- Some terms may be unexplained
- ``a : {x_0,...,x_n}`` means a could have the value of any one of ``x_0`` through ``x_n``

Worm()
~~~~~~~~

A representation of the whole worm

Worm.fi

Evidence(bibtex : BibtexEntry)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A representation of some document which provides evidence for something.  

Evidence.asserts(relationship : Relationship)
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

State that the Evidence asserts that relationship is true.

Example::

    import bibtex
    bt = bibtex.parse("my.bib")
    n1 = Neuron("AVAL")
    n2 = Neuron("DA3")
    c = Connection(pre=n1,post=n2,class="synapse")
    e = Evidence(bibtex=bt['white86'])
    e.asserts(c)

Other methods return objects which asserts accepts.

Example::

    n1 = Neuron("AVAL")
    r = n1.neighbor("DA3")
    e = Evidence(bibtex=bt['white86'])
    e.asserts(r)

Evidence.asserts()
++++++++++++++++++

Returns a sequence of statements asserted by this evidence

Example::

    import bibtex
    bt = bibtex.parse("my.bib")
    n1 = Neuron("AVAL")
    n2 = Neuron("DA3")
    c = Connection(pre=n1,post=n2,class="synapse")
    e = Evidence(bibtex=bt['white86'])
    e.asserts(c)
    list(e.asserts()) # Returns a list [..., d, ...] such that d==c

Relationship()
~~~~~~~~~~~~~~

A relationship between

Cell(name : String)
~~~~~~~~~~~~~~~~~~~

A biological cell


Cell.lineageName() : String
++++++++++++++++++++++++++++++++++++++

Return the lineage name. Multiplicity may result from developmental differences

Example::

    c = Cell(name="ADAL")
    c.lineageName() # Returns ["AB plapaaaapp"]

Cell.parentOf() : ListOf(Cell)
++++++++++++++++++++++++++++++++

Return the direct daughters of the cell

Example::

    c = Cell(lineageName="AB plapaaaap")
    c.parentOf() # Returns [Cell(lineageName="AB plapaaaapp"),Cell(lineageName="AB plapaaaapa")]

Cell.daughterOf() : ListOf(Cell)
++++++++++++++++++++++++++++++++++

Return the parent(s) of the cell. Multiplicity may result from uncertainty

Example::

    c = Cell(lineageName="AB plapaaaap")
    c.daughterOf() # Returns [Cell(lineageName="AB plapaaaa")]

Cell.divisionVolume() : Quantity
++++++++++++++++++++++++++++++++++++++

Return the volume of the cell at division

Example::

    c = Cell(lineageName="AB plapaaaap")
    c.divisionVolume() # Returns a Quantity representing the volume of AB plapaaaap 

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

Get a set of Connection objects describing synapses between this neuron and others

Neuron.neighbor() : ListOf(Neuron)
+++++++++++++++++++++++++++++++++++

Get the neighboring Neurons

Neuron.neighbor(neuronName : String) : Connection
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

State that neuronName is a neighbor of this Neuron

Muscle(name : String) 
~~~~~~~~~~~~~~~~~~~~~~
A representation of a muscle cell. `See also current API`

Muscle.receptor() : ListOf(Receptor)
++++++++++++++++++++++++++++++++++++++

Get a list of receptors for this muscle

Muscle.innervatedBy() : ListOf(Neuron)
++++++++++++++++++++++++++++++++++++++++

Get a list of neurons that synapse on this muscle cell

Muscle.innervatedBy(n : Neuron) : Relationship
+++++++++++++++++++++++++++++++++++++++++++++++

State that the muscle is innervated by n

Signaling(sender : Population, receiver : Population, messengerSpecies : Object)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A signalling Relationship between sender and receiver Populations

Signaling.sender() : Population
+++++++++++++++++++++++++++++++++++++

Get the sending population in this signalling relationship

Signaling.receiver() : Population
+++++++++++++++++++++++++++++++++++++

Get the receiving population in this signalling relationship

Signaling.messengerSpecies() : Object
+++++++++++++++++++++++++++++++++++++

Get the species transmitted in this signalling relationship

Network()
~~~~~~~~~~

A network of Neurons. `see current API`

Population : SetOf(Cell)
~~~~~~~~~~~~~~~~~~~~~~~~~~~

A collection of cells

Example::

    ns = Network.neurons()
    set(ns) # a Population

Connection(pre : Neuron, post : Neuron, [strength : Integer, ntrans : Neurotransmitter, type : {'gap junction', 'synapse'} ] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A representation of the connection between neurons. Either a gap junction or a chemical synapse

Connection.type() : {'gap junction', 'synapse'}
+++++++++++++++++++++++++++++++++++++++++++++++++++++

Returns the type of connection: 'gap junction' or 'synapse'

Connection.type({'gap junction', 'synapse'}) : Relationship
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Statements 

Connection.neurotransmitter() : Neurotransmitter
+++++++++++++++++++++++++++++++++++++++++++++++++
Returns the type of neurotransmitter used in the connection

Connection.strength() : Integer
++++++++++++++++++++++++++++++++
Returns the connection strength, the number of synapses made between the neurons

NeuroML
~~~~~~~~

A utility for generating NeuroML files from other objects. The semantics described `above <#draft-api>`__ do not apply here.

NeuroML.generate(object : {Neuron, IonChannel}, type : {0,1,2}) : neuroml.NeuroMLDocument
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Get a NeuroML object that represents the given object. The ``type`` determines what content is included in the NeuroML object:
0=full morphology+biophysics, 1=cell body only+biophysics, 2=full morphology only

NeuroML.write(document : neuroml.NeuroMLDocument, filename : String) 
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Write out a NeuroMLDocument

IonChannel
~~~~~~~~~~~~

