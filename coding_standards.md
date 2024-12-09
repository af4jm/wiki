---
title: coding standards
permalink: /coding_standards/

---

([home](/))

# coding standards

This was written in 2015, much of this is obsolete. I changed the name of the company I worked for to ___ before posting here.

## Branding & Identity

- all owned code must be in assemblies whose names start `___.` and in namespaces starting `___.`, followed in either case by the name of the product, <bdi lang="la" title="in other words [lat]">i.e.</bdi> ___. or ___.
- in assembly & namespace names, always use PascalCasing, <bdi lang="la" title="in other words [lat]">i.e.</bdi> NameName not nameName
- in JavaScript, always use a top-level namespace of ___, and camelCasing everywhere else, <bdi lang="la" title="in other words [lat]">i.e.</bdi> nameName not NameName
- in namespace and object names, standard abbreviations of 3 letters or more should not be in all caps, <bdi lang="la" title="in other words [lat]">i.e.</bdi> Html or xml, not HTML or XML (1<sup>st</sup> letter depends on Pascal or camel casing)
    - 2 letter abbreviations such as IO may be in all caps when using Pascal casing

## Maintainability

- avoid putting T-SQL outside the database, JavaScript outside .js files, and CSS outside .css files; in html, avoid `javascript:` or wiring event handlers in attributes and avoid `style=""` attributes
    - script tags should usually appear just before the `</body>` tag unless there’s a necessity to put them higher or in the header, and should always be of the form `<script type="text/javascript" src=""></script>` instead of `<script type="text/javascript">// JS code here</script>`
        - exception: `modernizr.js` only works inside `<head>`
    - style tags should always be of the form `<link rel="stylesheet" href="theme.css"/>` (which must be inside the `<head>`) instead of `<style>// CSS code here</style>`
        - exception: once it becomes widely adopted (it’s new to HTML5 and currently supported only by FireFox), `<style type="text/css" scoped="scoped">// CSS code here</style>` in the `<body>` will become an acceptable alternate to `style=""` attributes, the scoped attribute limits the contained styles to the parent object of the style tag and all descendants of that parent object
- always call `Dispose()` on all objects that implement `IDisposable`, that are either instantiated or returned from function calls, unless it’s being returned from that function. In C# this is typically done with a `using` block.
- avoid referencing namespace `System.Data` outside data access classes and namespace `System.Web` outside of UI-specific classes, in both cases this includes child namespaces
    - exception: MVC and WebAPI require these references in many of their plumbing classes, which are not strictly UI-specific, but should always be in the main web project, or in an Areas project
- avoid referencing private member fields, instead reference the property which is wrapping the field
- if a field is set on class initialization and should never be reset, always mark it as `readonly`
- avoid multiple `out` parameters, create an object instead (in SQL, use an additional result set)
- avoid magic numbers/<wbr/>strings, prefer the configuration file, the database, a static class or module of constants, or an enumeration as appropriate
- never use deprecated or obsolete methods, as they may disappear or error in the future
- never check in code that doesn’t compile without warnings
- never check in code that hasn’t been exercised
    - it should be ready to hand off to QA with confidence that it will pass
    - if an intermediate check-in is necessary, existing functionality should not be broken (<bdi lang="la" title="in other words [lat]">i.e.</bdi>, no yellow screen of death)
- never suppress compiler warnings unless there’s a documented justification for doing so
- when rethrowing an exception, always use `throw;`, never use `throw ex;` to avoid resetting the stack trace. Keep in mind that catching always unwinds the stack, so do that only if you are taking meaningful action
- never catch an exception unless you’re taking some action other than rethrowing… instead of catching & rethrowing, remove the `try`–`catch` and let the exception bubble since the result is the same
- if you need to catch an exception and throw an exception of a different type, always set `InnerException` to the original exception
- never optimize for performance without documenting the bottleneck, with optimizing compilers the bottleneck is probably not where you think it is
- when commenting out the entire body of a code block, comment the entire block (<bdi lang="la" title="in other words [lat]">i.e.</bdi> don’t leave an empty if)… unless you know you’re going to need it again soon, delete it instead as it can be retrieved from source control if necessary
- always use PascalCasing for naming assemblies, .NET code files, namespaces, classes/<wbr/>structures, methods/<wbr/>functions, properties, and events, and all SQL database objects (tables, procedures, etc.); always use camelCasing for all JavaScript file and object names, and in .NET and SQL for fields, parameters, and local variables; never use Hungarian notation, except…
    - `I` for interfaces
    - `v` for database views which are not updatable
    - JavaScript constructors, intended to be called with `new`, should be PascalCased

## Source Control

- never check in code without a comment in the check-in window
    - identify the associated work item for reference
- always check in everything necessary to build the solution, including the solution file
- the `bin` and `obj` directories should never exist in source control, for binaries use NuGet if possible, otherwise create a `lib` folder
    - in the NuGet packages folder, only check in the root packages file, but none of the packages. Set the solution so the packages are downloaded upon build.
    - exception: in a web site, `.refresh` files must be checked in since there is no project file, and they live in the `bin` folder
- avoid checking in code that contains tabs, set your IDE to use spaces (preferably 4) instead
- avoid checking in code with `// TODO:` comments, checked in code should be “done”/<wbr/>production-ready
- avoid checking in code with large blocks commented out, if the code is no longer needed remove it, it can be retrieved from source control history if it winds up being needed again at a later date

## Visual Studio

- when creating new solutions, always check the solution file into source control
- for references between projects in the same solution, always use project references… only use file references for assemblies outside the solution
- for WebForms, always use web projects, never use web sites… the project file is needed to track references, excluded files, build customizations, etc.
- prefer suffixes rather than Hungarian notation for disambiguating controls, <bdi lang="la" title="in other words [lat]">i.e.</bdi> a control and its label (prefer `NameLabel` instead of `lblName`), this allows similar controls to be grouped in IntelliSense and, when using WebForms, it allows writing selectors as `[id$='_NameLabel']` instead of needing to change `ClientIdMode` to make \#NameLabel work
    - note: don’t omit the underbar, which WebForms use to separate what it adds to the beginning of the ID from what is specified in the attribute, <bdi lang="la" title="in other words [lat]">i.e.</bdi> `[id$='txtName']` would match anything ending in `txtName`, <bdi lang="la" title="in other words [lat]">i.e.</bdi> both `txtName` and `reqtxtName`, leading to hard-to-find bugs; one more reason to avoid type prefixes… another way to avoid the potential conflict is qualify the selector with the type, <bdi lang="la" title="in other words [lat]">i.e.</bdi> `input[id$='_txtName']`
    - note: even this is not foolproof, as certain controls, such as radio buttons, may have a suffix, in which case the correct selector would be `[id|='_someID_']`
    - note: while the CSS specification considers the escaping `\` optional prior to the `_`, some older browsers, including IE6, ignore CSS selectors that begin with `_`, escaping avoids the issue

## Globalization & Localization

- all time values should be stored, transmitted, and calculated in UTC. In T-SQL use `SYSUTCDATETIME()` not `GETDATE()`. To display a time to the user, convert it to the appropriate time zone in the UI tier.
- when comparing strings, always use `string.Equals()` or `string.Compare()` instead of operators
    - if you need to compare strings case-insensitive and can’t use these methods (<bdi lang="la" title="in other words [lat]">i.e.</bdi> `switch`), always convert to upper case (via `string.ToUpperInvariant()` or `string.ToUpper()` in .NET or `string.toUpperCase()` in JavaScript), never to lower case (see “Turkish i problem” in your favorite search engine)
- when calling a method on string that accepts a `StringComparison`, always pass one
    - exception: passing a `CultureInfo` and `CompareOptions` (if it takes one) instead
- always pass an `IFormatProvider` (and a format string) to a method that takes them (<bdi lang="la" title="in other words [lat]">i.e.</bdi>, `ToString()` or `string.Format()`)
    - if the result will be displayed on screen, use `CultureInfo.CurrentCulture`, `DateTimeFormatInfo.CurrentInfo`, or `NumberFormatInfo.CurrentInfo`
    - if the result will be used internally, use `CultureInfo.InvariantCulture`, `DateTimeFormatInfo.InvariantInfo`, or `NumberFormatInfo.InvariantInfo`
    - exception: if the overload that takes the `IFormatProvider` is deprecated or obsolete (<bdi lang="la" title="for example [lat]">e.g.</bdi>, `Enum.ToString()`), don’t pass one

## SQL

- never EVER concatenate/<wbr/>format/<wbr/>etc. user input into a SQL string and execute it. Always pass user input to the database as a parameter. This also includes any values that ever were user input, not just the first time.
- when referencing database objects, always specify the schema, never assume all users in the system will have `dbo` as their default schema, this can also cause plan cache bloat as different users can get different query plans
- when using indexed views, always use query hint `WITH (NOEXPAND)` on the view, as without that hint, lower editions of SQL Server will ignore the indices and do a table scan
- avoid dynamic SQL when possible, but if it’s unavoidable always invoke it with `EXEC sys.sp_executesql @sql;` never with `EXEC(@sql);`
    - when passing dynamic SQL parameters from ADO.NET, always specify the size of variable-length data types, never let `AddWithValue` infer it, because it will always guess the exact length of the value passed, and different parameter lengths will get different query plans, bloating the plan cache
- never reference another database directly by name, use a synonym so that if the other database name changes, there’s less risk of missing a reference
- always use `datetime2`, `date`, or `time`; never use `datetime`, `smalldatetime`, or `datetimeoffset`
    - always store time values in UTC, as `time` or `datetime2`, only use `datetimeoffset` in the UI tier
        - <dfn>exception</dfn>: if a time is in the future and “local”, keep it local as the UTC offset could change before the event occurs (in this scenario, the time zone identifier also needs to be stored… ***not** the offset*)

## Web Services

- always specify a meaningful URI as a namespace, never leave the default `tempuri.com`
- always include a version as the last piece of the URI, if there isn’t a meaningful version number, use the 4-digit year, <bdi lang="la" title="in other words [lat]">i.e.</bdi>
    - <http://domain.com/product/item/1.0>
    - <http://domain.com/product/item/2014>

## Web

- never sniff browser user agent strings, test specific functionality using a library such as modernizr instead; also check [CanIUse.com](http://caniuse.com) to know if the new feature is widely supported or not
- always allow graceful degradation… it may not be as pretty in older browsers, but it should function without breaking, using CSS tools such as [ColorZilla’s gradient generator](http://colorzilla.com/gradient-editor) or the [border radius generator](http://border-radius.com)
- if IE6 support is an issue, prefer something like [an IE6 specific style sheet](https://code.google.com/p/universal-ie6-css)

## CSS

- avoid setting display to `none` or an empty string, instead add and remove a class defined in your style sheet, <bdi lang="la" title="in other words [lat]">i.e.</bdi> `.hidden { display: none !important; visibility: hidden; }` (bootstrap already defines this), to avoid issues around different behavior for block v. inline elements, etc.

## JavaScript

- always use semicolons, never rely on Automatic Semicolon Insertion, which may not always insert semicolons where you think
- for equality comparisons, always use `===` and `!==`, never use `==` and `!=`, the former compare type and value, the later type casts one of the objects to the type of the other, often changing the value (or at least the trueness/<wbr/>falseness of the value) in the process
- never use the global property `undefined` to get the only value of type `undefined`, use the `void` operator instead (<bdi lang="la" title="in other words [lat]">i.e.</bdi> `void null` or `void 0`). ECMA versions 3 & 4 allow the global property to be redefined, although this redefining is disallowed by ECMA version 5, many implementations still allow it.
- always put code inside an immediately-invoked function expression (IIFE) to avoid creating or using global variables, <bdi lang="la" title="in other words [lat]">i.e.</bdi>

``` js
(function moduleIIFE() {
    "use strict";
    var window, ___, module;
    window = this; // in a browser, should be window
    ___ = window.___ = window.___ || {};
    module = ___.module = ___.module || {};
    // code goes here, including $(window.document).ready or window.pageLoad
}).call(this);
```

- note: replace module with the name of the module, usually this will match the name of the file (without the .js, of course)
- see [Ben Alman’s blog post on IIFEs](http://benalman.com/news/2010/11/immediately-invoked-function-expression) for more.
- namespace code as shown with the ___ namespace in the IIFE example above
- when declaring ASP.NET `pageLoad` and `pageUnload`, always check if it’s already defined to ensure the original functionality isn’t removed, <bdi lang="la" title="in other words [lat]">i.e.</bdi>

``` js
// this example assumes the code is inside an IIFE as shown above
module.pageLoad = function (sender, args) {
    // code goes here
}

if (window.pageLoad) {
    module.pageLoadExisting = window.pageLoad;
    window.pageLoad = function (sender, args) {
        module.pageLoadExisting(sender, args);
        module.pageLoad(sender, args);
    };
} else {
    window.pageLoad = module.pageLoad;
}
```

where module is the name of whatever module is defined in that script

- OR convert the event to a jQuery event, and the problem goes away:
    - do this once in every page…

``` js
window.pageLoad = function (sender, args) {
    /// <summary>Wrap AjaxControlToolkit pageLoad in a jQuery event, so multiple handlers can be added.</summary>
    /// <param name="sender" type="Sys._Application">Application object the event is acting upon.</param>
    /// <param name="args" type="Sys.ApplicationLoadEventArgs">Application load event arguments.</param>
    /// <returns type="undefined" />
    $(window.document).trigger("ajaxToolkit-pageLoad", sender, args);
};
```

- then implement it like this…

``` js
$(window.document).on("ajaxToolkit-pageLoad", function (event, sender, args) {
    /// <summary>AjaxControlToolkit pageLoad.</summary>
    /// <param name="event" type="jQuery.Event">jQuery event object.</param>
    /// <param name="sender" type="Sys._Application">Application object the event is acting upon.</param>
    /// <param name="args" type="Sys.ApplicationLoadEventArgs">Application load event arguments.</param>
    /// <returns type="undefined" />

    // code goes here
});
```
