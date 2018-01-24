## Frequently asked questions

### Discard change pop-up always displayed

***Q: My users see the 'discard change?' pop-up, even when they've made no changes on my Form Blade. What's wrong?*** 

The EditScope `root` property returns an object or array that includes uses of Knockout observables (`KnockoutObservable<T>` and `KnockoutObservableArray<T>`). Any observable located within the EditScope is designed to be modified/updated only by the user, via Form fields that are bound to EditScope observables. Importantly, these EditScope observables were not designed to be modified directly from extension TypeScript code. If extension code modifies an observable during the Form/Blade initialization process, the EditScope will record this as a user edit, and this accidental edit will trigger the `discard changes` pop-up when the user tries to close the associated Blade.  

SOLUTION:
Rather than initializing the EditScope by programmatically modifying/updating EditScope observables, use these alternative techniques:
* If the extension uses a ParameterProvider component to manage its EditScope, initialize the EditScope data in the '`mapIncomingData[Async]`' callback supplied to ParameterProvider.
* If the extension uses an EditScopeCache component to manage its EditScope, initialize the EditScope data in the '`supplyNewData`' and '`supplyExistingData`' callbacks supplied to EditScopeCache.
* If neither of the above techniques suits the scenario, the '`editScope.resetValue()`' method can be used to set a new/initial value for an EditScope observable in a way that *is not recorded as a user edit* (although this only works for observables storing primitive-typed values).  
  
* * *

### EditScope Location

***Q: I need to integrate my Form with an EditScope. Where do I get the EditScope?*** 

SOLUTION: This varies according to the UX design. Developers can choose between using a `ParameterProvider` component or `EditScopeCache` component as follows:

* Use `ParameterProvider` for the following scenario:
    * **Pop-up/dialog-like form** - The blade uses an ActionBar with an 'Ok'-like button that commits user edits. Typically, when the user commits the edits, the blade is implicitly closed, like a conventional UI pop-up/dialog. The blade uses `parameterProvider.editScope` to access the loaded/initialized EditScope.

* Use `EditScopeCache` for the following scenarios:
    * **Save/Revert blade** - There are 'Save' and 'Revert changes' commands in the CommandBar of the blade. Typically, these commands keep the blade open so the user can perform successive edit and save cycles without closing and reopening the form. The blade uses an `EditScopeView`, as specified in  `editScopeCache.createView(...)`, to load/acquire the EditScope.  

    * **Document editing** - The blade uses an `EditScopeView`, as specified in  `editScopeCache.createView(...)`, to load/acquire the EditScope.   The user can make edits to a single EditScope/Form model across multiple parent-child blades. The parent blade sends its `inputs.editScopeId` input to any child blade that edits the same model as the parent Blade. The child blade uses this `inputs.editScopeId` in its call to `editScopeView.fetchForExistingData(editScopeId)` to fetch the EditScope of the parent Blade. Scenarios like these resemble document editing. 

* * *
  
### What is an EditScopeAccessor?

***Q: Form fields have two constructor overloads, which should I use? What is an EditScopeAccessor?*** 

SOLUTION: For more information about EditScopeAccessors, see [portalfx-forms-working-with-edit-scopes.md#editscopeaccessor](portalfx-forms-working-with-edit-scopes.md#editscopeaccessor).

* * * 

### Type metadata
<!-- TODO:  Move this back to the TypeScript  document -->
***Q: When do I need to worry about type metadata for my EditScope?***

SOLUTION: For many of the most common, simple Form scenarios, there is no need to describe the EditScope/Form model in terms of type metadata. Generally speaking, supplying type metadata is the way to turn on *advanced* FX behavior, in much the same way that - in .NET - developers apply custom attributes to their .NET types to tailor .NET FX behavior for the types.  

Re: EditScope and Forms, extensions supply [type metadata](portalfx-data-typemetadata.md) for the following scenarios:  

#### Editable grid
#### Entity-type

* **Editable grid** - Today's editable grid was developed to work exclusively with EditScope 'entity' arrays. An EditScope 'entity' array is one where created/updated/deleted array items are tracked individually by EditScope. To grant this special treatment to an array in the EditScope/Form model, supply type metadata for the type of the array items (for the `T` in `KnockoutObservableArray<T>`). In the following, the type is marked as an "entity type" and, additionally, the property/properties that constitute the entity's 'id' are specified:  

In TypeScript:

{"gitdown": "include-section", "file":"../Samples/SamplesExtension/Extension/Client/V1/Forms/Scenarios/ChangeTracking/Models/EditableFormData.ts", "section": "formsEditScopeFaq#entityTypeMetadata"}

In C#:

{"gitdown": "include-section", "file":"../Samples/SamplesExtension/SamplesExtension.DataModels/Person.cs", "section": "formsEditScopeFaq#entityTypeMetadataCsharp"}
  
#### Track edits

* **Opting out of edit tracking** - There are Form scenarios where some properties on the EditScope/Form model are not meant for editing but are - possibly - for presentation only. In this situation, the extension can instruct EditScope to *not track* user edits for such EditScope/Form model properties, like so:

In TypeScript:  

    MsPortalFx.Data.Metadata.setTypeMetadata("Employee", {
        properties: {
            accruedVacationDays: { trackEdits: false },
            ...
        },
        ...
    });  

In C#:  

    [TypeMetadataModel(typeof(Employee))]
    public class Employee
    {
        [TrackEdits(false)]
        public int AccruedVacationDays { get; set; }

        ...
    }  

Extensions can supply type metadata to configure their EditScope as follows:  

* When using ParameterProvider, supply the '`editScopeMetadataType`' option to the ParameterProvider constructor.
* When using EditScopeCache, supply the '`entityTypeName`' option to '`MsPortalFx.Data.EditScopeCache.createNew`'.

To either of these, extensions pass the type name used when registering the type metadata via '`MsPortalFx.Data.Metadata.setTypeMetadata`'.  
  
* * * 

### Missing rows from editable grid
<!-- TODO:  Move this to the EditScope document -->
***Q: The user added/removed rows from my editable grid, but I don't see the corresponding adds/removes in my EditScope array.  What gives?***

SOLUTION: EditScope 'entity' arrays were designed with a few requirements in mind:
* The user's edits need to be serialized so that Journey-switching works with unsaved Form edits. For editing large arrays, the FX should not serialize array edits by persisting two full copies of the (could-be-large) array.
* In the UI, the FX will want to render an indication of what array items were created/updated/deleted. In some cases, array removes need to be rendered with strike-through styling.
* Array adds/remove need to be revertable for some scenarios.

The resulting EditScope design was to make EditScope 'entity' arrays behave differently than regular JavaScript arrays.  Importantly:
* 'Creates' are kept out-of-band
* 'Deletes' are non-destructive

To conveniently see the *actual* state of an EditScope 'entity' array, use the '`getEntityArrayWithEdits`' EditScope method. This returns:
* An array that includes 'created' entities and doesn't include 'deleted' entities
* Discrete arrays that individually capture 'created', 'updated' and 'deleted' entities  

This '`getEntityArrayWithEdits`' is particularly useful in ParameterProvider's '`mapOutgoingDataForCollector`' callback when returning an edited array to some ParameterCollector:

{"gitdown": "include-section", "file":"../Samples/SamplesExtension/Extension/Client/V1/ParameterCollection/ParameterProviders/ViewModels/ProviderViewModels.ts", "section": "formsEditScopeFaq#getEntityArrayWithEdits"}

#### Apply array as edits

And there is a corresponding '`applyArrayAsEdits`' EditScope method that simplifies applying edits to an existing EditScope 'entity' array. This is often done in a ParameterCollector's '`receiveResult`' callback:

{"gitdown": "include-section", "file":"../Samples/SamplesExtension/Extension/Client/V1/ParameterCollection/FormWithCollectors/ViewModels/FormWithCollectorsBladeViewModel.ts", "section": "formsEditScopeFaq#applyArrayAsEdits"}

This pair of EditScope methods significantly simplifies working with EditScope 'entity' arrays.  
  
* * *

### Keeping EditScope from tracking changes

***Q: Some of my Form data is not editable. How do I keep EditScope from tracking changes for this data?***

SOLUTION: For more information about configuring an EditScope by using type metadata, see [portalfx-extensions-faq-edit-scope.md#track-edits](portalfx-extensions-faq-edit-scope.md#track-edits).
  
* * *

### Key-value pairs
<!-- TODO:  Move this to the EditScope or edit grid document -->
***Q: My Form data is just key/value-pairs. How do I model a Dictionary/StringMap in EditScope? Why can't I just use a JavaScript object like a property bag?***

SOLUTION: Like all models and view models treated by the Azure Portal FX, after the object/array is instantiated and returned to the FX in the process of rendering the UI, any subsequent mutation of that object/array should be done by changing/mutating **Knockout** observables. Portal controls and Knockout HTML template rendering both subscribe to these observables and re-render only when these observables change value.  

This poses a challenge to the common JavaScript programming technique of treating a JavaScript object as a property bag of key-value-pairs. If an extension only adds or removes keys from a model or ViewModel object, the FX will not be aware of these changes. EditScope will not recognize these changes as user edits and therefore such key adds/removes will put EditScope edit-tracking in an inconsistent state.  

So, how does an extension model a Dictionary/StringMap/property bag when using the Portal FX and EditScope?  

The pattern is to develop the Dictionary/StringMap/property bag as an observable array of key/value-pairs, like `KnockoutObservableArray<{ key: string; value: TValue; }>`.  

Often, additionally, it is important to let users edit the Dictionary/StringMap/property bag using *an editable grid*. In such cases, it is important to describe the array of key/value-pairs as an 'entity' array since editable grid can only be bound to an EditScope 'entity' array. See [above](#editable-grid) re: how to develop type metadata to use the array with editable grid.  

Here's a sample that does something similar, converting - in this case - an array of strings into an 'entity' array for consumption by editable grid.  

* * *

#### Modeling your data as an 'entity' array

SOLUTION: 
{"gitdown": "include-section", "file":"../Samples/SamplesExtension/Extension/Client/V1/ParameterCollection/ParameterProviders/ViewModels/ProviderViewModels.ts", "section": "formsEditScopeFaq#makeEntityForEditableGrid"}  

* * *

#### Converting your data to an 'entity' array for consumption by editable grid

SOLUTION: 
{"gitdown": "include-section", "file":"../Samples/SamplesExtension/Extension/Client/V1/ParameterCollection/ParameterProviders/ViewModels/ProviderViewModels.ts", "section": "formsEditScopeFaq#makeEntityForEditableGrid2"}  
  
* * *

### Q: What should be returned from 'saveEditScopeChanges'? I don't understand the different values of the `AcceptEditScopeChangesAction` enum.

<!-- TODO:  Move this to the EditScope document -->

SOLUTION: 
When creating an EditScopeCache, the `saveEditScopeChanges` callback supplied by the extension is called to push EditScope edits to a server/backend. This callback returns a `Promise` that should be resolved when the 'save' AJAX call completes, which occurs after the server/backend accepts the user's edits. 

When the extension resolves this `Promise`, it can supply a value that instructs the EditScope to  reset itself to a clean/unedited state. If no such value is returned during Promise resolution, then the EditScope is reset by default.  This means taking the user's client-side edits and considering these values to be the new, clean/unedited EditScope state. This works for many scenarios.  

There are scenarios where the default `saveEditScopeChanges` behavior does not work, like: the following. 
* During 'save', the server/backend produces new data values that need to be merged into the EditScope.
* For "create new record" scenarios, after 'save', the extension should clear the form, so that the user can enter a new record.

For these cases, the extension will resolve the `saveEditScopeChanges` Promise with a value from the `AcceptEditScopeChangesAction` enum. These values allow the extension to specify conditions like the following.
* The EditScopeCache is to implicitly reload the EditScope data as part of completing the 'save' operation.
* The EditScope's data is to be reverted/cleared as part of completing the 'save' operation (the "create new record" UX scenario).

For more information about each enum value, see the jsdoc comments around `MsPortalFx.Data.AcceptEditScopeChangesAction` or in `MsPortalFxDocs.js` in Visual Studio or any code editor.

#### Caveat / anti-pattern

There is an important anti-pattern to avoid here re: '`saveEditScopeChanges`'. If the AJAX call that saves the user's edits fails, the extension should merely **reject** the '`saveEditScopeChanges`' Promise (which is natural to do with Q Promise-chaining/piping). The extension *should not* resolve their Promise with '`AcceptEditScopeChangesAction.DiscardClientChanges`', since this will lose the user's Form edits (a data-loss bug).
  
* * *

### Common error: "Entity-typed object/array is not known to this edit scope..."  

SOLUTION: 
EditScope data follows a particular data model. In short, the EditScope is a hierarchy of 'entity' objects. By default, when the EditScope's '`root`' is an object, this object is considered an 'entity'. The EditScope becomes a hierarchy of 'entities' when:
* the EditScope includes an array of 'entity' objects
* some EditScope object includes a property that is 'entity'-typed  

An object is treated by EditScope as an 'entity' when type metadata associated with the object is marked as an 'entity' type (see [here](#entity-type) and the EditScope video/PPT [here](portalfx-forms-working-with-edit-scopes.md) for more details).

Every 'entity' object is tracked by the EditScope as being created/updated/deleted. Extension developers define 'entities' at a granularity that suit their scenario, making it easy to determine what in their larger EditScope/Form data model has been user-edited.  

Now, regarding the error above, once the EditScope is initialized/loaded, 'entities' can be introduced and removed from the EditScope only via EditScope APIs. It is an unfortunate design limitation of EditScope that extensions cannot simply make an observable change to add a new 'entity' object or remove an existing 'entity' from the EditScope. If an extension tries to do an observable add (make an observable change that introduces an 'entity' object into the EditScope), they'll encounter the error discussed here.  

To correctly (according to the EditScope design) add or remove an 'entity' object into/out of an EditScope, here are APIs that are useful:
* ['`applyArrayAsEdits`'](#apply-array-as-edits) - This API accepts a new array of 'entity' objects. The EditScope proceeds to diff this new array against the existing EditScope array items, determine which 'entity' objects are created/updated/deleted, and then records the corresponding user edits.
* '`getCreated/addCreated`' - These APIs allow for the addition of new, 'created' entity objects. The '`getCreated`' method returns a distinct, out-of-band array that collects all 'created' entities corresponding to a given 'entity' array. The '`addCreated`' method is merely a helper method that places a new 'entity' object in this '`getCreated`' array.
* '`markForDelete`' - 'Deleting' an 'entity' from the EditScope is treated as a non-destructive operation. This is so that - if the extension chooses - they can render 'deleted' (but unsaved) edits with strike-through styling (or equivalent). Calling this '`markForDelete`' method merely puts the associated 'entity' in a 'deleted' state.  

* * *

#### Common error scenario

SOLUTION: 
Often, extensions encounter this "Entity-typed object/array is not known to this edit scope..." error as a side-effect of modeling their data as 'entities' binding with [editable grid](#editable-grid) in some ParameterProvider Blade.  Then, commonly, the error is encountered when applying the array edits in a corresponding ParameterCollector Blade.  Here are two schemes that can be useful to avoid this error:
* Use the [`applyArrayAsEdits'](#apply-array-as-edits) EditScope method mentioned above to commit array edits to an EditScope.
* Define type metadata for this array *twice* - once only for editing the data in an editable grid (array items typed as 'entities'), and separately for committing to an EditScope in the ParameterCollector Blade (array items typed as not 'entities').  
  
* * *

### Common error: "Encountered a property 'foo' on an editable object that is not present on the original object..."  

SOLUTION: 
As discussed in [portalfx-extensions-faq-edit-scope.md#key-value-pairs](portalfx-extensions-faq-edit-scope.md#key-value-pairs), the extension should mutate the EditScope/Form model by making observable changes and by calling EditScope APIs. For any object residing in the EditScope, merely adding/removing keys cannot be detected by EditScope (or by the FX at large) and, consequently, edits cannot be tracked. When an extension *attempts* to add/remove keys from an EditScope object, this puts the EditScope edit-tracking in an inconsistent state. When the EditScope detects such an inconsistency, it issues the error above to encourage the extension developer to use (exclusively) observable changes and EditScope APIs to mutate/change the EditScope/Form model.  

* * *