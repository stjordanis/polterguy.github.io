# The scaffolded Angular code

Having come this far, it's time to explain the structure of the scaffolded Angular _output_
Magic produces. When you create an Angular frontend using Magic, really what Magic does, is
to create a fairly well structured Angular project for you - Which most
seasoned Angular developers would probably instantly feel acquainted with, and be able to
easily modify according to their needs. In the video below, I am showing you how
the structure of the output roughly looks like, if you prefer to follow video tutorials.
Notice, in both the video and this article, I am going through the _"angular-default"_
template. Magic contains several templates, some of which doesn't create grids, but
only HTTP service layers for you - But the different templates should produce content
that are similar to the structure we explain here.

<div style="position:relative; padding-bottom:56.25%; padding-top:30px; height:0; overflow:hidden;margin-top:4rem;margin-bottom:4rem;">
<iframe width="560" height="315" style="position:absolute; top:0; left:0; width:100%; height:100%;" src="https://www.youtube.com/embed/c4E-5fgpl5Y" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## The main structure of the Angular output

The Angular project Magic produces have 5 main folders.

* `base` - Common base classes, from which most of the scaffolded modules will inherit from
* `components` - Contains one folder for each CRUD endpoint group, wrapping your table with CRUD GUI components
* `helpers` - Helper components, you can use as you wish, as you extend the Angular result Magic gives you
* `pipes` - Pipes allowing you to perform transformation inside of your HTML files
* `services` - Services your app relies upon

Of the above folders, probably the most interesting folder will be the _"components"_ folder, which
will contain one grid component, and one edit/create component, for every single database table
you have in your system - Or to be accurate; All database tables you selected as you generated your
Angular project - Which may vary, according to what components you wish to have
your Angular project wrap, and what template you choose as you generate your frontend.

Magic will produce roughly 12.500 lines of code for a database with 16 tables. Don't let this
number scare you, since a lot of the code will be common boiler plate code. For a database table
with 4 main columns, Magic will produce roughly the following code ...

* 248 lines of TypeScript code
* 237 lines of HTML
* 15 lines of SCSS

Notice, the numbers might vary according to the structure of your database, and as I add features
and modifications to the scaffold template - But in general, the above figures should be fairly
accurate. This implies that if you have 100 database tables, with an average of 4 columns per table,
Magic will produce the following code for you automatically.

 * 24.800 lines of TypeScript code
 * 23.700 lines of HTML
 * 1.500 lines of SCSS

Which becomes almost 50KLOC in total. The average software developer can produce between 325
and 750 lines of code per month, implying what Magic gave you in 2-3 seconds, equals the productivity
of one software developer for 60-120 months, all other factors set aside. Of course, this comparison
might be a bit unfair, since Magic cannot intelligently add business logic to your code, and/or
modify it exactly according to your needs - But there is no doubt that Magic will soar
the average software developer's productivity by somewhere between 10x-100x, at the very minimum -
Especially in the beginning of a project. Not so much later maybe, but starting out the project with arguably
100% perfectly wired code, standardized on a single development model, and a well thought out
architecture, easily recognizable for other developers - Have _huge_ benefits also in the long run.

> Software Development is all about standards. Once you standardize your development model and architecture, all developers can easily maintain any parts of your platform, and you're free to move resources around as you see fit

The above point is difficult to emphasize enough, since once you've taught yourself how _one_ Magic
project looks like, you've arguably taught yourself how _all_ Magic projects looks like - And you will
be rapidly capable of maintaining new projects, without _any initial learning curve_ what so ever.

### Grid components and modal dialogues

All your main grid components, have one edit/create modal dialogue associated with them, depending
upon which template you're using of course. The same modal dialogue is used for both editing and
creating new entities, since the difference is almost always just the endpoint to use when invoking
the backend, and whether or not the model contains a primary key (update) or not (create).
Below you can find the TypeScript code that Magic will generate on your actor table, if you run
it on the Sakila database.

```typescript
/*
 * This file was automatically generated by Magic. Read more at https://polterguy.github.io
 */

import { Component, OnInit, ViewChild } from '@angular/core';
import { MatSnackBar } from '@angular/material/snack-bar';
import { GridComponent } from '../../base/grid.component';
import { MatPaginator } from '@angular/material';
import { MatDialog } from '@angular/material';
import { FormControl } from '@angular/forms';

import { EditActorComponent } from './modals/edit.actor.component';
import { HttpService } from 'src/app/services/http-service';
import { AuthService } from 'src/app/services/auth-service';

/**
 * "Datagrid" component for displaying instance of Actor
 * entities from your HTTP REST backend.
 */
@Component({
  selector: 'app-actor',
  templateUrl: './actor.component.html',
  styleUrls: ['./actor.component.scss']
})
export class ActorComponent extends GridComponent implements OnInit {

  /**
   * Which columns we should display. Reorder to prioritize columns differently.
   * Notice! 'delete-instance' should always come last.
   */
  public displayedColumns: string[] = [
    'first_name',
    'last_name',
    'last_update',
    'delete-instance'
  ];

  // Need to view paginator as a child to update page index of it.
  @ViewChild(MatPaginator, {static: true}) paginator: MatPaginator;

  // Form control declarations to bind up with reactive form elements.
  public actor_id: FormControl;
  public first_name: FormControl;
  public last_name: FormControl;
  public last_update: FormControl;

  // Constructor taking a bunch of services/helpers through dependency injection.
  constructor(
    protected authService: AuthService,
    protected snackBar: MatSnackBar,
    private httpService: HttpService,
    private dialog: MatDialog) {
      super(authService, snackBar);
  }

  /**
   * Overridde abstract method necessary to return the URL endpoint
   * for CRUD methods to base class.
   */
  protected url() {
    return 'magic/modules/sakila/actor';
  }

  /**
   * Overridden abstract method from base class, that returns the Observable
   * necessary to actually retrieve items from backend.
   */
  protected read(filter: any) {
    return this.httpService.actor.read(filter);
  }

  /**
   * Overridden abstract method from base class, that returns the Observable
   * necessary to actually retrieve count of items from backend.
   */
  protected count(filter: any) {
    return this.httpService.actor.count(filter);
  }

  /**
   * Overridden abstract method from base class, that returns the Observable
   * necessary to actually delete items in backend.
   */
  protected delete(ids: any) {
    return this.httpService.actor.delete(ids);
  }

  /**
   * Implementation of abstract base class method, to reset paginator
   * of component.
   */
  protected resetPaginator() {
    this.paginator.pageIndex = 0;
    this.filter.offset = 0;
  }

  /**
   * OnInit implementation. Retrieves initial data (unfiltered),
   * and instantiates our FormControls.
   */
  public ngOnInit() {

    // Retrieves data from our backend, unfiltered, and binds our mat-table accordingly.
    this.getData();

    /*
     * Creating our filtering FormControl instances, which gives us "live filtering"
     * on our columns.
     */
    this.actor_id = this.createFormControl('actor_id.eq');
    this.first_name = this.createFormControl('first_name.like');
    this.last_name = this.createFormControl('last_name.like');
    this.last_update = this.createFormControl('last_update.eq');
  }

  /**
   * Invoked when user wants to edit an entity
   * 
   * This will show a modal dialog, allowing the user to edit his record.
   */
  public editEntity(entity: any) {

    const dialogRef = this.dialog.open(EditActorComponent, {
      data: this.getEditData(entity)
    });
    dialogRef.afterClosed().subscribe(res => {
      if (res) {
        this.setEditData(res, entity);
      }
    });
  }

  /**
   * Invoked when user wants to create a new entity
   * 
   * This will show a modal dialog, allowing the user to supply
   * the initial data for the entity.
   */
  public createEntity() {

    const dialogRef = this.dialog.open(EditActorComponent, {
      data: {
        isEdit: false,
        entity: {},
      }});
    dialogRef.afterClosed().subscribe((res: any) => {
      if (res) {
        this.itemCreated(res);
      }
    });
  }
}
```

Notice how fairly short it is, considering all the things it does - Which is only possible due to
intelligently offloading most of the workload it does, to the `GridComponent` base class. However,
one of its nifty little details, is the way it wires up the `distinctUntilChanged()` parts above,
through its base class, which will automatically apply filtering conditions, if the user types
something into any of the filter textboxes, for then to stop typing for 400 milliseconds. This
provides you with _"live filtering"_ as you are typing into the filter input fields on the form.

The HTML files again, is using Material tables to implement paging, sorting, etc. You can see the
equivalent HTML file below, wrapping the actor table from Sakila.

```html

<div class="component-wrapper">

  <div class="mat-elevation-z4 relativized grid-wrapper">

    <h3>Actor</h3>

    <button
      mat-button
      *ngIf="authService.me.canInvoke('magic/modules/sakila/actor', 'post')"
      class="new-button"
      (click)="createEntity()">
      <mat-icon>add_circle</mat-icon>
    </button>

    <table
      mat-table
      multiTemplateDataRows
      [dataSource]="data">

      <ng-container matColumnDef="actor_id">
        <th mat-header-cell *matHeaderCellDef>
          <mat-form-field appearance="outline" class="fill-width filter-textbox">
            <mat-label>actor_id</mat-label>
            <input
              matInput
              placeholder="Filter ..."
              type="number"
              autocomplete="off"
              [formControl]="actor_id">
            <mat-icon matSuffix>search</mat-icon>
          </mat-form-field>
          <button
            class="sort-button"
            (click)="sort('actor_id')"
            mat-button>
            <mat-icon matSuffix>{{getSortIcon('actor_id')}}</mat-icon>
          </button>
        </th>
        <td mat-cell (click)="toggleDetails(el)" *matCellDef="let el">{{el.actor_id}}</td>
      </ng-container>

      <ng-container matColumnDef="first_name">
        <th mat-header-cell *matHeaderCellDef>
          <mat-form-field appearance="outline" class="fill-width filter-textbox">
            <mat-label>first_name</mat-label>
            <input
              matInput
              placeholder="Filter ..."
              type="text"
              autocomplete="off"
              [formControl]="first_name">
            <mat-icon matSuffix>search</mat-icon>
          </mat-form-field>
          <button
            class="sort-button"
            (click)="sort('first_name')"
            mat-button>
            <mat-icon matSuffix>{{getSortIcon('first_name')}}</mat-icon>
          </button>
        </th>
        <td mat-cell (click)="toggleDetails(el)" *matCellDef="let el">{{el.first_name}}</td>
      </ng-container>

      <ng-container matColumnDef="last_name">
        <th mat-header-cell *matHeaderCellDef>
          <mat-form-field appearance="outline" class="fill-width filter-textbox">
            <mat-label>last_name</mat-label>
            <input
              matInput
              placeholder="Filter ..."
              type="text"
              autocomplete="off"
              [formControl]="last_name">
            <mat-icon matSuffix>search</mat-icon>
          </mat-form-field>
          <button
            class="sort-button"
            (click)="sort('last_name')"
            mat-button>
            <mat-icon matSuffix>{{getSortIcon('last_name')}}</mat-icon>
          </button>
        </th>
        <td mat-cell (click)="toggleDetails(el)" *matCellDef="let el">{{el.last_name}}</td>
      </ng-container>

      <ng-container matColumnDef="last_update">
        <th mat-header-cell *matHeaderCellDef>
          <mat-form-field appearance="outline" class="fill-width filter-textbox">
            <mat-label>last_update</mat-label>
            <input
              matInput
              placeholder="Filter ..."
              type="text"
              autocomplete="off"
              [formControl]="last_update">
            <mat-icon matSuffix>search</mat-icon>
          </mat-form-field>
          <button
            class="sort-button"
            (click)="sort('last_update')"
            mat-button>
            <mat-icon matSuffix>{{getSortIcon('last_update')}}</mat-icon>
          </button>
        </th>
        <td mat-cell (click)="toggleDetails(el)" *matCellDef="let el">{{el.last_update | formatDate}}</td>
      </ng-container>

      <ng-container matColumnDef="delete-instance">
        <th
          mat-header-cell
          *matHeaderCellDef
          [ngClass]="authService.me.canInvoke('magic/modules/sakila/actor', 'delete') ? 'delete-column' : 'hidden'"></th>

        <td
          mat-cell
          *matCellDef="let el"
          [ngClass]="authService.me.canInvoke('magic/modules/sakila/actor', 'delete') ? 'delete-column' : 'hidden'">
          <button
            mat-button
            (click)="deleteEntity(el, {actor_id: el.actor_id})"
            placeholder="Deletes this record">
            <mat-icon matSuffix>delete</mat-icon>
          </button>
        </td>
      </ng-container>

      <ng-container matColumnDef="details">
        <td
          mat-cell
          *matCellDef="let el"
          colspan="4">
          <div
            class="details-sheet"
            *ngIf="shouldDisplayDetails(el)">
            <button
              *ngIf="authService.me.canInvoke('magic/modules/sakila/actor', 'put')"
              mat-button
              class="edit-details"
              (click)="editEntity(el)">
              <mat-icon matSuffix>edit</mat-icon>
            </button>
            <p class="details"><label>actor_id</label><span>{{el.actor_id}}</span></p>
            <p class="details"><label>first_name</label><span>{{el.first_name}}</span></p>
            <p class="details"><label>last_name</label><span>{{el.last_name}}</span></p>
            <p class="details"><label>last_update</label><span>{{el.last_update | formatDate}}</span></p>
          </div>
        </td>
      </ng-container>

      <tr
        mat-header-row
        *matHeaderRowDef="displayedColumns"></tr>

      <tr
        mat-row
        [ngClass]="getClassForRecord(row)"
        *matRowDef="let row; columns: displayedColumns;"></tr>

      <tr
        mat-row
        [ngClass]="getClassForDetails(row)"
        *matRowDef="let row; columns: ['details']; "></tr>

    </table>
    <mat-paginator
      #paginator
      [(length)]="itemsCount"
      [(pageSize)]="filter.limit"
      (page)="paged($event)"
      [pageSizeOptions]="[5,10,25,50]">
    </mat-paginator>

  </div>

</div>
```

Notice how the above HTML is using a mat-table with _two_ `mat-row` declarations. This is
to allow for _"inline viewing"_ as the user wants to see the details of a record.

Authorization above, is determined through the `canInvoke` method, which can be found on the
`GridComponent` base class. This method takes a URL and an HTTP verb, and returns either `true`
or `false`, depending upon whether or not the user has access to invoke the endpoint/verb
combination or not. This method again will dynamically build its authorization declaration,
by invoking the Magic system endpoint, responsible for returning all endpoint/verbs combinations,
associated with the roles in the system that are legally allowed to invoke the endpoint.

This is used to hide and show features in the frontend, according to whether or not the user
is allowed to invoke the functionality associated with whatever GUI element is encapsulating
the feature - Such as _"delete entity"_, _"update entity"_, etc. And since each URL/verb
combination has its own unique set of authorization declarations, this varies from user to user,
depending upon which role the user belongs to.

The modal dialogue for editing and creating entities in your actor table, can be found below.

```typescript
/*
 * This file was automatically generated by Magic. Read more at https://polterguy.github.io
 */

import { Component, Inject } from '@angular/core';
import { MAT_DIALOG_DATA, MatDialogRef, MatSnackBar } from '@angular/material';
import { HttpService } from 'src/app/services/http-service';
import { DialogComponent, DialogData } from '../../../base/dialog.component';

/**
 * Modal dialog for editing your existing Actor entity types, and/or
 * creating new entity types of type Actor.
 */
@Component({
  templateUrl: './edit.actor.component.html',
  styleUrls: ['./edit.actor.component.scss']
})
export class EditActorComponent extends DialogComponent {

  /**
   * Constructor taking a bunch of services injected using dependency injection.
   */
  constructor(
    public dialogRef: MatDialogRef<EditActorComponent>,
    @Inject(MAT_DIALOG_DATA) public data: DialogData,
    protected snackBar: MatSnackBar,
    private service: HttpService) {
    super(snackBar);
    this.primaryKeys = ['actor_id'];
    this.createColumns = [
      'first_name',
      'last_name'
    ];
    this.updateColumns = [
      'actor_id',
      'first_name',
      'last_name'
    ];
  }

  /**
   * Returns a reference to ths DialogData that was dependency injected
   * into component during creation.
   */
  protected getData() {
    return this.data;
  }
  
  /**
   * Returns a reference to the update method, to update entity.
   */
  protected getUpdateMethod() {
    return this.service.actor.update(this.data.entity);
  }
  /**
   * Returns a reference to the create method, to create new entities.
   */
  protected getCreateMethod() {
    return this.service.actor.create(this.data.entity);
  }

  /**
   * Closes dialog.
   * 
   * @param data Entity that was created or updated
   */
  public close(data: any) {
    if (data) {
      this.dialogRef.close(data);
    } else {
      this.dialogRef.close();
    }
  }
}
```

Yet again most of the heavy lifting is done in its base class. Below is the HTML for
the same component.

```html
<h1 mat-dialog-title>{{data.isEdit ? 'Edit' : 'Create'}} Actor</h1>
<div mat-dialog-content>

  <mat-form-field
    *ngIf="canEditColumn('actor_id')"
    class="entity-edit-field">
    <input
      matInput
      type="number"
      step="1"
      (change)="changed('actor_id')"
      placeholder="actor_id"
      [(ngModel)]="data.entity.actor_id"
      autocomplete="off">
  </mat-form-field>

  <mat-form-field
    *ngIf="canEditColumn('first_name')"
    class="entity-edit-field">
    <input
      matInput
      type="text"
      placeholder="first_name"
      (change)="changed('first_name')"
      [(ngModel)]="data.entity.first_name"
      autocomplete="off">
  </mat-form-field>

  <mat-form-field
    *ngIf="canEditColumn('last_name')"
    class="entity-edit-field">
    <input
      matInput
      type="text"
      placeholder="last_name"
      (change)="changed('last_name')"
      [(ngModel)]="data.entity.last_name"
      autocomplete="off">
  </mat-form-field>

</div>

<div mat-dialog-actions>

  <div class="right-aligned">

    <button 
      mat-raised-button
      color="primary"
      (click)="upsert()">Save</button>

    <button
      mat-button
      (click)="close()">Cancel</button>

  </div>

</div>
```

### The backend service

The backend service to use for CRUD operations is called _"http-service.ts"_, and you can
find it inside of your _"services"_ folder. The structure of this generated class, is extremely
formalized, and easily understood. Below is an example of some of its method groups, that are
generated for the Sakila database in the above video.

```typescript
  /**
   * HTTP CRUD service methods for your 'actor' entities.
   */
  get actor() : ICrudEntity {

    return {

      delete: (filter: any) => {
        return this.httpClient.delete<any>(
          environment.apiUrl +
          'magic/modules/sakila/actor' +
          this.getQueryArgs(filter));
      },

      read: (filter: any) => {
        return this.httpClient.get<any>(
          environment.apiUrl +
          'magic/modules/sakila/actor' +
          this.getQueryArgs(filter));
      },

      count: (filter: any) => {
        return this.httpClient.get<any>(
          environment.apiUrl +
          'magic/modules/sakila/actor-count' +
          this.getQueryArgs(filter));
      },

      create: (args: any) => {
        return this.httpClient.post<any>(
          environment.apiUrl +
          'magic/modules/sakila/actor',
          args);
      },

      update: (args: any) => {
        return this.httpClient.put<any>(
          environment.apiUrl +
          'magic/modules/sakila/actor',
          args);
      }
    }
  }
```

Notice how all related methods are grouped by their entity name, allowing you to easily find
the correct method, to for instance create new entities, of some specific type. This is done
with some intelligent usage of TypeScript interfaces, properties, and anonymous types.
This little trick allows you to consume the service for instance as follows.

```typescript
this.httpService.actor.read({filter: 10, offset: 20}).subscribe(res => {
  console.log(res);
});
```
 
Due to the automatically generated TypeScript comments, you'll also have very good
intellisense as you write code, and such easily be able to consume the correct service
method, as you modify and add to the code Magic produced yourself. Each _"component group"_
can return one of either `ICrudEntity` or `ICrdEntity`. The latter is for entities that
for some reasons couldn't create an `update` endpoint. You can see these interfaces below.

```typescript
/**
 * Entity method group containing Create, Read and Delete, but not Update.
 * 
 * This interface is implemented on method groups not containing update,
 * which might happen, if a table have no updateable columns, such as
 * link tables, with only primary keys, etc.
 */
export interface ICrdEntity {

  /**
   * Creates a new entity.
   *
   * @param args Initial values for your entity
   */
  create(args: any) : Observable<any>;

  /**
   * Reads entities from your backend.
   *
   * @param filter Filter condition for items to retrieve
   */
  read(filter: any) : Observable<any>;

  /**
   * Counts entities from your backend.
   *
   * @param filter Filter condition for items to count
   */
  count(filter: any) : Observable<any>;

  /**
   * Deletes one entity from your backend.
   *
   * @param args Filter condition for item to delete, primary key(s) for entity
   */
  delete(args: any) : Observable<any>;
}

/**
 * Entity method group containing all 4 CRUD operations.
 */
export interface ICrudEntity extends ICrdEntity {

  /**
   * Updates one entity in your backend.
   *
   * @param args What item to update, and what values to update it with
   */
  update(args: any) : Observable<any>;
}
```

The comments for the interface methods, will follow you in your IDE's intellisense,
as you type, and provide hints for you automatically, as you write your own code on
top of Magic's generated Angular result.

There are also some additional helper services and components, depending upon which
template you choose as you scaffold your frontend. These includes.

* auth component for managing users and roles in the system
* profile component for managing your user's specific data, such as its password, etc
* home component which is your app's landing page, or home route
* app component, which is the wire frame component, containing your navigation/menu, etc

To modify Magic's wire frame for instance, to apply your own type of menu/navbar,
you only really need to modify _one single component_ - Being the _"app"_ component,
which is the component responsible for loading the currently active router link, and
displaying the menu, etc. This allows you to easily modify the _"looks and feels"_
of the end result, applying your own custom style to it, and your own structure.
We will also look at how to create your completely custom template yourself - But that
is for a later day.

* [Scheduled tasks and persisted function invocations](/tutorials/task-scheduler/)
