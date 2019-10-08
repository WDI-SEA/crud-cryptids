# RESTful Cryptids

We have the GET requests for all of our pages and one POST route to add a new cryptid. That is, however, only __two__ of the four CRUD actions. Time to impliment the UPDATE and DELETE functionality.

## Prerequesites

1. Have a working Dinosaur app
2. Have all the routes from the [Cryptids Lab](./readme.md)!
3. `method-override` node module

## Objectives
* Implement UPDATE functinality to change the values of pre-existing cryptids
* Implement DELETE functinality to destroy a pre-existing cryptid

##### These are the routes we want to implement
| VERB | URL | Action (CRUD) | Description |
|------|-----|---------------|-------------|
| PUT | /cryptids/:id | Update (Update) | edits a specific cryptid |
| DELETE | /cryptids/:id | Destroy (Delete) | destroys a specific cryptid (:id would be replaced by an actual number 1) |

You can add another GET route for a specific edit page or put your form(s) into the show route.