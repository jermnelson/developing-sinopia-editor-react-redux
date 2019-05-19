# Introduction
## Background of the Sinopia Stack
Funded the Mellon Foundation and overseen as part of the  LD4P (Linked Data for Production) grant,
[Sinopia][SINOPIA] available at [https://sinopia.io][SINOPIA]. A critical deliverable for a second
phase of the grant was a collaborative linked-data cataloging environment that could be used in a
production environment. 

Sinopia is developing using ECMAScript 6 features and conventions that is then transplained into
Javascript using [Babel][BABEL]. 

Two partners in the Sinopia project provide an important service called Questions Authorities (QA), a joint
project of Cornell University and the University of Iowa. QA is an API service that queries Lucene indexes
of both RDF and non-RDF datastores and returns JSON data of the results. 

## Functional Javascript 
Within the programming community, the ubiquity of Javascript as the default programming language for 
scripting websites and applications means most developers have at least a passing knowledge of the
language. While client-side manipulation of the HTML DOM (document object model) has changed over the
years with the introduction of such Javascript libraries as [JQuery][JQUERY] and the continued addition of different
supported technologies, Javascript has morphed into a server-side language with the emergence of the [Node.js][NODE]
ecosystem.

With the features Javascript, multiple approaches for programming like imperative, object-oriented, and 
in the past ten years, functional methodologies. 

## Resource Templates and Profiles
The Library of Congress's [Bibframe Editor][BFE] is a BIBFRAME-focused linked-data editor that was an 
important source of inspiration for Sinopia, with the intention to do a LD4P for of BFE but because of the
Javascript code organization and lack of any unit or integration tests, Sinopia ended up being implemented
in a more-modern and decomposed manner that allowed for modern testing and better distributed and decomposed
into React components with Redux state management. 

## React Components
An open source project sponsored by Facebook, [React][REACT] is a Javascript library that wraps
HTML elements in defined classes and functions for building user interfaces.    

Sinopia's React components are built as [Babel][BABEL] JSX Class Components that are Babel transplained 
into Javascript functions. Although Sinopia's initial  


 
## Editor's Redux State
Initially, Sinopia's React components were structured with extensive `props` and state changes
to represent and respond to actions and user expectations to accepting values both from any 
parent information and also push state information to any composited child components.

### React Components Reducers
For the React components in Sinopia, they reflect or mutate the global Redux state through two 
methods, `mapStateToProps` and `mapDispatchToProps`. 


### RDF Reducer
As the Redux state captures the event of a user entering or linking data to when it happens in the 
React components, at any particular point-in-time Sinopia can generates RDF. 

## Linking to Other Sources

## Entity Management with the Sinopia Server

## Next Steps
### Context-specific React and Web Components
### Custom Reducers
An early requirement of Sinopia is to provide the ability of catalogers to do two different, but related, workflows.
For pre-existing graphs of RDF entities either available from third party authorities like 
Library of Congress, VIAF, or ShareVDEn or from internally created entities stored in Sinopia's server, catalogers
will need to be able either edit, add, or delete triples about these entities or derive a new RDF entity by copying
those triples, much like the copy-cataloging current MARC based work-flows in libraries. 


## References

## About the author
Jeremy Nelson is a software engineer with the [Stanford University Libraries][SUL] and technical lead
on the [Sinopia][SINOPIA] project. He is also co-founder and CTO of [KnowledgeLinks.io][KNOW_LNKS].

[BABEL]: https://babeljs.io/
[BFE]: http://bibframe.org/editor
[JQUERY]: https://jquery.com/
[KNOW_LNKS]: http://knowledgelinks.io/
[NODE]: https://nodejs.org/en/
[REACT]: https://reactjs.org/
[SINOPIA]: https://sinopia.io
[SUL]: https://library.stanford.edu/
