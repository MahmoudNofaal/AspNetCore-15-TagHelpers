# ASP.NET Core MVC - Tag Helpers

## Tag Helpers

Tag helpers are a feature in ASP.NET Core MVC that allow you to extend HTML elements with server-side capabilities. They are C# classes that modify the behavior and output of HTML elements during the rendering process.

---

## Benefits of Tag Helpers

### HTML-Friendly Syntax

Tag helpers look like standard HTML elements, making them easier to read and write than traditional HTML helpers.

### Strong Typing

Tag helpers offer compile-time type safety and IntelliSense support, catching errors early in development.

### Code Reuse

They can be easily reused across different views and projects.

### Reduced Server Roundtrips

Tag helpers execute on the server, allowing you to perform complex logic and data binding before the page is sent to the client.

### Extensibility

You can create your own custom tag helpers to meet specific needs.

---

## When to Use Tag Helpers

- **Form Handling**: Create forms and bind them to your models easily
- **Links and URLs**: Generate links with correct routing information
- **Caching**: Control how your views are cached
- **Conditional Rendering**: Show or hide content based on conditions
- **Custom Elements**: Build reusable custom UI components

---

## Best Practices

### Namespace Management

Keep tag helper namespaces organized:

```csharp
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

### Readability

Keep tag helper attributes concise and self-explanatory.

### Performance

Be mindful of the number of tag helpers used in a view, as they can impact rendering performance.

### Testing

Write unit tests for your custom tag helpers to ensure their correct behavior.

---

## Things to Avoid

- **Overusing Tag Helpers**: Use them for appropriate tasks, not for every HTML element
- **Excessive Nesting**: Avoid deeply nested tag helpers, as it can make your code difficult to read
- **Mixing Tag Helpers and HTML Helpers**: Try to use either tag helpers or HTML helpers consistently within a view to maintain a cleaner code structure

---

## Important Tag Helpers with Examples

### Anchor Tag Helper (&lt;a&gt;)

```html
<a asp-controller="Home" asp-action="Index">Home</a>
```

- Generates a link to the `Index` action method in the `HomeController`
- Automatically handles routing and URL generation

**Additional Examples**:

```html
<!-- With route parameters -->
<a asp-controller="Products" asp-action="Details" asp-route-id="@product.Id">View Details</a>

<!-- With multiple route parameters -->
<a asp-action="Search" asp-route-category="@category" asp-route-page="1">Search</a>

<!-- With fragment (anchor) -->
<a asp-action="Index" asp-fragment="section2">Jump to Section 2</a>
```

### Form Tag Helper (&lt;form&gt;)

```html
<form asp-controller="Products" asp-action="Create" method="post">
    <!-- form fields -->
</form>
```

- Creates a form that submits data to the `Create` action in the `ProductsController`
- Handles anti-forgery tokens automatically for better security

**Additional Examples**:

```html
<!-- Form with route parameter -->
<form asp-action="Edit" asp-route-id="@Model.Id" method="post">
    <!-- form fields -->
</form>

<!-- Form for GET request (search) -->
<form asp-action="Index" method="get">
    <input type="text" name="searchTerm" />
    <button type="submit">Search</button>
</form>
```

### Input Tag Helper (&lt;input&gt;)

```html
<input asp-for="ProductName" class="form-control" />
```

- Binds the input field to the `ProductName` property of your model
- Automatically sets the input type (e.g., text, email, password) based on the property type

**Additional Examples**:

```html
<!-- Email input (automatically detected) -->
<input asp-for="Email" class="form-control" />

<!-- Date input -->
<input asp-for="DateOfBirth" type="date" class="form-control" />

<!-- Checkbox -->
<input asp-for="IsActive" type="checkbox" />

<!-- Hidden field -->
<input asp-for="Id" type="hidden" />

<!-- Password input -->
<input asp-for="Password" type="password" class="form-control" />
```

### Select Tag Helper (&lt;select&gt;)

```html
<select asp-for="CategoryId" asp-items="Model.Categories"></select>
```

- Creates a dropdown list bound to the `CategoryId` property
- `asp-items` takes a collection of items to populate the dropdown

**Additional Examples**:

```html
<!-- With ViewBag -->
<select asp-for="CountryId" asp-items="@ViewBag.Countries" class="form-control">
    <option value="">-- Select Country --</option>
</select>

<!-- Multiple selection -->
<select asp-for="SelectedCategories" asp-items="@Model.Categories" multiple class="form-control"></select>

<!-- Using SelectListGroup -->
<select asp-for="ProductId" asp-items="@Model.GroupedProducts"></select>
```

### Label Tag Helper (&lt;label&gt;)

```html
<label asp-for="ProductName"></label>
```

- Generates a label for the `ProductName` input field
- Automatically sets the `for` attribute to match the input's ID

**Example Output**:

```html
<label for="ProductName">Product Name</label>
```

### Validation Tag Helpers

#### Validation Summary

```html
<div asp-validation-summary="All" class="text-danger"></div>
```

**Options**:
- `All`: Display all validation errors
- `ModelOnly`: Display only model-level errors
- `None`: Don't display validation summary

#### Validation Message

```html
<span asp-validation-for="ProductName" class="text-danger"></span>
```

Displays validation messages for a specific property.

### Cache Tag Helper (&lt;cache&gt;)

```html
<cache expires-after="@TimeSpan.FromMinutes(10)">
    <!-- Content to cache -->
</cache>
```

- Caches the enclosed content for the specified duration
- Improves performance for content that doesn't change frequently

**Additional Examples**:

```html
<!-- Cache with sliding expiration -->
<cache expires-sliding="@TimeSpan.FromMinutes(5)">
    @await Component.InvokeAsync("PopularProducts")
</cache>

<!-- Cache with absolute expiration -->
<cache expires-on="@DateTime.Now.AddHours(1)">
    @await Component.InvokeAsync("DailyDeals")
</cache>

<!-- Cache with vary-by parameters -->
<cache vary-by-user="true" expires-after="@TimeSpan.FromMinutes(10)">
    Welcome, @User.Identity.Name
</cache>

<cache vary-by-query="category,page" expires-after="@TimeSpan.FromMinutes(5)">
    <!-- Content varies by query string parameters -->
</cache>
```

### Environment Tag Helper (&lt;environment&gt;)

```html
<environment include="Development">
    <link rel="stylesheet" href="~/css/site.css" />
</environment>

<environment exclude="Development">
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

- Conditionally renders content based on the environment
- `asp-append-version` automatically adds a version query string to the URL in non-development environments for cache busting

### Partial Tag Helper (&lt;partial&gt;)

```html
<partial name="_ProductCard" model="@product" />
```

Renders a partial view with optional model.

**Additional Examples**:

```html
<!-- With view data -->
<partial name="_Navigation" view-data="@ViewData" />

<!-- Optional partial (doesn't error if not found) -->
<partial name="_OptionalHeader" optional="true" />
```

### Image Tag Helper (&lt;img&gt;)

```html
<img src="~/images/logo.png" asp-append-version="true" alt="Logo" />
```

Automatically appends a version query string for cache busting.

---

## Controllers

### Index (Read)

**HTTP Verb**: GET

**Purpose**: Displays a list or table of entities (e.g., persons)

**Logic**:
- Retrieves data from the `PersonsService` using methods like `GetFilteredPersons` and `GetSortedPersons`
- Populates `ViewBag` with:
  - `SearchFields`: A dictionary of searchable fields and their display names
  - `CurrentSearchBy`: The currently selected search field
  - `CurrentSearchString`: The current search term
  - `CurrentSortBy`: The current sorting field
  - `CurrentSortOrder`: The current sort order (ASC or DESC)
- Returns the Index view with the filtered and sorted data

**Example**:

```csharp
[HttpGet]
[Route("persons")]
public IActionResult Index(
    string searchBy = "PersonName",
    string? searchString = null,
    string sortBy = "PersonName",
    string sortOrder = "ASC")
{
    ViewBag.SearchFields = new Dictionary<string, string>()
    {
        { nameof(PersonResponse.PersonName), "Person Name" },
        { nameof(PersonResponse.Email), "Email" },
        { nameof(PersonResponse.DateOfBirth), "Date of Birth" },
        { nameof(PersonResponse.Gender), "Gender" }
    };
    
    ViewBag.CurrentSearchBy = searchBy;
    ViewBag.CurrentSearchString = searchString;
    ViewBag.CurrentSortBy = sortBy;
    ViewBag.CurrentSortOrder = sortOrder;
    
    List<PersonResponse> persons = _personsService.GetFilteredPersons(searchBy, searchString);
    persons = _personsService.GetSortedPersons(persons, sortBy, sortOrder);
    
    return View(persons);
}
```

### Create (Create)

**HTTP Verbs**: GET (Display form), POST (Process submission)

**Purpose**: Creates a new entity

**Logic**:

**GET**:
- Retrieves a list of countries from `CountriesService` for populating the "Country" dropdown in the form
- Returns the Create view

**POST**:
- Receives `PersonAddRequest` via model binding
- Validates the model state
- If valid, calls `_personsService.AddPerson` to create the new person and redirects to the Index action
- If invalid, repopulates `ViewBag.Countries` and `ViewBag.Errors` and returns the Create view with error messages

**Example**:

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

### Edit (Update)

**HTTP Verbs**: GET (Display form), POST (Process submission)

**Purpose**: Updates an existing entity

**Logic**:

**GET**:
- Retrieves the person to edit using `_personsService.GetPersonByPersonID`
- Retrieves a list of countries from `CountriesService` for the dropdown
- Returns the Edit view with the person's data in a `PersonUpdateRequest`

**POST**:
- Receives `PersonUpdateRequest` via model binding
- Validates the model state
- If valid, calls `_personsService.UpdatePerson` and redirects to the Index action
- If invalid, repopulates `ViewBag.Countries` and `ViewBag.Errors` and returns the Edit view with error messages

**Example**:

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

### Delete (Delete)

**HTTP Verbs**: GET (Display confirmation), POST (Perform deletion)

**Purpose**: Deletes an existing entity

**Logic**:

**GET**:
- Retrieves the person to delete using `_personsService.GetPersonByPersonID`
- Returns the Delete view to confirm the deletion

**POST**:
- Receives `PersonUpdateRequest` (containing the PersonID) via model binding
- Calls `_personsService.DeletePerson` and redirects to the Index action

**Example**:

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

---

## Views

### Index.cshtml (Read)

Displays a table of persons with search and sort functionality.

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
            <input type="text" name="searchString" class="form-control" 
                   placeholder="Search..." value="@ViewBag.CurrentSearchString" />
        </div>
        <div class="col-md-3">
            <button type="submit" class="btn btn-primary">Search</button>
            <a asp-action="Index" class="btn btn-secondary">Clear</a>
        </div>
    </div>
</form>

<!-- Create Button -->
<a asp-action="Create" class="btn btn-success">Create New</a>

<!-- Data Table -->
<table class="table table-striped">
    <thead>
        <tr>
            <th>
                <partial name="_GridColumnHeader" 
                         model="@(new { ColumnName = "PersonName", DisplayName = "Name" })" />
            </th>
            <th>
                <partial name="_GridColumnHeader" 
                         model="@(new { ColumnName = "Email", DisplayName = "Email" })" />
            </th>
            <th>Actions</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var person in Model)
        {
            <tr>
                <td>@person.PersonName</td>
                <td>@person.Email</td>
                <td>
                    <a asp-action="Edit" asp-route-personID="@person.PersonID" 
                       class="btn btn-sm btn-primary">Edit</a>
                    <a asp-action="Delete" asp-route-personID="@person.PersonID" 
                       class="btn btn-sm btn-danger">Delete</a>
                </td>
            </tr>
        }
    </tbody>
</table>
```

### Create.cshtml

Renders a form for creating a new person.

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
    
    <div class="form-group">
        <input asp-for="ReceiveNewsLetters" type="checkbox" />
        <label asp-for="ReceiveNewsLetters"></label>
    </div>
    
    <button type="submit" class="btn btn-primary">Create</button>
    <a asp-action="Index" class="btn btn-secondary">Cancel</a>
</form>

@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

### Edit.cshtml

Similar to Create.cshtml but for editing an existing person.

```html
@model PersonUpdateRequest

<h1>Edit Person</h1>

<form asp-action="Edit" asp-route-personID="@Model.PersonID" method="post">
    <input asp-for="PersonID" type="hidden" />
    
    <div asp-validation-summary="All" class="text-danger"></div>
    
    <!-- Same form fields as Create.cshtml -->
    
    <button type="submit" class="btn btn-primary">Update</button>
    <a asp-action="Index" class="btn btn-secondary">Cancel</a>
</form>

@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

### Delete.cshtml

Displays a confirmation message and a form to confirm the deletion.

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

<form asp-action="Delete" asp-route-personID="@Model.PersonID" method="post">
    <button type="submit" class="btn btn-danger">Delete</button>
    <a asp-action="Index" class="btn btn-secondary">Cancel</a>
</form>
```

---

## Client-Side Validations

Client-side validations are enabled in these views through the inclusion of jQuery, jQuery Validate, and jQuery Unobtrusive Validation libraries in the `@section scripts` block.

### Benefits

**Instant Feedback**: Validation messages appear immediately when the user interacts with the form fields.

**Reduced Server Load**: Validations are performed on the client-side, reducing the number of round trips to the server.

### Required Scripts

```html
@section Scripts {
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/jquery-validation/dist/jquery.validate.min.js"></script>
    <script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.min.js"></script>
}
```

Or use the validation scripts partial:

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

---

## HttpPost Action Method Submission Process

### 1. Form Submission

The user fills out the form and clicks the submit button.

### 2. Client-Side Validation (Optional)

If enabled, JavaScript validation checks are performed before the form is submitted to the server. If there are errors, they are displayed immediately, and the submission is prevented.

### 3. Request Sent to Server

If there are no client-side errors, the form data is sent to the server via a POST request.

### 4. Model Binding

ASP.NET Core's model binding system extracts the form data and attempts to create a model object (`PersonAddRequest` or `PersonUpdateRequest`) based on the form field names.

### 5. Model Validation

Data annotations and custom validation rules are applied to the model object. If errors are found, they are added to the `ModelState` object.

### 6. Controller Action Logic

**If ModelState.IsValid is true**:
- The action performs the appropriate CRUD operation (create, update, delete) using the service layer

**If ModelState.IsValid is false**:
- The action typically returns the view again, repopulating the form with the user's input and displaying error messages

### 7. Redirect (Optional)

After a successful POST request, the action often redirects to another page (e.g., the "Index" view) to prevent accidental re-submissions (Post-Redirect-Get pattern).

---

## Key Points to Remember

### Tag Helpers

**Purpose**: Server-side code that modifies HTML elements to include server-side logic

**Benefits**:
- HTML-friendly syntax
- Strong typing and IntelliSense
- Code reuse
- Reduced server round-trips

**Common Tag Helpers**:
- `a`: Creates links (e.g., `asp-controller`, `asp-action`)
- `form`: Generates HTML forms (e.g., `asp-controller`, `asp-action`, `method`)
- `input`, `textarea`, `select`: Bind to model properties (e.g., `asp-for`)
- `label`: Creates labels for form fields (`asp-for`)
- `cache`: Caches a portion of the view
- `partial`: Renders a partial view
- `environment`: Conditionally renders content based on the environment

---

## CRUD Operations with Tag Helpers

### Index (Read)

#### Anchor (a): Create links to Create, Edit, and Delete actions

```html
<a asp-action="Create">Create</a>
<a asp-action="Edit" asp-route-id="@item.Id">Edit</a> 
<a asp-action="Delete" asp-route-id="@item.Id">Delete</a>
```

#### Form (form): Create a form for filtering or searching

```html
<form asp-action="Index" method="get">
    <input type="text" name="searchString" />
    <button type="submit">Search</button>
</form>
```

### Create & Edit

#### Form: Create the form for submitting data

```html
<form asp-action="Create" method="post"> 
    <!-- form fields -->
</form>
```

#### Input, Textarea, Select: Bind to model properties

```html
<input asp-for="Name" />
<textarea asp-for="Description"></textarea>
<select asp-for="CategoryId" asp-items="ViewBag.Categories"></select>
```

#### Label: Generate labels for input fields

```html
<label asp-for="Name"></label>
```

#### Span (Validation): Display validation messages

```html
<span asp-validation-for="Name"></span>
```

#### Div (Validation Summary): Summarize validation errors

```html
<div asp-validation-summary="All"></div>
```

### Delete

#### Form: Create a form that submits a delete request

```html
<form asp-action="Delete" asp-route-id="@Model.Id" method="post">
    <button type="submit">Delete</button>
</form>
```

---

## Comparison Table: Tag Helpers vs HTML Helpers

| Aspect | Tag Helpers | HTML Helpers |
|--------|-------------|--------------|
| **Syntax** | HTML-like | C# method calls |
| **Readability** | More readable | Less readable |
| **IntelliSense** | Full support | Limited support |
| **Type Safety** | Compile-time | Compile-time |
| **Example** | `<input asp-for="Name" />` | `@Html.TextBoxFor(m => m.Name)` |
| **Mixing with HTML** | Easy | Harder |
| **Learning Curve** | Easier for HTML devs | Easier for C# devs |

**Recommendation**: Use Tag Helpers for new projects as they provide better readability and are more HTML-friendly.

---

## Custom Tag Helpers

You can create your own custom tag helpers to encapsulate reusable HTML generation logic.

### Example: Email Tag Helper

```csharp
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

**Usage**:

```html
<email address="support@example.com"></email>
```

**Output**:

```html
<a href="mailto:support@example.com">support@example.com</a>
```

---

## Interview Focus Areas

### Understanding

Explain the benefits of tag helpers over traditional HTML helpers:
- More HTML-friendly syntax
- Better IntelliSense support
- Easier to read and maintain
- Natural integration with HTML

### Usage

Demonstrate how to use common tag helpers in CRUD scenarios:
- Form creation and submission
- Data binding with `asp-for`
- Generating links with routing
- Validation display

### Model Binding and Validation

Show how to use tag helpers to bind to models and display validation errors:
- Using `asp-for` for two-way binding
- Displaying validation messages with `asp-validation-for`
- Showing validation summary with `asp-validation-summary`

### Best Practices

Discuss how to write clean, maintainable, and reusable code with tag helpers:
- Keep views simple and focused on presentation
- Use strongly typed models
- Leverage partial views for reusability
- Consistent use of tag helpers throughout the application

### Performance Considerations

Explain how tag helpers can impact performance and how to mitigate potential issues:
- Use caching tag helper for expensive operations
- Avoid excessive nesting
- Profile and optimize rendering performance
- Use async operations where appropriate

### Security

Emphasize the importance of input validation and output encoding to prevent XSS vulnerabilities:
- Always use `[ValidateAntiForgeryToken]` for POST actions
- Validate all user input
- Use tag helpers which automatically encode output
- Implement proper authorization and authentication

---

## Additional Resources

### Common Tag Helper Attributes

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `asp-for` | Bind to model property | `<input asp-for="Name" />` |
| `asp-action` | Specify action method | `<a asp-action="Index">` |
| `asp-controller` | Specify controller | `<a asp-controller="Home">` |
| `asp-route-*` | Add route parameters | `<a asp-route-id="5">` |
| `asp-items` | Populate select options | `<select asp-items="@Model.List">` |
| `asp-validation-for` | Show validation message | `<span asp-validation-for="Name">` |
| `asp-validation-summary` | Show validation summary | `<div asp-validation-summary="All">` |
| `asp-append-version` | Add version for cache busting | `<link asp-append-version="true" />` |

### Best Practices Checklist

- ✅ Use tag helpers consistently throughout your application
- ✅ Prefer strongly typed models over ViewBag/ViewData
- ✅ Always validate user input on both client and server side
- ✅ Use `[ValidateAntiForgeryToken]` for all POST actions
- ✅ Implement proper error handling and user feedback
- ✅ Keep views focused on presentation logic only
- ✅ Use partial views for reusable components
- ✅ Test your views and tag helpers
- ✅ Follow consistent naming conventions
- ✅ Document custom tag helpers