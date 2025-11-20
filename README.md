# ASP.NET Core MVC - Tag Helpers Complete Guide

## Table of Contents
1. [Introduction](#introduction)
2. [Core Concepts](#core-concepts)
3. [Built-in Tag Helpers](#built-in-tag-helpers)
4. [CRUD Implementation](#crud-implementation)
5. [Validation](#validation)
6. [Custom Tag Helpers](#custom-tag-helpers)
7. [Best Practices](#best-practices)

---

## Introduction

### What are Tag Helpers?

Tag helpers are C# classes that extend HTML elements with server-side capabilities during the rendering process. They provide a more natural, HTML-friendly way to generate dynamic content compared to traditional HTML helpers.

### Why Use Tag Helpers?

**HTML-Friendly Syntax**
- Look like standard HTML elements
- Easier to read and write than HTML helpers
- Better for designers and front-end developers

**Strong Typing**
- Compile-time type safety
- Full IntelliSense support
- Catches errors early in development

**Maintainability**
- Easier to understand and maintain
- Natural integration with HTML
- Consistent with modern web development practices

### Tag Helpers vs HTML Helpers

| Aspect | Tag Helpers | HTML Helpers |
|--------|-------------|--------------|
| **Syntax** | `<input asp-for="Name" />` | `@Html.TextBoxFor(m => m.Name)` |
| **Readability** | HTML-like, intuitive | C# method calls |
| **IntelliSense** | Full support | Limited |
| **Mixing with HTML** | Seamless | Requires context switching |
| **Learning Curve** | Easier for web developers | Easier for C# developers |

**Recommendation**: Use Tag Helpers for all new ASP.NET Core projects.

---

## Core Concepts

### Enabling Tag Helpers

Add to `_ViewImports.cshtml`:

```csharp
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

### Common Attributes Reference

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `asp-for` | Bind to model property | `<input asp-for="Email" />` |
| `asp-action` | Target action method | `<a asp-action="Index">` |
| `asp-controller` | Target controller | `<a asp-controller="Home">` |
| `asp-route-{param}` | Pass route parameter | `<a asp-route-id="@item.Id">` |
| `asp-items` | Populate dropdown | `<select asp-items="@Model.Countries">` |
| `asp-validation-for` | Field validation message | `<span asp-validation-for="Email">` |
| `asp-validation-summary` | Validation summary | `<div asp-validation-summary="All">` |
| `asp-append-version` | Cache busting | `<link asp-append-version="true" />` |

---

## Built-in Tag Helpers

### 1. Anchor Tag Helper (`<a>`)

Generates links with automatic routing.

**Basic Link**
```html
<a asp-controller="Home" asp-action="Index">Home</a>
```

**With Route Parameter**
```html
<a asp-action="Details" asp-route-id="@product.Id">View Details</a>
```

**Multiple Parameters**
```html
<a asp-action="Search" 
   asp-route-category="@category" 
   asp-route-page="1">Search</a>
```

**With Fragment (Anchor)**
```html
<a asp-action="Index" asp-fragment="section2">Jump to Section 2</a>
```

---

### 2. Form Tag Helper (`<form>`)

Creates forms with automatic anti-forgery token handling.

**Basic Form**
```html
<form asp-controller="Products" asp-action="Create" method="post">
    <!-- form fields -->
</form>
```

**Edit Form with Route Parameter**
```html
<form asp-action="Edit" asp-route-id="@Model.Id" method="post">
    <!-- form fields -->
</form>
```

**Search Form (GET)**
```html
<form asp-action="Index" method="get">
    <input type="text" name="searchTerm" />
    <button type="submit">Search</button>
</form>
```

---

### 3. Input Tag Helper (`<input>`)

Binds input fields to model properties with automatic type detection.

**Text Input**
```html
<input asp-for="ProductName" class="form-control" />
```

**Email (Auto-detected)**
```html
<input asp-for="Email" class="form-control" />
<!-- Generates: <input type="email" ... /> -->
```

**Date Input**
```html
<input asp-for="DateOfBirth" type="date" class="form-control" />
```

**Password**
```html
<input asp-for="Password" type="password" class="form-control" />
```

**Checkbox**
```html
<input asp-for="IsActive" type="checkbox" />
```

**Hidden Field**
```html
<input asp-for="Id" type="hidden" />
```

---

### 4. Select Tag Helper (`<select>`)

Creates dropdown lists bound to model properties.

**Basic Dropdown**
```html
<select asp-for="CategoryId" asp-items="Model.Categories" class="form-control">
    <option value="">-- Select Category --</option>
</select>
```

**With ViewBag**
```html
<select asp-for="CountryId" asp-items="@ViewBag.Countries" class="form-control">
    <option value="">-- Select Country --</option>
</select>
```

**Multiple Selection**
```html
<select asp-for="SelectedCategories" 
        asp-items="@Model.Categories" 
        multiple 
        class="form-control">
</select>
```

---

### 5. Label Tag Helper (`<label>`)

Generates labels with proper `for` attribute.

```html
<label asp-for="ProductName"></label>
<!-- Output: <label for="ProductName">Product Name</label> -->
```

---

### 6. Textarea Tag Helper (`<textarea>`)

Creates multi-line text input.

```html
<textarea asp-for="Address" class="form-control" rows="3"></textarea>
```

---

### 7. Validation Tag Helpers

**Validation Message (Field-specific)**
```html
<span asp-validation-for="ProductName" class="text-danger"></span>
```

**Validation Summary**
```html
<div asp-validation-summary="All" class="text-danger"></div>
```

**Options for `asp-validation-summary`:**
- `All` - Display all validation errors
- `ModelOnly` - Display only model-level errors
- `None` - Don't display validation summary

---

### 8. Cache Tag Helper (`<cache>`)

Improves performance by caching content.

**Time-based Expiration**
```html
<cache expires-after="@TimeSpan.FromMinutes(10)">
    @await Component.InvokeAsync("PopularProducts")
</cache>
```

**Sliding Expiration**
```html
<cache expires-sliding="@TimeSpan.FromMinutes(5)">
    @await Component.InvokeAsync("RecentOrders")
</cache>
```

**Absolute Expiration**
```html
<cache expires-on="@DateTime.Now.AddHours(1)">
    @await Component.InvokeAsync("DailyDeals")
</cache>
```

**Vary by User**
```html
<cache vary-by-user="true" expires-after="@TimeSpan.FromMinutes(10)">
    Welcome, @User.Identity.Name
</cache>
```

**Vary by Query String**
```html
<cache vary-by-query="category,page" expires-after="@TimeSpan.FromMinutes(5)">
    <!-- Content varies by query parameters -->
</cache>
```

---

### 9. Environment Tag Helper (`<environment>`)

Conditionally renders content based on environment.

```html
<environment include="Development">
    <link rel="stylesheet" href="~/css/site.css" />
</environment>

<environment exclude="Development">
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

---

### 10. Partial Tag Helper (`<partial>`)

Renders partial views.

**Basic Usage**
```html
<partial name="_ProductCard" model="@product" />
```

**With ViewData**
```html
<partial name="_Navigation" view-data="@ViewData" />
```

**Optional Partial**
```html
<partial name="_OptionalHeader" optional="true" />
```

---

### 11. Image Tag Helper (`<img>`)

Adds cache-busting version strings.

```html
<img src="~/images/logo.png" asp-append-version="true" alt="Logo" />
```

---

## CRUD Implementation

### Index Action (Read)

**Controller**
```csharp
[HttpGet]
[Route("persons")]
public IActionResult Index(
    string searchBy = "PersonName",
    string? searchString = null,
    string sortBy = "PersonName",
    string sortOrder = "ASC")
{
    // Configure search fields
    ViewBag.SearchFields = new Dictionary<string, string>()
    {
        { nameof(PersonResponse.PersonName), "Person Name" },
        { nameof(PersonResponse.Email), "Email" },
        { nameof(PersonResponse.DateOfBirth), "Date of Birth" },
        { nameof(PersonResponse.Gender), "Gender" }
    };
    
    // Pass current values to view
    ViewBag.CurrentSearchBy = searchBy;
    ViewBag.CurrentSearchString = searchString;
    ViewBag.CurrentSortBy = sortBy;
    ViewBag.CurrentSortOrder = sortOrder;
    
    // Get filtered and sorted data
    List<PersonResponse> persons = _personsService.GetFilteredPersons(searchBy, searchString);
    persons = _personsService.GetSortedPersons(persons, sortBy, sortOrder);
    
    return View(persons);
}
```

**View (Index.cshtml)**
```html
@model IEnumerable<PersonResponse>

<h1>Persons</h1>

<!-- Search Form -->
<form asp-action="Index" method="get">
    <div class="row">
        <div class="col-md-3">
            <select name="searchBy" class="form-control">
                @foreach (var field in ViewBag.SearchFields)
                {
                    <option value="@field.Key" selected="@(ViewBag.CurrentSearchBy == field.Key)">
                        @field.Value
                    </option>
                }
            </select>
        </div>
        <div class="col-md-6">
            <input type="text" 
                   name="searchString" 
                   class="form-control" 
                   placeholder="Search..." 
                   value="@ViewBag.CurrentSearchString" />
        </div>
        <div class="col-md-3">
            <button type="submit" class="btn btn-primary">Search</button>
            <a asp-action="Index" class="btn btn-secondary">Clear</a>
        </div>
    </div>
</form>

<!-- Create Button -->
<a asp-action="Create" class="btn btn-success mt-3">Create New</a>

<!-- Data Table -->
<table class="table table-striped mt-3">
    <thead>
        <tr>
            <th>Name</th>
            <th>Email</th>
            <th>Date of Birth</th>
            <th>Gender</th>
            <th>Country</th>
            <th>Actions</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var person in Model)
        {
            <tr>
                <td>@person.PersonName</td>
                <td>@person.Email</td>
                <td>@person.DateOfBirth?.ToString("MM/dd/yyyy")</td>
                <td>@person.Gender</td>
                <td>@person.Country</td>
                <td>
                    <a asp-action="Edit" 
                       asp-route-personID="@person.PersonID" 
                       class="btn btn-sm btn-primary">Edit</a>
                    <a asp-action="Delete" 
                       asp-route-personID="@person.PersonID" 
                       class="btn btn-sm btn-danger">Delete</a>
                </td>
            </tr>
        }
    </tbody>
</table>
```

---

### Create Action (Create)

**Controller**
```csharp
[HttpGet]
[Route("persons/create")]
public IActionResult Create()
{
    ViewBag.Countries = _countriesService.GetAllCountries();
    return View();
}

[HttpPost]
[Route("persons/create")]
[ValidateAntiForgeryToken]
public IActionResult Create(PersonAddRequest personRequest)
{
    if (!ModelState.IsValid)
    {
        ViewBag.Countries = _countriesService.GetAllCountries();
        ViewBag.Errors = ModelState.Values
            .SelectMany(v => v.Errors)
            .Select(e => e.ErrorMessage)
            .ToList();
        return View(personRequest);
    }
    
    PersonResponse personResponse = _personsService.AddPerson(personRequest);
    return RedirectToAction("Index");
}
```

**View (Create.cshtml)**
```html
@model PersonAddRequest

<h1>Create Person</h1>

<form asp-action="Create" method="post">
    <div asp-validation-summary="All" class="text-danger"></div>
    
    <div class="form-group">
        <label asp-for="PersonName"></label>
        <input asp-for="PersonName" class="form-control" />
        <span asp-validation-for="PersonName" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="Email"></label>
        <input asp-for="Email" class="form-control" />
        <span asp-validation-for="Email" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="DateOfBirth"></label>
        <input asp-for="DateOfBirth" type="date" class="form-control" />
        <span asp-validation-for="DateOfBirth" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="Gender"></label>
        <select asp-for="Gender" class="form-control">
            <option value="">-- Select Gender --</option>
            <option value="Male">Male</option>
            <option value="Female">Female</option>
            <option value="Other">Other</option>
        </select>
        <span asp-validation-for="Gender" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="CountryID"></label>
        <select asp-for="CountryID" asp-items="@ViewBag.Countries" class="form-control">
            <option value="">-- Select Country --</option>
        </select>
        <span asp-validation-for="CountryID" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="Address"></label>
        <textarea asp-for="Address" class="form-control" rows="3"></textarea>
        <span asp-validation-for="Address" class="text-danger"></span>
    </div>
    
    <div class="form-group form-check">
        <input asp-for="ReceiveNewsLetters" type="checkbox" class="form-check-input" />
        <label asp-for="ReceiveNewsLetters" class="form-check-label"></label>
    </div>
    
    <button type="submit" class="btn btn-primary">Create</button>
    <a asp-action="Index" class="btn btn-secondary">Cancel</a>
</form>

@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

---

### Edit Action (Update)

**Controller**
```csharp
[HttpGet]
[Route("persons/edit/{personID}")]
public IActionResult Edit(Guid personID)
{
    PersonResponse? personResponse = _personsService.GetPersonByPersonID(personID);
    
    if (personResponse == null)
    {
        return NotFound();
    }
    
    PersonUpdateRequest personUpdateRequest = personResponse.ToPersonUpdateRequest();
    ViewBag.Countries = _countriesService.GetAllCountries();
    
    return View(personUpdateRequest);
}

[HttpPost]
[Route("persons/edit/{personID}")]
[ValidateAntiForgeryToken]
public IActionResult Edit(PersonUpdateRequest personRequest)
{
    if (!ModelState.IsValid)
    {
        ViewBag.Countries = _countriesService.GetAllCountries();
        ViewBag.Errors = ModelState.Values
            .SelectMany(v => v.Errors)
            .Select(e => e.ErrorMessage)
            .ToList();
        return View(personRequest);
    }
    
    PersonResponse updatedPerson = _personsService.UpdatePerson(personRequest);
    return RedirectToAction("Index");
}
```

**View (Edit.cshtml)**
```html
@model PersonUpdateRequest

<h1>Edit Person</h1>

<form asp-action="Edit" asp-route-personID="@Model.PersonID" method="post">
    <input asp-for="PersonID" type="hidden" />
    
    <div asp-validation-summary="All" class="text-danger"></div>
    
    <!-- Same form fields as Create.cshtml -->
    <div class="form-group">
        <label asp-for="PersonName"></label>
        <input asp-for="PersonName" class="form-control" />
        <span asp-validation-for="PersonName" class="text-danger"></span>
    </div>
    
    <!-- ... other fields ... -->
    
    <button type="submit" class="btn btn-primary">Update</button>
    <a asp-action="Index" class="btn btn-secondary">Cancel</a>
</form>

@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

---

### Delete Action (Delete)

**Controller**
```csharp
[HttpGet]
[Route("persons/delete/{personID}")]
public IActionResult Delete(Guid personID)
{
    PersonResponse? personResponse = _personsService.GetPersonByPersonID(personID);
    
    if (personResponse == null)
    {
        return NotFound();
    }
    
    return View(personResponse);
}

[HttpPost]
[Route("persons/delete/{personID}")]
[ValidateAntiForgeryToken]
public IActionResult Delete(PersonUpdateRequest personRequest)
{
    PersonResponse? personResponse = _personsService.GetPersonByPersonID(personRequest.PersonID);
    
    if (personResponse == null)
    {
        return NotFound();
    }
    
    _personsService.DeletePerson(personRequest.PersonID);
    return RedirectToAction("Index");
}
```

**View (Delete.cshtml)**
```html
@model PersonResponse

<h1>Delete Person</h1>

<div class="alert alert-warning">
    <h3>Are you sure you want to delete this person?</h3>
</div>

<div class="card">
    <div class="card-body">
        <dl class="row">
            <dt class="col-sm-3">Name:</dt>
            <dd class="col-sm-9">@Model.PersonName</dd>
            
            <dt class="col-sm-3">Email:</dt>
            <dd class="col-sm-9">@Model.Email</dd>
            
            <dt class="col-sm-3">Date of Birth:</dt>
            <dd class="col-sm-9">@Model.DateOfBirth?.ToString("MM/dd/yyyy")</dd>
            
            <dt class="col-sm-3">Gender:</dt>
            <dd class="col-sm-9">@Model.Gender</dd>
            
            <dt class="col-sm-3">Country:</dt>
            <dd class="col-sm-9">@Model.Country</dd>
            
            <dt class="col-sm-3">Address:</dt>
            <dd class="col-sm-9">@Model.Address</dd>
        </dl>
    </div>
</div>

<form asp-action="Delete" asp-route-personID="@Model.PersonID" method="post" class="mt-3">
    <button type="submit" class="btn btn-danger">Confirm Delete</button>
    <a asp-action="Index" class="btn btn-secondary">Cancel</a>
</form>
```

---

## Validation

### Client-Side Validation

Enable instant feedback without server roundtrips.

**Required Scripts**
```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

**Or manually include:**
```html
@section Scripts {
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/jquery-validation/dist/jquery.validate.min.js"></script>
    <script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.min.js"></script>
}
```

### Validation Flow

**Client-Side (Optional but Recommended)**
1. User fills form and submits
2. JavaScript validation checks run
3. If errors exist, they're displayed immediately
4. Submission is prevented until valid

**Server-Side (Always Required)**
1. Request reaches server
2. Model binding creates object from form data
3. Data annotations are validated
4. Errors added to `ModelState`
5. Controller checks `ModelState.IsValid`
6. If invalid, view is returned with errors
7. If valid, operation proceeds

### Post-Redirect-Get Pattern

After successful POST, redirect to prevent re-submission:

```csharp
[HttpPost]
public IActionResult Create(PersonAddRequest request)
{
    if (!ModelState.IsValid)
    {
        return View(request); // Return view with errors
    }
    
    _service.AddPerson(request);
    return RedirectToAction("Index"); // Redirect after success
}
```

---

## Custom Tag Helpers

### Creating a Custom Tag Helper

**Example: Email Tag Helper**

```csharp
using Microsoft.AspNetCore.Razor.TagHelpers;

[HtmlTargetElement("email")]
public class EmailTagHelper : TagHelper
{
    public string Address { get; set; }
    
    public override void Process(TagHelperContext context, TagHelperOutput output)
    {
        output.TagName = "a";
        output.Attributes.SetAttribute("href", $"mailto:{Address}");
        output.Content.SetContent(Address);
    }
}
```

**Usage**
```html
<email address="support@example.com"></email>
```

**Output**
```html
<a href="mailto:support@example.com">support@example.com</a>
```

### Registering Custom Tag Helpers

In `_ViewImports.cshtml`:

```csharp
@addTagHelper *, YourAssemblyName
```

---

## Best Practices

### ✅ Do's

**Use Tag Helpers Consistently**
- Adopt tag helpers throughout your application
- Don't mix tag helpers and HTML helpers unnecessarily

**Leverage Strong Typing**
- Always use `asp-for` for model binding
- Take advantage of IntelliSense and compile-time checking

**Validate on Both Sides**
- Client-side for user experience
- Server-side for security (always required)

**Secure POST Actions**
- Always use `[ValidateAntiForgeryToken]`
- Protect against CSRF attacks

**Keep Views Simple**
- Views should focus on presentation
- Move complex logic to controllers or services

**Use Partial Views**
- Create reusable components
- Keep views DRY (Don't Repeat Yourself)

**Handle Errors Gracefully**
- Display validation errors clearly
- Provide helpful feedback to users

**Follow Naming Conventions**
- Use consistent property naming
- Match model properties with form fields

### ❌ Don'ts

**Don't Overuse Tag Helpers**
- Not every HTML element needs a tag helper
- Use them where they add value

**Don't Mix Approaches**
- Stick with tag helpers or HTML helpers, not both
- Maintain consistency within a view

**Don't Skip Server Validation**
- Client-side validation can be bypassed
- Always validate on the server

**Don't Expose Sensitive Data**
- Be careful with hidden fields
- Don't trust client-side data

**Don't Nest Too Deeply**
- Keep HTML structure readable
- Extract complex sections to partials

**Don't Forget Cache Busting**
- Use `asp-append-version` for static files
- Prevent stale resource loading

---

## Performance Considerations

### Caching Strategies

**Use Cache Tag Helper**
```html
<cache expires-after="@TimeSpan.FromMinutes(10)">
    @* Expensive operation *@
</cache>
```

**Minimize Tag Helper Overhead**
- Tag helpers execute on every request
- Use caching for expensive operations
- Profile rendering performance

**Async Operations**
- Use `async/await` where appropriate
- Don't block on I/O operations

---

## Security Best Practices

### Input Validation
- Validate all user input
- Use data annotations
- Implement custom validation when needed

### Output Encoding
- Tag helpers automatically encode output
- Prevents XSS vulnerabilities
- Don't bypass encoding unless necessary

### Anti-Forgery Tokens
```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public IActionResult Create(PersonAddRequest request)
{
    // Action implementation
}
```

### Authorization
- Implement proper authentication
- Use `[Authorize]` attribute
- Check permissions before allowing operations

---

## Quick Reference Cheat Sheet

### Common Tag Helper Patterns

**Link to Action**
```html
<a asp-action="Edit" asp-route-id="@item.Id">Edit</a>
```

**Form Submission**
```html
<form asp-action="Create" method="post">
    <input asp-for="Name" />
    <span asp-validation-for="Name"></span>
    <button type="submit">Submit</button>
</form>
```

**Dropdown List**
```html
<select asp-for="CategoryId" asp-items="@ViewBag.Categories">
    <option value="">-- Select --</option>
</select>
```

**Validation Summary**
```html
<div asp-validation-summary="All" class="text-danger"></div>
```

**Partial View**
```html
<partial name="_ViewName" model="@Model" />
```

**Environment-Specific Content**
```html
<environment include="Development">
    <!-- Development only -->
</environment>
```

---

## Additional Resources

### Official Documentation
- [ASP.NET Core Tag Helpers](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/intro)
- [Built-in Tag Helpers](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/built-in/)
- [Authoring Tag Helpers](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/authoring)

### Next Steps
1. Practice implementing CRUD operations
2. Create custom tag helpers for your needs
3. Explore advanced validation scenarios
4. Learn about partial views and view components
5. Study caching strategies
