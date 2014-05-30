Draft API
---------

This API is based on the principle that most statements correspond to some action on the database. Some of these actions may be complex, but intuitively ``a.B()`` (the Query form) will query ``$a $B ?x`` against the database, where ``$a`` and ``$B`` signify identifiers associated with ``a`` and ``B``; on the other hand, ``a.B(c)`` (the Update form) will return a triple from the database, adding it if it isn't already there. However, this is `only` to give an intuition -- for instance, insertions may be refused where they contradict some facts, and the objects returned from either the Query or Update forms may be complex objects.

Notes

- Of course, when these methods communicate with an external database, they may fail due to the database being unavailable and the user should be notified if a connection cannot be established in a reasonable time. Also, some objects are created by querying the database; these may be made out-of-date in that case.
- Some terms may be unexplained


Evidence(bibtex : BibtexEntry)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A representation of some document which provides evidence for something.  

Evidence.asserts(relationship : Relationship)
+++++++++++++++++++++++++++++++++++++++++++++

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
    c = n1.neighbor("DA3")
    e = Evidence(bibtex=bt['white86'])
    e.asserts(c)

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
    list(e.asserts()) # Returns a list [..., d, ...] such that d==e

Cell(name : String)
~~~~~~~~~~~~~~~~~~~

A biological cell


Cell.lineageName()
++++++++++++++++++

Return the lineage name. Multiplicity may result from developmental differences

Example::

    c = Cell(name="ADAL")
    c.lineageName() # Returns ["AB plapaaaapp"]

Cell.parentOf()
+++++++++++++++

Return the direct children of the cell

Example::

    c = Cell(lineageName="AB plapaaaap")
    c.parentOf() # Returns ["AB plapaaaapp","AB plapaaaapa"]

Cell.daughterOf()
+++++++++++++++++

Return the parent(s) of the cell. Multiplicity may result from uncertainty

Example::

    c = Cell(lineageName="AB plapaaaap")
    c.daughterOf() # Returns ["AB plapaaaa"]

Cell.divisionVolume()
+++++++++++++++++++++

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

Neuron(name : String)
~~~~~~~~~~~~~~~~~~~~~

A subclass of Cell

Neuron.connection()
+++++++++++++++++++

Get a set of Connection objects describing synapses between this neuron and others

Neuron.neighbor()
+++++++++++++++++

Get the neighboring Neurons

Connection(pre : Neuron, post : Neuron, [strength : Integer, ntrans : Neurotransmitter, type : { 'gap junction', 'synapse' } ] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A representation of the connection between neurons. Either a gap junction or a chemical synapse

Connection.type()
+++++++++++++++++

Returns the type of connection: 'gap junction' or 'synapse'

Connection.neurotransmitter()
+++++++++++++++++++++++++++++
Returns the type of neurotransmitter used in the connection

Connection.strength()
+++++++++++++++++++++
Returns the connection strength, the number of synapses made between the neurons

NeuroML
~~~~~~~

A utility for generating NeuroML files from other objects. The semantics described `above <#draft-api>`__ do not apply here.

NeuroML.generate(object, type : {0,1,2})
+++++++++++++++++++++++++++++++++++++++++

Get a NeuroML object that represents the given object. The ``type`` determines what content is included in the NeuroML object:
0=full morphology+biophysics, 1=cell body only+biophysics, 2=full morphology only

