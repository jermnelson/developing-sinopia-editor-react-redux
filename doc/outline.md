# Introduction
## Background of Sinopia
Funded by the Mellon Foundation as part of the  LD4P (Linked Data for Production) grant,
[Sinopia][SINOPIA], available at [https://sinopia.io][SINOPIA] is a cloud-based collaborative linked-data 
cataloging environment that could be used in a production environment. The Sinopia project's primary
software development team are members of the Stanford University Libraries with Michelle Futornick is
project owner who directs the priorities and communicates the user's needs and requirements to the 
development team in an iterative manner following [Agile](https://www.agilealliance.org/) software practices.

The Agile development methodology provides an approach and processes for responding to changes in 
requirements or environment by emphasizing flexibility and adapting to those changes. Specific Agile practices
of the Stanford development team uses include pair programming, testing, stand-up meetings, planning sessions,
and sprints. In pair programming two or more developers collaborate together to code or solve specific problems
by having one programmer type while both developers talk through the problem together thereby increasing code
comprehension and knowledge transfer within the team. Another critical Agile practice used by the Sinopia 
development team is creating unit tests for specific Javascript modules and integration tests that test interactions
between multiple components and modules in the code base through scripting actions in a headless web browser.
The Sinopia development team also has daily stand-up meetings where the developers check-in with each other and 
the product owner on progress and challenges encountered in the previous day and to plan for the upcoming day.
Finally, the Sinopia team organized to one-to-two week sprints that attempted to accomplish a specific goals and
tasks within the time period that also included a planning meeting at the beginning of the sprint and weekly
storytime meeting where challenges and issues that usually required the project owner feedback and decisions 
that are then incorporated into the sprint execution.  

At the start of the Sinopia project, the decision was made to build the linked data editor based on previous
development work done by the Library of Congress in the creation of their [BIBFRAME Editor][BFE] or BFE along
with supporting projects like the [BIBFRAME Profile Editor](https://github.com/lcnetdev/profile-edit) and 
[Verso](https://github.com/lcnetdev/verso). The Library of Congress catalogers have been using the BFE to 
cataloging books and other materials for specific workflows related to the Library of Congress infrastructure. 
Christine Harlow, now at Temple University, created Sinopia's architecture for deploying first a forked 
version of the Library of Congress BIBFRAME Profile in [Amazon Web Services](https://aws.amazon.com/) (AWS) with
the Linked Data Editor to follow. Because the initial BFE codebase was a monolithic Javascript module and the
BFE lacked any unit or integration tests, it was decided that a hard fork of the BFE code base for Sinopa Linked 
Data Editor was necessary in order to address design and implementation shortcomings present in the BFE. In 
addition, a new backend architecture was necessary that leveraged AWS services like 
[Cognito](https://aws.amazon.com/cognito/) a user authentication service and to host an instance of 
[Trellis](https://www.trellisldp.org/), a linked-data platform that allows the use of a Postgres relational 
database to store the output of the Linked Data Editor.   

Two partners in the Sinopia project provide an important service called [Questions Authorities](https://github.com/samvera/questioning_authority) 
(QA), a joint project of Cornell University and the University of Iowa. QA is an API service that queries Lucene 
indexes of both RDF and non-RDF datastores and returns JSON data of the results. This service provides an intermediary 
source of lookup data for the Library of Congress [subject](http://id.loc.gov/authorities/subjects.html) and 
[name](http://id.loc.gov/authorities/names.html) authorities, the Getty Linked 
Data [vocabularies](http://www.getty.edu/research/tools/vocabularies/lod/) and [ShareVDE](http://www.share-vde.org/).

## Functional Javascript 
Within the programming community, the ubiquity of Javascript as the default programming language for 
scripting websites and applications means most developers have at least a passing knowledge of the
language. While client-side manipulation of the HTML DOM (document object model) has changed over the
years with the introduction of such Javascript libraries as [JQuery][JQUERY] and the continued addition of different
supported technologies, Javascript has morphed into a server-side language with the emergence of the [Node.js][NODE]
ecosystem.

Sinopia uses ECMAScriptfeatures and conventions that is then converted into Javascript using the [Babel][BABEL] 
compiler. ECMAScript 6 features used in Sinopia include class declarations such  `class Input {...}`, importing of
Javascript modules like `import Input from './Input` and to support the import, using the export feature like 
`export const Input`. Because Sinopia's Linked Data Editor communicates in an asynchronous fashion with the 
Sinopia server back-end, the ECMAScript 7 and 8 features like Promises with `async` and `await` keywords are used
in the code. Finally, Sinopia extensively uses the ECMAScript spread operator as well as the arrow function syntax
`() => {}` throughout the code base.

Javascript supports multiple programming paradigms like imperative, object-oriented, and 
in the past ten years, the functional programming approach. The popularity of building functional [React][REACT] 
and [Redux][REDUX] code for web browser and native user interfaces. Javascript built-in `map` and `reduce` 
operators along with support for currying and other functional-friendly constructs like the `=>` arrow functional 
form. The key insight in writing functional Javascript is focusing on functions with minimal side-effects and
deterministic expectations that given a set of inputs, the function will return a set of outputs.  

For example, a traditional Javascript function is declared using the `function` keyword:
```javascript
function AddTwo (x,y) {
  return x+y;
}
```

A more functional ECMAScript using the `const` keyword and arrow functional form looks like:

```javascript
const AddTwo = (x,y) => {
  return x+y
}
``` 


## Resource Templates and Profiles
The Library of Congress's [Bibframe Editor][BFE] is a BIBFRAME-focused linked-data editor that was an 
important source of inspiration for Sinopia. To support a wide range of cataloging use cases, the BFE 
followed a two-part strategy where a user first creates a JSON Profile containing one or more Resource 
Templates in the BIBFRAME Profile Editor that is saved in the backend Verso server which can then be
loaded into the BFE to generate different types of user interfaces depending on the requirements for the 
material. So, for example, a Monograph Profile contains a BIBFRAME Work and Instance Resources
Templates along with any supporting Resource Templates that a cataloger could then load into the BFE to 
catalog a Book Instance or Work. The Profile JSON file containing one or more Resource Templates is in effect
a Domain Specific Language (DSL) for generating the user interface needed by the cataloger to cataloging 
a specific type of BIBFRAME entity. The Profile DSL, while developed for the BIBFRAME ontology, is generic
enough that other linked-data vocabularies can be used in addition to BIBFRAME but must be specified within 
a Resource Template. 
 

A Resource Template contains a number of properties that define such things as the label to display in the
user interface, an URI to use as the RDF type, and one or more properties contained in the `propertyTemplate`
list. Sinopia, following the BFE's example, only displays one top-level Resource Template at a time in the editor
user interface. A source of confusion early on in the project arose when analyzing the Library of Congress set of 
Profiles because the id of a profile was often duplicated as one of the ids of the contained Resource Templates. 
This was fixed in later iterations of these Library of Congress Profiles.


Here is an example of a Resource Template for a BIBFRAME Note that could then be referenced by other Resource 
Templates:

```javascript
{
  "id": "resourceTemplate:bf2:Note",
  "resourceURI": "http://id.loc.gov/ontologies/bibframe/Note",
  "resourceLabel": "Note",
  "propertyTemplates": [
    {
      "propertyURI": "http://www.w3.org/2000/01/rdf-schema#label",
      "propertyLabel": "Note",
      "mandatory": "false",
      "repeatable": "false",
      "type": "literal",
      "resourceTemplates": [],
      "valueConstraint": {
        "valueTemplateRefs": [],
        "useValuesFrom": [],
        "valueDataType": {},
        "editable": "true",
        "repeatable": "false",
        "defaults": []
      }
    }
  ]
}
```

If the property is required, and if the property can be
duplicated. In the development of Sinopia, the decision was to limit and simplify what values are supported
in the `type` property, with the simplest being for



```javascript
    {
      "propertyURI": "http://id.loc.gov/ontologies/bibframe/note",
      "propertyLabel": "Notes about the Work",
      "mandatory": "false",
      "repeatable": "true",
      "type": "resource",
      "resourceTemplates": [],
      "valueConstraint": {
        "valueTemplateRefs": [
          "resourceTemplate:bf2:Note"
        ],
        "useValuesFrom": [],
        "valueDataType": {},
        "defaults": []
      }
    }
```

## React Components
An open source project sponsored by Facebook, [React][REACT] is a Javascript library that wraps
HTML elements in defined classes and functions for building user interfaces. 

Initially the intention of the
Sinopia project team was to extend a LD4P fork of BFE but because of the
Javascript code organization and lack of any unit or integration tests, Sinopia ended up being implemented
in a more-modern and decomposed manner that allowed for better testing by breaking down the user interface into 
decomposed and discreet React components.  

Sinopia's React components are built as [Babel][BABEL] JSX Class Components that are Babel transplained 
into Javascript functions. Although Sinopia's initial  

## Editor's Redux State
Initially, Sinopia's React components were structured with extensive `props` and state changes
to represent and respond to actions and user expectations to accepting values both from any 
parent information and also push state information to any composited child components. As the team
became more conversant with Redux, the refactoring implementation of the React components simplified
both the model instanations as well as what props and state.

### React Components Reducers
For the React components in Sinopia, they reflect or mutate the global Redux state through two 
methods, `mapStateToProps` and `mapDispatchToProps` as recommended in the official React-Redux
[documentation](#redux-ref-01).

### RDF Reducer
As the Redux state captures the event of a user entering or linking data to when it happens in the 
React components, at any particular point-in-time Sinopia can generates RDF. 

## Linking to Other Sources
Sinopia is able to link to other sources through one or more custom [React][REACT] components 
that provide a typeahead input using a third party node.js module called [React Bootstrap Typeahead](http://ericgio.github.io/react-bootstrap-typeahead/).  


### `InputListLOC` Library of Congress **id.loc.gov** Component
The Library of Congress's Linked Data Service at [http://id.loc.gov](http://id.loc.gov/) provides a number
of subjects, thesauri, classifications, and other vocabularies. For large linked data services 
Sinopia uses Questioning Authorities service but for smaller vocabularies the `InputListLOC` React
component directly connects and retrieves a JSON list that is presented to the end user as a 
lookahead. 

Here is a screen shot of the `InputListLOC` component within Sinopia:

### `InputLookupQA` Questioning Authorities Component

### `InputLookupWikidata` Wikidata Component

### `InputLookupRDARegistry` RDA Registry Component
Another source of controlled vocabularies required by the initial cohort of libraries 
are curated and hosted at the RDA Registry website. 
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

1. https://react-redux.js.org/using-react-redux/connect-mapstate#usage-guidelines 
<a name="redux-ref-01"></a>

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
