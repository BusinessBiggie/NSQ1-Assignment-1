# Bookstore Relational Database Project

My attempt at a PostgreSQL implementation for Amazon.uk's bookstore. :)

## Conceptual model
Before making the conceptual model, I browsed around Amazon to get a better overview of how the categories worked in regards to sub categories, and what data could be found when clicking on a book. The multiplicity might be a bit off here as it was my initil idea.

<img width="681" height="666" alt="image" src="https://github.com/user-attachments/assets/e53f576a-46cd-4761-a6b9-2a1bdd04018d" />

## Logical model
<img width="1558" height="599" alt="image" src="https://github.com/user-attachments/assets/728b416b-b223-41de-b3d4-549a179b46c2" />

### Normalization
I tried to adhere to the 3rd level of normalization such as seperating address from customer, format from book etc...

I also tried to relevant data typing and correct size based on what the entity needed.
One of the most apparent decisions here is the high level of normalization (specifically 3NF).

### Junctions
To handle the many to many relationships Ive used junction tables, such as BookAuthor, since a book can have multiple authors and an author can write multiple books. Similar situations are found with BookCategory and BookCharacter as well as Orderline. Orderline allows an order to contain multiple books and allows quantity and unit pricing.

### Category
As I wanted there to be infinite sub categories, it made sense to go a recursive way, by each category having a parent category. It is then easy to map out the hierarchy of the "category tree"

## Challenges
### Modifying
#### 4
I had issues with Duplicate Key errors, for an example if a book both was noted as "Sciene Fcition(Level 2)" and "Space Opera(Level 3)", when the "Space Opera" was removed and I tried to change go one category up the "tree", so the Space Opera book would become Science fiction, there would be duplicate data. Thus a DO $$ block was madde. THe block copies every book from space opera into science fiction now, then the ON CONFLICT DO NOTHING ensures when a duplicate key error appears it skips the row and keeps going.
### Querying
#### 4
This task was possible the hardest of them all, as there were subcategories that needed to be filtered out. It wasnt enough to just give it a list of categories to "blacklist", there had to be some kind of check if a subcategory where a child of a category which was "blacklisted". 

At first I came to the 4.0 solution, but found an issue regarding the limit of subcategories, it works on a single child depth, but fails after that. After some research I found recursive statements, which made it possible to find all the children of certain categories. It works by finding children of a category, and then reruns the statement finding the childrens children, and so on, until it fails to find new children. Then it returns a list called blacklist, and thus it is possible to find the books which does NOT have the categories.
#### 9
This task was a bit of a challenge as my intial approaches only showed books which had sold copies, this solution was to use left join, and using COALESCE to find the first non null value, e.g. either the sum or 0. If there are sold copies the quantities sold gets added togheter for a total amount, and if there are none sold, it should just display 0. 


P.S sorry about the messy file :( I was time constrained due to my hand
