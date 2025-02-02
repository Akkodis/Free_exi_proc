<br/>📎 4. EXI Streams



table{

<br/>📎 Table 4-1 EXI events



th{

td{

"EXI Event Type"

}

td{

"Event Content(Content Items)"

}

td{

"Grammar Notation(Terminal Symbols)"

}

td{

"Information Item"

}

}

tr{

td{

"Start Document"

}

td{

}

td{

SD

}

td{

}

}

tr{

td{

"End Document"

}

td{

}

td{

ED

}

td{

}

}

tr{

td{

"Start Element"

}

td{

qname

}

td{

SE(qname)

}

td{

}

}

tr{

td{

"Start Element"

}

td{

qname

}

td{

SE((_*_))

}

td{

}

}

tr{

td{

"Start Element"

}

td{

qname

}

td{

SE((uri:(_*_)))

}

td{

}

}

tr{

td{

"End Element"

}

td{

}

td{

EE

}

td{

}

}

tr{

td{

"Attribute"

}

td{

qname

value

}

td{

AT(qname)

}

td{

}

}

tr{

td{

"Attribute"

}

td{

qname

value

}

td{

AT((_*_))

}

td{

}

}

tr{

td{

"Attribute"

}

td{

qname

value

}

td{

AT((uri:(_*_)))

}

td{

}

}

tr{

td{

"Characters"

}

td{

value

}

td{

CH

}

td{

}

}

tr{

td{

"Namespace Declaration"

}

td{

uri

prefix

local_element_ns

}

td{

NS

}

td{

}

}

tr{

td{

"Comment"

}

td{

text

}

td{

CM

}

td{

}

}

tr{

td{

"Process Instruction"

}

td{

name

text

}

td{

PI

}

td{

}

}

tr{

td{

"DOCTYPE"

}

td{

name

public

system

text

}

td{

DT

}

td{

}

}

tr{

td{

"Entity Reference"

}

td{

name

}

td{

ER

}

td{

}

}

tr{

td{

"Self Contained"

}

td{

}

td{

SC

}

td{

}

}

}

example{

<br/>📎 Example 6-1. Example productions with event codes.







Grammar{

lhs{

DocContent

}

rhs{

SE("A")

DocEnd

EventCode(0)

}

rhs{

SE("B")

DocEnd

EventCode(1)

}

rhs{

SE("C")

DocEnd

EventCode(2)

}

rhs{

SE("D")

DocEnd

EventCode(3)

}

rhs{

SE((_*_))

DocEnd

EventCode(4)

}

rhs{

DT

DocContent

EventCode(5,0)

}

rhs{

CM

DocContent

EventCode(5,1,0)

}

rhs{

PI

DocContent

EventCode(5,1,1)

}

}

}

<br/>📎 8. EXI Grammars




EXI is a knowledge based encoding that uses a set of grammars to determine which events are most likely to occur at any
given point in an EXI stream and encodes the most likely alternatives in fewer bits. It does this by mapping the stream
of events to a lower entropy set of reptresentative values and encoding those values using a set of simple variable length
codes or an EXI compression algorithm.



The result is a very simple, small algorithm that uniformly handles schema-less encoding, schema-informed encoding,
schema deviations, and any combination thereof in EXI streams. These variations do not require different algorithms or different parsers,
they are simply informed by different combinations of grammars.

The following sections describe the grammars used to inform the EXI encoding.

Note:

The grammar semantics in this specification are written for clarity and generality. They do not prescribe a particular implementation approach.
<br/>📎 8.1 Grammar Notation



<br/>📎 8.1.1 Fixed Event Codes



((

Each grammar production has an event code, which is represented by a sequence of one to three parts separated by periods (.) [in this ceps specification we use commas (),) instead].
Each part is an unsigned integer. The following are examples of grammar productions with event codes as theyx appear in this specification.


)example{

<br/>📎 Example 8-1. Example productions with fixed event codes.





















Grammar{

lhs{

LeftHandSide_1

}

rhs{

Terminal_1

NonTerminal_1

EventCode(0)

}

rhs{

Terminal_2

NonTerminal_2

EventCode(1)

}

rhs{

Terminal_3

NonTerminal_3

EventCode(2,0)

}

rhs{

Terminal_4

NonTerminal_4

EventCode(2,1)

}

rhs{

Terminal_5

NonTerminal_5

EventCode(2,2,0)

}

rhs{

Terminal_6

NonTerminal_6

EventCode(2,2,1)

}

lhs{

LeftHandSide_2

}

rhs{

Terminal_1

NonTerminal_1

EventCode(0)

}

rhs{

Terminal_2

NonTerminal_2

EventCode(1,0)

}

rhs{

Terminal_3

NonTerminal_3

EventCode(1,1)

}

}

}




The number of parts in a given event code is called the event code's length.
No two productions with the same nonterminal Symbolson the left-hand side are permitted to have the same event code.

<br/>📎 8.1.2 Variable Event Codes



Some non-terminal symbols are used on the right-hand side in a production without a terminal symbol prefixed to them,
but with a parenthesized event code affixed instead. Such non-terminal symbols are macros amd they are used to capture some recurring
set of productions as symbols so that a symbol can be used in the grammar representation instead of including all the productions the macro
represents in place every time it is used.

example{

<br/>📎 Example 8-2. Example productions that use macro non-terminal symbols.















Grammar{

lhs{

ABigProduction_1

}

rhs{

Terminal_1

NonTerminal_1

EventCode(0)

}

LEFTHANDSIDE{

2

0

}

lhs{

ABigProduction_2

}

rhs{

Terminal_1

NonTerminal_1

}

LEFTHANDSIDE{

1

1

}

rhs{

Terminal_2

NonTerminal_2

}

}

}

Because non-terminal macros are injected into the right-hand side of more than one production, the event codes of
productions with these macro non-terminals on the left-hand side are not fixed, but will have different event code values depending
on the context in which the macro non-terminal appears. This specification calls these variable event codes and use variables in place
of individual event code parts to indicate the event code parts are determined by the context. Below are some examples of variable event codes.


__Macro__  *__LEFTHANDSIDE__ * :





*i*  __:=__  hd(arglist)

*j*  __:=__  hd(tail(arglist))

rhs{

TERMINAL_1

NONTERMINAL_1

EventCode(i,0)

}

rhs{

TERMINAL_2

NONTERMINAL_2

EventCode(i,1)

}

rhs{

TERMINAL_3

NONTERMINAL_3

EventCode(i,(j+2))

}

rhs{

TERMINAL_4

NONTERMINAL_4

EventCode(i,(j+3))

}

rhs{

TERMINAL_5

NONTERMINAL_5

EventCode(i,(j+4),0)

}

rhs{

TERMINAL_6

NONTERMINAL_6

EventCode(i,(j+4),1)

}

Unless otherwise specified, the variable i evaluates to the first part of the event code of the production in which the macro non-terminal LeftHandSide_1
appears on the right-hand side. Similarly, the expression i.j represents the first two parts of the event code of the production in which
the macro LEFTHANDSIDE_1 appears on the right-hand side.

Non-terminal macros are used in this specification for notational convenience only. They are non-terminals, even though they are used in place of non-terminals.
Productions that use non-terminal macros on the right-hand side need to be expanded by macro substitution before such productions are interpreted. Therefore, ABigProduction_1
and ABigProduction_2 shown in the preceding example are quivalen to the following set of productions obtained by expanding the non-terminal maro symbol LEFTHANDSIDE_1 and
evaluating the variable event codes.

example{

<br/>📎 8.2 Expanded productions equivalent to the productions used above.



}

<br/>📎 8.2 Grammar Event Codes




Each production rule in the EXI grammar includes an event code value that approximates the likelihood the associated production rule will be matched over the other
productions with the same left-hand-side non-terminal symbol. Ultimately, the event codes determine the value(s) by which each non-terminal symbol will be
represented in the EXI stream.


To understand how a given event code approximates the likelihood a given production will match, it is useful to visualize the event codes for a set of production rules that have;
the same non-terminal symbol on the left-hand side as a tree.;
For example, the following set of productions:
Grammar{





lhs{

ElementContent

}

rhs{

EE

EventCode(0)

}

rhs{

SE((_*_))

ElementContent

EventCode(1,0)

}

rhs{

CH

ElementContent

EventCode(1,1)

}

rhs{

ER

ElementContent

EventCode(1,2)

}

rhs{

CM

ElementContent

EventCode(1,3,0)

}

rhs{

PI

Elementcontent

EventCode(1,3,2)

}

}



represents a set of informationm items that might occur as element content after the start tag. Using the production event codes, we can visualize this set of productions as follows:

figure{

Tree(Leaf(EE,0),Tree(Leaf(SE((_*_)),0),Leaf(CH,1),Leaf(ER,2),Tree(Leaf(CM,0),Leaf(PI,1),3),1))

caption{

"Event code tree for ElementContent grammar"

}

}

where the terminal symbols are represented by the leaf nodes of the tree, and the event code of each production rule defines a path from the root of the tree
to the node that represents the terminal symbol that is on the right-hand side of the production. We call this the event code tree for a given set of productions.


An event code tree is similar to a Huffman tree in that shorter paths are generally used for symbols that are considered more likely. However, event code
trees are far simpler and less costly to compute and maintain. Event code trees are shallow and contain at most three levels. In addition, the length of ech event code
in the event code tree is assigned statically without analyzing the data. This classification provides some of the benefits of a Huffman tree without the cost.

<br/>📎 8.3 Pruning Unneeded Productions



As discussed in section [6.3 Fidelity Options], applications MAY provide a set of fidelity options to specify the XML features they require.
EXI processors MUST use thesefidelity options to prune the productions of which the terminal symbols represent the events that are not required from the grammars,
improving compactness and processing efficiency. For example, the following set of productions represent the set of information items that might occur as element
content after the start tag.


example{

<br/>📎 Example 8-6. Example productions with full fidelity



Grammar{



lhs{

ElementContent

}

rhs{

EE

ElementContent

EventCode(0)

}

rhs{

SE((_*_))

ElementContent

EventCode(1,0)

}

rhs{

CH

ElementContent

EventCode(1,1)

}

rhs{

ER

ElementContent

EventCode(1,2)

}

rhs{

CM

ElementContent

EventCode(1,3,0)

}

rhs{

PI

ElementContent

EventCode(1,3,1)

}

}

}

exi_prune()

expect_equality{



exi_prune()

Grammar{



lhs{

ElementCount

}

rhs{

EE

ElementCount

EventCode(0)

}

rhs{

SE((_*_))

ElementCount

EventCode(1,0)

}

rhs{

CH

ElementCount

EventCode(1,1)

}

}

}

If an application sets the fidelity options Preserve.Comments, Preserve.pis and Preserve.dtd to false, the productionsmatching comment (CM),
processing instruction (PI) and entity reference (ER) events are pruned frm the grammar, producing the following set of productions:


example{

<br/>📎 Example 8-7. Example productions after pruning



Grammar{



lhs{

ElementCount

}

rhs{

EE

ElementCount

EventCode(0)

}

rhs{

SE((_*_))

ElementCount

EventCode(1,0)

}

rhs{

CH

ElementCount

EventCode(1,1)

}

}

}

Removing these productions from the grammar tells EXI processors that comments and processing instructions will never occur in the EXI stream,
which reduces the entropy of the stream allowing it to be encoded in fewer bits.

Each time a production is removed from a grammar, the event codes of the other productions with the same nonterminal symbol on the left-hand side MUST be
adjusted to keep them contguous if its removal has left the remaining productions with non-contiguous event codes.

<br/>📎 8.4 Built-in XML Grammars



This section describes built-in XML grammars used by EXI when no schema information is available or when available schema information describes
only portions of the EXI stream.

The built-in XML grammars are dynamic and continuously evolve to reflect knowledge learned while processing an EXI stream.
New built-in element grammars are created to describe the content of newly encountered elements and new grammar productions are added
to refine existing built-in grammars. Newly learned grammars and productions are used to more efficiently represent subsequent events in the EXI stream.
All newly created built-in element grammars are global element grammars.

[Definition:] A global element grammar is a grammar describing the content of an element that has global scope (i.e. a global element).
At the onset of processing an EXI stream, the set of global element grammars is the set of all schema-informed element grammars derived from element declarations
that have a {scope} property of global. Each built-in element grammar created while processing an EXI stream is added to the set of global element grammars.
Each global element grammar has a unique qname.
<br/>📎 8.4.1 Built-in Document Grammar




In the absence of schema information describing the content of the EXI stream, the following grammar describes the events that will occur in an EXI document.

Grammar{

BuiltInDocumentGrammar



lhs{

Document

}

rhs{

SD

DocContent

EventCode(0)

}

lhs{

DocContent

}

rhs{

SE((_*_))

DocEnd

EventCode(0)

}

rhs{

DT

DocContent

EventCode(1,0)

}

rhs{

CM

DocContent

EventCode(1,1,0)

}

rhs{

PI

DocContent

eventCode(1,1,1)

}

lhs{

DocEnd

}

rhs{

ED

EventCode(0)

}

rhs{

CM

DocEnd

EventCode(1,0)

}

rhs{

PI

DocEnd

EventCode(1,1)

}

}



Semantics


All productions in the built-in document grammars from LeftHandSide : SE((_*_)) RightHandSide
 are evaluated as follows:


1. Let qname be the qname of the element matched by SE((_*_)) i.e (qname == (_*_)) 
2. If a global element grammar does not exist for element qname, create one according to section 8.4.3. Built-in Element Grammar. 
3. Evaluate the element content using the global element grammar for element qname 
4. Evaluate the remainder of event sequence using RightHandSide 


<br/>📎 8.4.2 Built-in Fragment Grammar



In the absence of schema information describing the contents of an EXI stream, the following grammar describes the events that may occur
in an EXI fragment. The grammar below represents the initial set of productions in the built-in fragment grammar at the start of EXI stream
processing. The associated semantics explain how the built-in fragment grammar evolves to more efficiently represent subsequent events in the EXI stream.

Grammar{



lhs{

Fragment

}

rhs{

SD

FragmentContent

EventCode(0)

}

lhs{

FragmentContent

}

rhs{

SE((_*_))

FragmentContent

EventCode(0)

}

rhs{

ED

EventCode(1)

}

rhs{

CM

FragmentContent

EventCode(2,0)

}

rhs{

PI

FragmentContent

EventCode(2,1)

}

}




Semantics:



All productions in the built-in fragment grammars of the form LeftHandSide: SE((_*_)) RightHandSide are evaluated as folows:

1. Let qname be the qname of the element matched by SE((_*_)) 
2. If a global element grammar does not exist for element qname, create one according to section 8.4.3 Built-in Element Grammar.
3. Create a production of the form LeftHandSide: SE(qname) RightHandSide with event code 0.
4. Increment the first part of the event code of each production in the current grammar with the non-terminal LeftHandSide on the left-hand side.
5. Add the production created in step 3 to the grammar
6. Evaluate the element content using the global element grammar for element qname .
7. Evaluate the remainder of event sequence using RihgtHandSide.


All productions of the form LeftHandSide : SE(qname) RightHandSide that were previously added to the grammar upon the first occurence of the element that
has qname qname are evaluated as follows when they are matched.


1. Evaluate the element content using the global element grammar for element qname 
2. Evaluate the remainder of event sequence using RightHandSide.


<br/>📎 8.4.3 Built-in Element Grammar



[Definition:] When no grammar exists for an element occuring in an EXI stream, a built-in element grammar is
created for that element- Built-in element grammars are initially generic and are progressively refined as the specific
content for the associated element is learned. All built-in element grammars are global element grammars and can be
uniquely identified by the qname of the global element they describe. At the outset of processing an EXI stream, the set of
built-in element grammars is empty.

Below is the initial set of productions used for all newly created built-in element grammars. The semantics describe how productions
are added to each built-in element grammar as the content of the associated element is learned.
__Macro__  *__ChildContentItems__ * :



*i*  __:=__  hd(arglist)

*j*  __:=__  hd(tail(arglist))

rhs{

SE((_*_))

ElementContent

EventCode(i,j)

}

rhs{

CH

ElementContent

EventCode(i,(j+1))

}

rhs{

ER

ElementContent

EventCode(i,(j+2))

}

rhs{

CM

ElementContent

EventCode(i,(j+3),0)

}

rhs{

PI

ElementContent

EventCode(i,(j+3),1)

}

Grammar{



lhs{

StartTagContent

}

rhs{

EE

EventCode(0,0)

}

rhs{

AT((_*_))

StartTagContent

EventCode(0,1)

}

rhs{

NS

StartTagContent

EventCode(0,2)

}

rhs{

SC

Fragment

EventCode(0,3)

}

ChildContentItems{

0

4

}

lhs{

ElementContent

}

rhs{

EE

EventCode(0)

}

ChildContentItems{

1

0

}

}



Note:

-   When the value of the selfContained option is false, the production with the terminal symbol SC on the rihgt-hand side is absent from the above grammar,
    and the use of the non-terminal macro ChildContentItems that has StartTagContent non-terminaö on the left-hand side (shown in bold above) gets expanded with variable values 0;3;
    instead of 0;4; used above.
-   When xsi:type attribute appears in an element where the built-in grammar is in effect, it MUST occur before any AT events of the sane element unless it is known that xsi:type 
    attribute will not impact grammar selection.
-   When xsi:nil attribute appears in an element where the built-in element grammar is in effect, it does not impact grammar selection and is not strictly required to occur before other
    AT events of the same element.


Semantics:

All productions in the built-in element grammar of the form LeftHandSide AT(_*_) RightHandSide are evaluated as follows:

1. Let qname be the qname of the attribute matched by AT(_*_)
2. If qname is not xsi:type or if a production of the form LeftHandSide : AT(xsi:type) with an event code of length does not exist in the current element grammar, create a production
   of the form LeftHandSide : AT(qname) RightHandSide with an event code 0 and increment the first part of the event code of each production in the current grammar with the non-terminal
   LeftHandSide on the left-hand side. Add this production to the grammar.
3. If qname is xsi:type, let target-type be the value of the xsi:type attribute and assign it the QName datatype representation (see 7.1.7 QName).
   If there is no namespace in scope for the specified qname prefix, set the uri of target-type to empty ("") and the localName to the full lexical value of the QName, including the prefix.
   Encode target-type according to section 7. Representing Event Content. If a grammar can be found for the target-type using the encoded target-type representation, evaluate the element contents using the
   grammar for target-type instead of RightHandSide.


The production of the form LeftHandSide : AT(xsi:type) RihghtHandSide that was previously added to the grammar upon the first occurence of the xsi:type attribute is evaluated as follows when it is matched:

1. Let target-type be the value of the xsi:type attribute and assign it the QName datatype representation (see 7.1.7 QName).
2. If there is no namespace in scope for the specified qname prefix, set the uri of target-type to empty("") and the localName to the full lexical value of the QName, including the prefix.
3. Represent the value of target-type according to section 7.Representing Event Content.
4. If a grammar can be found for the target-type type using the encoded target-type representation, evaluate the element contents using the grammar for target-type type instead of RightHandSide.

All productions of the form LeftHandSide: SC Fragment are evaluated as follows:

1. Save the string table, grammars and any implementation-specific state learned while processing this EXI Body.
2. Initialize the string table, grammars and any implementation-specific state learned while processing EXI Body to the state they held just prior to processing this EXI Body.
3. Skip to the next byte-aligned boundary in the stream if it is not already at such a boundary.
4. Let qname be the qname of the SE event immediately preceding this SC event.
5. Let content be the sequence of events following this SC event that match the grammar for element qname, up to and including the terminating EE event.
6. Evaluate the sequence of events (SD, SE(qname), content, ED) according to the Fragment grammar (see 8.4.2 Built-in Fragment Grammar).
7. Skip to the next byte-aligned boundary in the stream if it is not already at such a boundary.
8. Restore the string table, grammars and implementation-specific state learned while processing this EXI Body to that saved in step 1 above.

All productions in the built-in element grammar of the form LeftHandSide : SE(*) RightHandSide are evaluated as follows:

1. Let qname be the qname of the element matched by SE(_*_)
2. If a global element grammar does not exist for element qname, create one according to section 8.4.3. Built-in Element Grammar.
3. Create a production of the form LeftHandSide : SE (qname) RightHandSide with an event code 0
4. Increment the first part of the event code of each production in the current grammar with the non-terminal LeftHandSide on the left-hand side.
5. Add the production created in step 3 to the grammar
6. Evaluate the element content using the global element grammar for element qname.
7. Evaluate the remainder of event sequence using RightHandSide.

All productions of the form LeftHandSide : SE (qname) RightHandSide that were previously added to the grammar upon the first occurence of the element that has
the qname qname are evaluated as follows when they are matched:

1. Evaluate the element content using the global element grammar for element qname
2. Evaluate the remaoinder of event sequence using RightHandSide

All productions in the built-in grammar of the form LeftHandSide : CH RightHandSide are evaluated as follows:

1. If a production of the form, LeftHandSide : CH RightHandSide with an event code of length 1 does not exist in the current element grammar, create one with
   event code 0 ad increment the first part of the event code of each production in the current grammar with the non-terminal LeftHandSide on the left-hand side.
2. Add the production created in step 1 to the grammar.
3. Evaluate the remainder of the event sequence using RightHandSide.

All productions in the built-in elemen grammar of the form LeftHandSide : EE are evaluated as follows:

1. If a production of the form, LeftHandSide : EE with an event code of length 1 does not exist in the current element grammar, create
   one with event code 0 and increment the first part of the event code of each production in the current grammar with non-terminal LeftHandSide
   on the left-hand side.
2. Add the production created in step 1 to the grammar.
<br/>📎 8.5 Schema-informed Grammars





This section describes the schema-informed grammars used by EXI when schema infaormation is available to describe the contents of the EXI stream.
Schema information used for processing an EXI stream is either indicated by the header option schemaId, or communicated out-of-band in the absence of schemaId.


Schema-informed grammars accept all XML documents and fragments regardless of whether and how closely they match the schema. The EXI stream encoder encodes individual events
using schema-informed grammars where they are available and falls back to the built-in XML grammars where they are not. In general, events for which a achema-informed grammar exists will be
encoded more efficiently.


Unlike built-in XML grammars, schema-informed grammars are static and do not evolve, which permits the reuse of schema-informed gram,mars across the processing of multiple EXI streams.
This is a single outstanding difference between the two grammar systems.


It is important to note that schema-informed and built-in grammars are often used together within the context of a single EXI stream. While processing a schema-informed grammar, built-in grammars
may be created to represent schema deviations or elements that match wildcards declared in the schema. Even though these built-in grammars occur in the context of a schema-informed stream, they are
still dynamic and evolve to represent content learned while processing the EXI stream as described in 8.4 Built-in XML Grammars.

<br/>📎 8.5.1 Schema-informed Document Grammar



When schema information is available to describe the contents of an EXI stream, the following grammar describes the events that will occur in an EXI document.


Syntax



__Macro__  *__schema_informed_document_grammar__ * :

*global_elements*  __:=__  arglist

Grammar{



lhs{

Document

}

rhs{

SD

DocContent

EventCode(0)

}

lhs{

DocContent

}

*i*  __:=__  0

__for each__  *e*  __in__  global_elements

rhs{

SE(e)

DocEnd

EventCode(i)

}

i

__←__  (i+1)

rhs{

SE((_*_))

DocEnd

EventCode(i)

}

rhs{

DT

DocContent

EventCode((i+1),0)

}

rhs{

CM

DocContent

EventCode((i+1),1,0)

}

rhs{

PI

DocContent

EventCode((i+1),1,1)

}


            Note:


            - The variable as_identifier(i) in the grammar above is the number of global elements declared in the schema. arglist represents
              all the qnames of global elements sorted lexicographically, first by local-name, then by uri.
              
            lhs{

DocEnd

}

rhs{

ED

EventCode(0)

}

rhs{

CM

DocEnd

EventCode(1,0)

}

rhs{

PI

DocEnd

EventCode(1,1)

}

}

schema_informed_document_grammar{



G_0

G_1

G_2

G_3

}



Semantics:



In a schema-informed grammar, all productions of the form LeftHandSide:SE(_*_) RightHandSide are evaluated as follows:


1. Let qname be the qname of the element matched by SE(_*_)
2. If a global element grammar does not exist for element qname, create one according to section 8.4.3. Built-in Element Grammar.
3. Evaluate the element content using the global element grammar for element qname.
4. Evaluate the remainder of event sequence using RightHandSide.

<br/>📎 8.5.2 Schema-informed Fragment Grammar





When schema information is availble to describe the contents of an EXI stream, the following grammar describes the events that will occur in an EXI fragment.



Syntax


__Macro__  *__SchemaInformedFragmentGrammar__ * :

Grammar{



lhs{

Fragment

}

rhs{

SD

FragmentContent

EventCode(0)

}

*i*  __:=__  0

lhs{

FragmentContent

}

__for each__  *F_i*  __in__  arglist

rhs{

SE(F_i)

FragmentContent

EventCode(i)

}

i

__←__  (i+1)

rhs{

SE((_*_))

FragmentContent

EventCode(i)

}

rhs{

ED

EventCode((i+1))

}

rhs{

CM

FragmentContent

EventCode((i+2),0)

}

rhs{

PI

FragmentContent

EventCode((i+2),1)

}

}

SchemaInformedFragmentGrammar{

F_0

F_1

F_2

}





Note:


- The variable i in the grammar above represents the number of unique element qnames declared in the schema. The variables F_0, F_1, ... , F_(i-1) represent these qnames sorted
lexicographically, first by local-name, then by uri. If there is more than one element declared with the same qname, the qname is included only once. If all
such elements have the same schema type name and {nilable} property value, their content is evaluated according to the specific grammar for that element declaration.
Otherwise, their content is evaluated according to the relaxed Element Fragment grammar described in 8.5.3 Schema-informed Element Fragment Grammar.


Semantics:


In a schema-informed grammar, all productions of the form LeftHandSide: SE(_*_) RightHandSide are evaluated as follows:

1. Let qname be the qname of the element matched by SE(_*_)
2. If a global element grammar does not exist for element qname, create one according to section 8.4.3 Built-in Element Grammar.
3. Evaluate the element content using the global element grammar for element qname.
4. Evaluate the remainder of event sequence using RightHandSide.


<br/>📎 8.5.3 Schema-informed Element Fragment Grammar






[Definition:] When schema information is available to describe the contents of an EXI stream and more than one element is declared with the same qname, but not all
such elements have the same type name and {nilable} property value, the Schema-informed Element Fragment Grammar are used for processing the events that may occur
in such elements when they occur inside an EXI fragment or EXI Element Fragment. The schema-informed element fragment grammar consists of ElementFragment anf ElementFragmentTypeEmpty
which are defined below. ElementFragment is a grammar that accounts both element declarations and attribute declarations in schemas, whereas ElementFragmentTypeEmpty is a grammar
that regards only attribute delarations.

__Macro__  *__SchemaInformedElementFragmentGrammar__ * :

Grammar{



*As*  __:=__  as_nodeset(hd(arglist))

*Fs*  __:=__  as_nodeset(hd(tail(arglist)))

lhs{

ElementFragment_0

}

*i*  __:=__  0

__for each__  *A_i*  __in__  As

rhs{

AT(A_i)

ElementFragment_0

EventCode(i)

}

i

__←__  (i+1)

rhs{

AT((_*_))

ElementFragment_0

}

lhs{

ElementFragment_1

}

lhs{

ElementFragment_2

}

lhs{

ElementFragmentTypeEmpty_0

}

lhs{

ElementFragmentTypeEmpty_1

}

__for each__  *F_j*  __in__  Fs

F_j

}

SchemaInformedElementFragmentGrammar{

A_0

A_1

A_2

F_0

F_1

}

