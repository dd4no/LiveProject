# **Tech Academy Live Project**

## _Introduction_
The final two weeks of my course of study at The Tech Academy involved participating in a project for a real organization with a team of peers.

The project, an interactive website for managing the content and productions for a theater company, was built using .NET MVC and Entity Framework with a Code-first approach.

The project was managed using Agile/Scrum methodologies and organized as a two-week sprint with an initial planning meeting, daily stand-ups, a sprint review, and ended with a code retrospective.  Project management was accomplished through Azure DevOps and utilized boards and user stories.

Most of my work revolved around creating the area to manage the theatre's list of productions.


<hr>

## _My Assignments_

Front End Stories
-----------------

* [Style Registration Form](#1-style-registration-form)
* [Style Production Create and Edit Pages](#3-style-create-and-edit-pages)

Back End Stories
----------------
* [Create Productions Model and CRUD](#2-create-productions-model-and-crud)
* [Add Search and Pagination to Productions Index](#5-add-search-and-pagination-to-productions-index)

Full Stack Stories
------------------
* [Redesign Productions Index Page](#4-redesign-productions-index-page)
* [Create Production Details Modal](#6-create-production-details-modal)

<hr><hr>

### 1. Style Registration Form
My first assignment was to add basic styling to a form for new users to register an account.  It mostly involved utilizing CSS, and was styled by adding Bootstrap classes to the existing Razor page and creating a few custom classes to control the behavior.

Before:
![Before](/images/beforeReg.png)
After:
![After](/images/afterReg.png)

```css
/* REGISTER PAGE */


/* Style form with smaller size, centered element, discerning background, shape, and shadow */
.Register-form {
    width: 50%;
    margin: 0 auto;
    padding-top: 10px;
    background-color: rgb(50 50 50);
    border-radius: 10px;
    box-shadow: 1px 1px 3px var(--secondary-color), -1px -1px 0px var(--light-color);
}

/* Center align headings and format color and spacing */
.Register-heading2 {
    color: var(--secondary-color);
    text-align: center;
    margin-bottom: 25px;
    padding-top: 50px;
}

/* Center align headings and format color and spacing */
.Register-heading4 {
    color: var(--secondary-color);
    text-align: center;
    padding-top: 5px;
}

/* Allow elements to fill entire form and be centered */
.Register-div {
    padding: 10px;
    max-width: 80%;
    margin: 0 auto;
    text-align: center;
}

/* Change background color of input fields */
.Register-input {
    background-color: var(--light-color);
}

/* Hover effects for input fields */
.Register-input:hover {
    box-shadow: 0px 0px 10px var(--secondary-color--dark);
}

/* Style submit button */
.Register-button {
    background-color: var(--secondary-color);
    font-weight: bold;
    margin-bottom: 10px;
}

/* Button hover effect */
.Register-button:hover {
    box-shadow: 0px 0px 5px var(--light-color);
}
```
Back to: [Assignments](#my-assignments)

### 2. Create Productions Model and CRUD
Next, I was tasked with creating a class for the productions and creating the database table for it through Entity Framework.  It was relatively straightforward and involved creating the class and associated properties to match the database schema, utilizing a context file to create the table, and creating a new controller with scaffolding for CRUD pages to manage the table.

Production Class:
```c#
using System;
using System.ComponentModel.DataAnnotations;

namespace TheatreCMS3.Areas.Prod.Models
{
    public class Production
    {
        public int ProductionID { get; set; }
        public string Title { get; set; }
        public string Description { get; set; }
        public string Playwright { get; set; }
        public int Runtime { get; set; }
        [DataType(DataType.Date)]
        [DisplayFormat(DataFormatString ="{0:yyyy-MM-dd}", ApplyFormatInEditMode =true)] 
        public DateTime OpeningDay { get; set; }
        [DataType(DataType.Date)]
        [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
        public DateTime ClosingDay { get; set; }
        [DataType(DataType.Time)]
        [DisplayFormat(DataFormatString ="{0:HH:mm}", ApplyFormatInEditMode =true)]
        public DateTime ShowTimeEve { get; set; }
        [DataType(DataType.Time)]
        [DisplayFormat(DataFormatString = "{0:HH:mm}", ApplyFormatInEditMode = true)]
        public DateTime ShowTimeMat { get; set; }
        public int Season { get; set; }
        public bool IsWorldPremier { get; set; }
        public string TicketLink { get; set; }

        // Properties not yet created.

        //public virtual List<CastMember> CastMembers { get; set; }
        //public virtual ProductionPhoto DefaultPhoto { get; set; }
        //public virtual List<ProductionPhoto> ProductionPhotos { get; set; }
        //public virtual List<CalendarEvent> CalendarEvents { get; set; }

        //public static bool IsCurrentlyShowing()
        //{
        //    return;
        //}
    }
}
```

Productions Controller:
```c#
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Entity;
using System.Linq;
using System.Net;
using System.Web;
using System.Web.Mvc;
using TheatreCMS3.Areas.Prod.Models;
using TheatreCMS3.Models;

namespace TheatreCMS3.Areas.Prod.Controllers
{
    public class ProductionsController : Controller
    {
        private ApplicationDbContext db = new ApplicationDbContext();

        // GET: Prod/Productions
        public ActionResult Index()
        {
            return View(db.Productions.ToList());
        }

        // GET: Prod/Productions/Details/5
        public ActionResult Details(int? id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            Production production = db.Productions.Find(id);
            if (production == null)
            {
                return HttpNotFound();
            }
            return View(production);
        }

        // GET: Prod/Productions/Create
        public ActionResult Create()
        {
            return View();
        }

        // POST: Prod/Productions/Create
        // To protect from overposting attacks, enable the specific properties you want to bind to, for 
        // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ProductionID,Title,Description,Playwright,Runtime,OpeningDay,ClosingDay,ShowTimeEve,ShowTimeMat,Season,IsWorldPremier,TicketLink")] Production production)
        {
            if (ModelState.IsValid)
            {
                db.Productions.Add(production);
                db.SaveChanges();
                return RedirectToAction("Index");
            }

            return View(production);
        }

        // GET: Prod/Productions/Edit/5
        public ActionResult Edit(int? id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            Production production = db.Productions.Find(id);
            if (production == null)
            {
                return HttpNotFound();
            }
            return View(production);
        }

        // POST: Prod/Productions/Edit/5
        // To protect from overposting attacks, enable the specific properties you want to bind to, for 
        // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "ProductionID,Title,Description,Playwright,Runtime,OpeningDay,ClosingDay,ShowTimeEve,ShowTimeMat,Season,IsWorldPremier,TicketLink")] Production production)
        {
            if (ModelState.IsValid)
            {
                db.Entry(production).State = EntityState.Modified;
                db.SaveChanges();
                return RedirectToAction("Index");
            }
            return View(production);
        }

        // GET: Prod/Productions/Delete/5
        public ActionResult Delete(int? id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            Production production = db.Productions.Find(id);
            if (production == null)
            {
                return HttpNotFound();
            }
            return View(production);
        }

        // POST: Prod/Productions/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Production production = db.Productions.Find(id);
            db.Productions.Remove(production);
            db.SaveChanges();
            return RedirectToAction("Index");
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
}
```

Db Context:
```c#
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext()
            : base("DefaultConnection", throwIfV1Schema: false)
        {
        }

        public static ApplicationDbContext Create()
        {
            return new ApplicationDbContext();
        }

        public System.Data.Entity.DbSet<Areas.Blog.Models.BlogPost> BlogPosts { get; set; }
        public System.Data.Entity.DbSet<TheatreCMS3.Areas.Rental.Models.TheaterRental> TheaterRentals { get; set; }
        public System.Data.Entity.DbSet<TheatreCMS3.Areas.Rental.Models.RentalRequest> RentalRequest { get; set; }
        public System.Data.Entity.DbSet<TheatreCMS3.Areas.Blog.Models.Comment> Comments { get; set; }

        // Create Productions table
        public DbSet<TheatreCMS3.Areas.Prod.Models.Production> Productions { get; set; }
    }
```

![Scaffolding](/images/scaffolding.png)

Back to: [Assignments](#my-assignments)

### 3. Style Create and Edit Pages
Following creation of basic CRUD pages, I was assigned to add styling to the Create and Edit pages.  Once again, this was accomplished through adding both Bootstrap and custom CSS classes.  Elements that were changed included the heading, buttons, text input fields and adding a container to hold the form.  I also included a media query into the code to make the form an appropriate width for smaller screens.

![Create-Form](/images/createTop.png)
![Create-Form](/images/createBottom.png)

Back to: [Assignments](#my-assignments)

### 4. Redesign Productions Index Page
The default production index was to be changed from a simple database list into a card layout with each production having it's own card that included the title and a production photo.  Each card featured an image overlay which, upon mouse hover, provided buttons for Edit, Delete, and Details.

![Before](/images/beforeIndex)
![After](/images/afterIndex)

```html

@model IEnumerable<TheatreCMS3.Areas.Prod.Models.Production>

@{
    ViewBag.Title = "Index";
}


<!-- Title -->
<h2 class="Production--header text-left ml-2">Productions</h2>

<!-- Create Button -->
<div class="text-left ml-2">
    @Html.ActionLink("Create New", "Create", null, new { @class="btn Production--button mb-3"})
</div>

<!-- Card box -->
<div class="container p-1 mb-5">

    <!-- Flex-wrap row with responsive number of columns -->
    <div class="row row-cols-1 row-cols-sm-2 row-cols-md-4 row-cols-lg-5 no-gutters mb-5">

        <!-- Iterate Productions table and create a card for each row -->
	    @foreach (var item in Model)
	    {

            <!-- Card -->
			<div>
		    <div class="card w-auto  mx-1 mb-2 p-0 cms-bg-secondary">

				<div class="Production--cardImage">
					<!-- Production Image -->				
					@*<img src="@Html.DisplayFor(modelItem => item.DefaultPhoto) alt="@Html.DisplayFor(modelItem => item.Title)" />*@
					<img src="@Url.Content("~/Content/images/macbeth.png")" class="card-img-top" alt="Default Photo" />

					<!-- Hover overlay with Details, Edit, and Delete link buttons -->
					<div class="card-img-overlay text-center Production--cardOverlay">
						<p>
							@Html.ActionLink("Details", "Details", new { id = item.ProductionID }, new { @class = "badge badge-pill badge-warning" })
							@Html.ActionLink("Edit", "Edit", new { id = item.ProductionID }, new { @class = "badge badge-pill badge-dark" })
							@Html.ActionLink("Delete", "Delete", new { id = item.ProductionID }, new { @class = "badge badge-pill badge-danger" })
						</p>           
					</div>
				</div>

                <!-- Production Title -->
			    <div class="card-title my-0 p-1 Production--cardTitle overflow-auto">
				    <h5 class="card-title cms-text-dark text-center my-0 ">@Html.DisplayFor(modelItem => item.Title)</h5>
			    </div>

		    </div>
			</div>
	    }

    </div>
</div>
```

```css
/* PRODUCTION AREA STYLING */


/* CREATE AND EDIT PRODCUTIONS PAGES*/

/* Outer form container */
.Production--formBox {
    padding: 4px;
    margin: 0 auto;
    margin-bottom: 50px;
    border-radius: 10px;
    width: 60%;
    background-color: var(--main-color--light);
}

/* Create wider fields on small screens */
@media screen and (max-width: 990px) {
    .Production--formBox {
        width: 100%;
    }
}

/* Inner form container */
.Production--form {
    padding: 10px;
    border-radius: 10px;
    background-color: var(--light-color);
}

/* Headings */
.Production--header {
    padding-top: 50px;
    margin-bottom: 25px;
    color: var(--secondary-color);
    text-align: center;
}

/* Input fields  */
.Production--input-div {
    max-width: 100%;
}

.Production--input {
    background-color: var(--secondary-color);
}

.Production--input:focus {
    border: 2px solid var(--main-color);
}

/* Buttons */
.Production--button {
    background-color: var(--secondary-color);
    font-weight: bold;
}

.Production--button-list {
    margin-left: 5px;
    background-color: var(--main-color--light);
    font-weight: bold;
}

.Production--button:hover {
    opacity: 0.9;
}

/* END CREATE AND EDIT PRODUCTION PAGES */
```

Back to: [Assignments](#my-assignments)

### 5. Add Search and Pagination to Productions Index
The next task was to add a search bar to the index to be able to search the database by production title, and to add pagination to control the number of results returned and provide the user navigation when dealing with large results.  This task involved adding a package to the project to utilize paging utilities and a change to the controller to alter the logic used.

```c#
using PagedList;

namespace TheatreCMS3.Areas.Prod.Controllers
{
    public class ProductionsController : Controller
    {
        private ApplicationDbContext db = new ApplicationDbContext();

        // GET: Prod/Productions
        public ViewResult Index(string sortOrder, string currentFilter, string searchString, int? page)
        {
            if (page <= 0)
            {
                page = 1;
            }

            // Retain sort order during page changes.
            ViewBag.CurrentSort = sortOrder;

            // If search string is changed set "page" to 1.
            if (searchString != null)
            {
                page = 1;
            }
            // Otherwise retain search string during page changes.
            else
            {
                searchString = currentFilter;
            }
            // Provide view with current filter string.
            ViewBag.CurrentFilter = searchString;

            // Query database for table rows.
            var productions = from p in db.Productions select p;

            // If search string is not empty get search results.
            if (!String.IsNullOrEmpty(searchString))
            {
                productions = productions.Where(p => p.Title.Contains(searchString));
            }

            // Sort results ascending by title
            productions = productions.OrderBy(p => p.Title);

            // Set page size.
            int pageSize = 10;            
            
            // Retain value of "page" or set to 1 if "page" is null.
            int pageNumber = (page ?? 1);
            

            // Return list to view with page number and page size.
            return View(productions.ToPagedList(pageNumber, pageSize));
        }
```

```html
@model PagedList.IPagedList<TheatreCMS3.Areas.Prod.Models.Production>
@using PagedList.Mvc;
<link href="~/Content/PagedList.css" rel="stylesheet" type="text/css" />

@{
	ViewBag.Title = "Productions";
}

<!-- Title -->
<h2 class="Production--header text-left ml-2">Productions</h2>

<!-- Create Button -->
<div class="text-left ml-2">
	@Html.ActionLink("Create New", "Create", null, new { @class = "btn Production--button mb-3" })
</div>

<!-- Search Bar -->
@using (Html.BeginForm("Index", "Productions", FormMethod.Get))
{
	<p class="cms-text-secondary ml-2">
		Find Title: <br />
		@Html.TextBox("SearchString")
		<input type="submit" value="Search" />
	</p>
}

<!-- Card box -->
<div class="container p-1">

	<!-- Flex-wrap row with responsive number of columns -->
	<div class="row row-cols-1 row-cols-sm-2 row-cols-md-4 row-cols-lg-5 no-gutters mb-1">

		<!-- Iterate Productions table and create a card for each row -->
		@foreach (var item in Model)
		{
			<!-- Card -->
			<div>
				<div class="card w-auto  mx-1 mb-2 p-0 cms-bg-secondary">

					<div class="Production--cardImage">

						<!-- Production Image -->
						@*<img src="@Html.DisplayFor(modelItem => item.DefaultPhoto) alt="@Html.DisplayFor(modelItem => item.DefaultPhoto)" />*@
						<img src="@Url.Content("~/Content/images/macbeth.png")" class="card-img-top" alt="Default Photo" />

						<!-- Hover overlay with Details, Edit, and Delete link buttons -->
						<div class="card-img-overlay text-center Production--cardOverlay">
							<p>
								@Html.ActionLink("Details", "Details", new { id = item.ProductionID }, new { @class = "badge badge-pill badge-warning" })
								@Html.ActionLink("Edit", "Edit", new { id = item.ProductionID }, new { @class = "badge badge-pill badge-dark" })
								@Html.ActionLink("Delete", "Delete", new { id = item.ProductionID }, new { @class = "badge badge-pill badge-danger" })
							</p>
						</div>
					</div>

					<!-- Production Title -->
					<div class="card-title my-0 p-1 Production--cardTitle overflow-auto">
						<h5 class="card-title cms-text-dark text-center my-0 ">@Html.DisplayFor(modelItem => item.Title)</h5>
					</div>

				</div>
			</div>
		}

	</div>
</div>

<!-- Page Navigation -->
<p class="ml-2 my-1 p-0 cms-text-secondary">
	Page @(Model.PageCount < Model.PageNumber ? 0 : Model.PageNumber) of @Model.PageCount
</p>
<p>
	@Html.PagedListPager(Model, page => Url.Action("Index", new { page, currentFilter = ViewBag.CurrentFilter }))
</p>
```

```css
/* PRODUCTIONS INDEX PAGE */

.Production--cardImage {
	position: relative;
}

.Production--cardOverlay {
	opacity: 0;
	transition: .5s ease;
}

.Production--cardOverlay:hover {
	opacity: 1;
}

.Production--cardTitle {
	height: 40px;
}

/* END PRODUCTIONS INDEX PAGE */
```

![After](/images/afterSearch.png)

Back to: [Assignments](#my-assignments)

### 6. Create Production Details Modal
My final story's mission was to create a pop-up modal for the Details of each production to be displayed upon clicking each production card.  This task was accomplished by creating a Bootrap modal component, and rendering a Partial View within it which was called by using Javascript/JQuery and AJAX.

Index.cshtml:
```html
<!-- Details Modal -->
<div class="modal" id="Details" tabindex="-1">

	<div class="modal-dialog modal-dialog-centered modal-dialog-scrollable">

		<div class="modal-content">

			<div id="ModalContent"></div>

		</div>

	</div>

</div>
```

Productions Controller:
```c#
// Render Partial View to Details Modal.
        [HttpPost]
        public ActionResult DetailsModal(string id)
        {
            // Convert query string to integer for record search.
            int record = Convert.ToInt32(id);

            // Find matching database record.
            Production production = db.Productions.Find(record);

            // Return error if record not located.
            if (production == null)
            {
                return HttpNotFound();
            }

            // Return partial view with matching record.
            return PartialView("_DetailsModal", production);
        }
```
Partial View:
```html
@model TheatreCMS3.Areas.Prod.Models.Production


<div class="p-1">
	<h4 class="">@Html.DisplayFor(model => model.Title)</h4>

	<hr />

	<table class="table-dark m-auto w-100">

		<tr>
			<td>@Html.DisplayNameFor(model => model.Description)</td>
			<td>@Html.DisplayFor(model => model.Description)</td>
		</tr>

		<tr>
			<td>@Html.DisplayNameFor(model => model.Playwright)</td>
			<td>@Html.DisplayFor(model => model.Playwright)</td>
		</tr>

		<tr>
			<td>@Html.DisplayNameFor(model => model.Runtime)</td>
			<td>@Html.DisplayFor(model => model.Runtime)</td>
		</tr>

		<tr>
			<td>@Html.DisplayNameFor(model => model.OpeningDay)</td>
			<td>@Html.DisplayFor(model => model.OpeningDay)</td>
		</tr>

		<tr>
			<td>@Html.DisplayNameFor(model => model.ClosingDay)</td>
			<td>@Html.DisplayFor(model => model.ClosingDay)</td>
		</tr>

		<tr>
			<td>@Html.DisplayNameFor(model => model.ShowTimeEve)</td>
			<td>@Html.DisplayFor(model => model.ShowTimeEve)</td>
		</tr>

		<tr>
			<td>@Html.DisplayNameFor(model => model.ShowTimeMat)</td>
			<td>@Html.DisplayFor(model => model.ShowTimeMat)</td>
		</tr>

		<tr>
			<td>@Html.DisplayNameFor(model => model.Season)</td>
			<td>@Html.DisplayFor(model => model.Season)</td>
		</tr>

		<tr>
			<td>@Html.DisplayNameFor(model => model.IsWorldPremier)</td>
			<td>@Html.DisplayFor(model => model.IsWorldPremier)</td>
		</tr>

		<tr>
			<td>@Html.DisplayNameFor(model => model.TicketLink)</td>
			<td>@Html.DisplayFor(model => model.TicketLink)</td>
		</tr>

	</table>

	<div class="modal-footer">
		<button type="button" class="btn cms-bg-dark cms-text-light" data-dismiss="modal">Close</button>
	</div>

</div>
```

JavaScript:
```js
// Retreive database record from production table and open modal
$(function () {
    $('.ModalButton').click(function () {
        var button = $(this);
        var id = button.attr('data-id');
        $.ajax({
            type: "POST",
            url: '/Prod/Productions/DetailsModal',
            data: {id : id},
            success: function (data) {
                $('#ModalContent').html(data);
                $('#Details').modal('toggle');
            }
        });    
    });
});
```

![Final-Index](/images/finalIndex.png)

Back to: [Assignments](#my-assignments)

<hr><hr>

## _Learning Hightlights_
* I Learned how to work on a devolopment team and how to focus on one assigned task at time while maintaining communication with the team.
* I learned how to work with Azure DevOps board and stories.
* I gained experience with version control branching, merge conflict resolution, and reverting to previous points in my work.
* I learned to make more frequent commits, how to make better comments on my commits, and how to review the history.
* I gained confidence with C#, Razor, Bootstrap and .Net.
* I had a great time!

Back to: [Top](#tech-academy-live-project)



