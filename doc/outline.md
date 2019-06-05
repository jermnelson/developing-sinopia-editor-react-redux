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
Both of these `AddTwo` functions are equivalent in that they take two input parameters and return a 
single value. The `const` keyword for the second is a critical difference in that it creates an immutable 
function that cannot be reassigned to another function or variable in subsequent code while defining the
`AddTwo` function in the first form can be reassigned without the Babel compiler complaining. The `const`
keyword reduces the opportunity for accidentally introducing bugs by preventing the developer from
reassigning the function with potentially harmful or unintentional effects.  

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

The source for constructing the actual React components containing the HTML inputs used in the Sinopia Linked Data 
Editor are the properties in the `propertyTemplates` list. Each property contains a `propertyURI` attribute 
that is used as the RDF predicate in the constructed RDF while the `propertyLabel` is displayed in user interface
either as an HTML label or as a placeholder in the HTML input.
Other attributes for the property include *mandatory* set to true if the property is required, 
*repeatable* if the property can be duplicated. Early In the development of Sinopia, the decision was to limit and 
simplify what values are supported in the *type* property, with the simplest being `literal` with the others being 
`lookup`, `list`, and `resource`.
The `literal` property are for string values that are entered by the user, while the `list` and `lookup` types
references typeahead components for searching and linking external entities and values in the user interface. The 
*valueConstraint* attribute contains a number of sub-attributes like *defaults*, as list of one or more values that
are used to pre-populate the values in the input fields.   

The `resource` type is more complicated in that it references another Resource Template through the 
*valueConstraint*'s *valueTemplateRefs* attribute. The *valueTemplateRefs* attribute is a list of one or more 
Resource Template IDs that are then used to embedded a Resource Template into the user interface so that the 
values entered act as a separate entity that is then linked to the calling property. When generating the RDF,
an URI or blank-node of the embedded Resource Templates are positioned in the RDF object role with the original 
Resource Template as the RDF subject and the *propertyURI* property functioning as the RDF predicate.
 
Below is an example of a *propertyTemplate* with a type of **resource** that references the ID of the Resource
Template above, `resourceTemplate:bf2:Note` in the *valueConstraint.valueTemplateRefs* attribute:

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
HTML elements in defined classes and functions for building user interfaces. React classes and functions are often
defined in an extension of Javascript called JSX that is used to build components that can be assembled into
larger, more complex user interfaces. For example, a relative simple JSX component could represent a title on a 
page with an HTML H1 tag with valid Javascript expression embedded between curly brackets:

```javascript
const title = <h1>Book Title: {title}</h1>
```

React components also have two important Javascript arrays called `props` and `state`. 
The `props` array contains read-only properties that can be referenced within the component itself using the
curly braces syntax and are set when the component is rendered. Because the component's `props` are read-only,
this enforces the constraint that the `props` are immutable and follow a pure functional form. While creating
React component in pure functional form is possible, using the ES6 Class syntax allows for more comprehensible 
code that extends the base `React.Component` class. A React component class must implement a `render` method 
that returns the desired Javascript.

The title Javascript expression above could be refactor as a simple ES6 Class by extending the `React.Component`
and return the HTML snippet with the title prop referenced using the `this` keyword indicating a class instance
variable: 

```javascript
class Title extends React.Component {

  render() {
    return (<h1>Book Title: {this.props.title}</h1>)
  }
}
```

If coming from object-oriented languages, the temptation might be to create an hierarchy of React components but
this pattern is discouraged by the designers of React because React components are intended more for composition, 
where more complex React components are made-up of simpler components where the enclosing components pass properties
down through the child's initial props. To illustrate, here is a `Header` Reaction component that 
contains other React components and like `Title` and an `Author`. 

```javascript
class Header extends React.Component {

  render() {
    return(<header>
            <Title title={"Pride and Prejudice"} />
            <Author givenName={"Jane"} familyName={"Austen"} />
           </header>)
  }
}
```

This `Header` component could render this output and save to the web browser's DOM like this:

```html
<header>
  <h1>Book Title: Pride and Prejudicer</h1>
  <p>by Jane Austin</p>
</header>
```

### Sinopia's React Components
Initially the intention of the
Sinopia project team was to extend a LD4P fork of BFE but because of the BFE's 
Javascript code organization and lack of any unit or integration tests, Sinopia ended up being implemented
in a more-modern and decomposed manner that allowed for better testing by breaking down the user interface into 
decomposed and discreet React components.  


Sinopia's React components are built as JSX Class Components that are complied into Javascript functions
using [Babel][BABEL]. To build out the user interface for the Linked Data Editor, Sinopia uses a combination 
of third party React components along with custom React components. Sinopia's Linked Data editor is a hierarchy 
of components with `<RootContainer>` being the top-level React component. The `<RootContainer>` imports the 
`<OffCanvas>` that is composed of two children, the `<OffCanvasMenu>` and the `<OffCanvasBody>` components 
both from the [react-offcanvas](https://www.npmjs.com/package/react-offcanvas) module. The `<OffCanvasMenu>` 
presents a list of links to help and third-party resources displayed in a pane displayed when the 
**Help and Resources** link in the navigation bar is clicked. The other function of the `<RootContainter>` component
is to connect the React user interface to the Redux state store that will be explored in the next section.

The `<OffCanvasBody>` contains another React component from the 
[react-router-dom](https://reacttraining.com/react-router/) third-party module that allows for the easy generation of
a single-page application `<BrowserRouter>` [React][REACT] component that matches specific URL patterns into 
multiple routes for base route `/` to the homepage, the `/editor` route to editor forms, a `/templates` route that
displays a list of available templates and to upload a new resource template. Other supporting routes include the
`/login` to allow the user to authenticate using AWS Cogntio, The `/menu` for the off-canvas help and resources 
page, and a *404* route for unmatched routes entered by the user.   
 
* [`<RootContainer>`](https://github.com/LD4P/sinopia_editor/blob/master/src/components/RootContainer.jsx)
  * [`<OffCanvasBody>`](https://www.npmjs.com/package/react-offcanvas#offcanvasbody)
    * [`<BrowserRouter>`](https://reacttraining.com/react-router/web/api/BrowserRouter)
      * [`<App>`](https://github.com/LD4P/sinopia_editor/blob/master/src/components/App.jsx)
  * [`<OffCanvasMenu>`](https://www.npmjs.com/package/react-offcanvas#offcanvasmenu)
    * [`<CanvasMenu>`](https://github.com/LD4P/sinopia_editor/blob/master/src/components/CanvasMenu.jsx)

On the Sinopia's homepage, the top level React Components are outlined in the following image when the 
**Help and Resources** is clicked and the `<OffCanvasMenu />` is displayed:

![Root Container and Off-Canvas React Components](../img/root-container-off-canvas.png)

### HomePage and User Authentication
When accessing Sinopia's homepage at [sinopia.io](https://sinopia.io/), the `<App />` React 
component, using the `<Switch />` and `<Route />` React components from the react-router-dom package,
displays the `<HomePage />` and the standard Sinopia `<Footer />` React components. The `<HomePage />`
component contains three children, the `<Header />`, `<NewsPanel />`, and the 
`<DescPanel />` React components. The homepage's `<Header />` contains links to the `/templates` route, 
a link to Sinopia's Profile Editor, and finally a link that activates the `<OffCanavsMenu />`'s child's 
`<CanvasMenu />`. The `<NewsPanel />` has a `<NewsItem />` that allows the product owner to make 
announcements by editing and then creating a pull request that can then be reviewed and then pushed to the
AWS environments by the developers and system administrators. The `<DescPanel />` contains more general
description of the Sinopia project and what the project hopes to accomplish during the grant period and 
after.

The `<App />` also contains the `<LoginPanel />` component that uses the Amazon's [Amplify SDK](https://aws-amplify.github.io/docs/js/api)
to authenticate the user to the AWS Cognito service that then generates a valid [JSON Web Token](https://jwt.io/a) 
for user authentication in Sinopia and in Sinopia's backend [Trellis][TRELLIS] Linked Data Platform. For the 
initial release of Sinopia, all users are authorized to add, edit, or delete any resources stored in
Trellis. This may change in future releases with more restrictive user rights for resources created in 
specific group containers within Trellis. 

The React Component Hierarchy for the `<App />`'s `<HomePage />`, `<LoginPanel />`, and `<Footer />`:

* [`<App>`](https://github.com/LD4P/sinopia_editor/blob/master/src/components/App.jsx)
  * [`<LoginPanel>`]()
    * [`<Switch />`](https://reacttraining.com/react-router/web/api/Switch)
      * [`<Route />`](https://reacttraining.com/react-router/web/api/Route) => [`<HomePage />`]()
        * [`<Header />`]()
        * [`<NewsPanel />`]()
          * [`<NewsItem />`]()
        * [`<DescPanel />`]
  * [`<Footer />`]()

### Resource Templates Upload and Listing
From Sinopia's `<HomePage />`'s `<Header />`, an authenticated user is taken to the `/templates` route 
which contains 

### Linked Data Editor Forms
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
Library of Congress, VIAF, or ShareVDE or from internally created entities stored in Sinopia's server, catalogers
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
[TRELLIS]: https://www.trellisldp.org/
