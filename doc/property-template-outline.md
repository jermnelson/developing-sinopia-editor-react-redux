## `<PropertyTemplateOutline />` Component
The challenge of representing a resource's properties that include referencing a
target Resource Template needs to be solved in the user interface when using these
Profiles. In the Library of Congress BFE, the UI solution was to open a Modal that 
would render the target Resource Templates propertyTemplates. Those propertyTemplates
often reference further Resource Resources that require a new pop-up Modal until
the user loses the context with a whole series of Modals layered on top of one '
another. 

In an early prototype, Astrid Usong, Sinopia's User Interface designer, came
up with different approach that instead of using Modals, her design represented these
relationships as an outline. When demonstrated during a Sinopia-focused pre-conference 
at the 2019 Code4Lib conference in San Jose, CA., the participants preferred the 
outline view over the pop-up modal because it was easier to keep the context of the 
target Resource Template in relation to the original Resource Template when editing
a resource's metadata. The outline view also provide a visual hierarchy built on familiar
user interface tree where parent nodes can be expanded to reveal one or more 
`<PropertyTypeRow>` children.

* [`<PropertyTemplateOutline>`](https://github.com/LD4P/sinopia_editor/blob/master/src/components/editor/property/PropertyPanel.jsx)
  * [`<OutlineHeader>`](https://github.com/LD4P/sinopia_editor/blob/master/src/components/editor/property/OutlineHeader.jsx)
  * [`<PropertyTypeRow>`](https://github.com/LD4P/sinopia_editor/blob/master/src/components/editor/property/PropertyTypeRow.jsx)
    * [`<PropertyComponent>`](https://github.com/LD4P/sinopia_editor/blob/master/src/components/editor/property/PropertyComponent.jsx)
      * [`<InputLiteral>`]()
      * [`<InputLookupQA>`]()
      * [`<InputListLOC>`]()
    * [`<ResourceProperty>`]()
      * [`<PropertyActionButtons>`]()
      * [`<PropertyTemplateOutline>`]()    

To support this tree hierarchy of propertyTemplates one or more layers deep; the 
`<PropertyTemplateOutline>` React component is used and is composed of an `<OutlineHeader>`
made-up of a collapsed plus-sign icon that when expanded shows any child `<PropertyTypeRow>`
components. The `<PropertyTypeRow>` contains either `<PropertyComponent>` made up of either
`<InputLiteral>`, `<InputLookupQA>`, or `<InputListLOC>` , or a `<ResourceComponent>` that 
has `<PropertyActionButtons>` (containing an `<AddButton>` if the propertyTemplate's repeatable
property is true) and one or more `<PropertyTemplateOutline>`. When the `<PropertyTypeRow>` with 
`<ResourceComponent>` is expanded, a network call with the Resource Template ID is made to 
the Sinopia Server and the Resource Template JSON is retrieved and displayed in the expanded
view of the Resource with each of the target Resource Template's propertyTemplates having their
own `<PropertyTemplateOutline>`.
