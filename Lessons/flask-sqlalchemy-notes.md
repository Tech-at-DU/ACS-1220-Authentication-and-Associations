# Books App Notes

Here are some notes on how to perform a range of operations in Flask with SQLAlchemy. 

## Select 

### Get all Books 

```python
allbooks = Book.query.all()
```

You can get any book from the `allbooks` list like this: 

```python
allbooks[0] # get the first book
```

### Search with a Filter

Find a book by it's title. Here you are search for an exact match. 

```python
b1 = Book.query.filter_by(title="To Kill a Mockingbird").one()
```

### Using LIKE

Here you can search for a partial string. Similar to using the LIKE key word in SQL. 

Notice that each model has a `like` method that you call on any property! 

```python
b = Book.query.filter(Book.title.like("%St%")) # all books with "St" in the title
b.all()
```

Here you're searching "%St%". This will find any title that contains the string "st" 
with any other characters before or after. This search is case insensitive and will find 
both "The string cheese" and "Steve Jobs".

## Insert 

These examples cover adding new records to tables. 

Note how Join tables are treated as lists on their relationship tables.

### Adding a new genre

```python
g = Genre(name="biography")
db.session.add(g)
db.session.commit()
```


### Add a new Author

```python
a = Author(name="Isaac Walter")
db.session.add(a)
db.session.commit()
```

### Add a book

Notice you are using the Author object `a` created above!

```python
b = Book(title="Steve Jobs", author=a)
db.session.add(b)
db.session.commit()
```

### Add a new user 

```python
me = User(username="Me")
db.session.add(b)
db.session.commit()
```

### Add a favorite book to a user

Notice that `favorite_books` is our join table but we treat it like a list! 
Here you are appending the Book `b`, created above, to the User `me`, also created above, favorite_books list. 

Here the favorite books table represents a relationship between Users and Books. In SQL this is treated as a separate table. 
In Python the User and Book objects treat the relationship as a List. Appending a Book to a User's favorite books list will 
create a new row in the table. 

```python
me.favorite_books.append(b)
db.session.add(me)
db.session.commit()
```

### Add a user to a book

Here you are adding a user to a book. This is like saying that this book is a favorite of this user. 
Again you'll user `me` created earlier. This time you'll get a book from the database first. 
Then add the user to that book's user list. 

```python
m = Book.query.all()[0] # get the first book
m.users.append(me)
db.session.add(m)
db.session.commit()
```

## Handling Routes

To display all of the Books in a route you need to get the books and pass them to the template. 

### Get Books in a route 

```python
@main.route('/books')
def bookspage():
    books = Book.query.all() # Get all of the books 
    return render_template('home.html', books=books) # pass the books to the template
```

Notice the books list is passed to the template in `render_template`

### Display Books in a Template

A template will receive the variables passed through the `render_template` method. 
This example follows the one above and uses the `books` list passed to the template. 

```html
{% for book in books %}
  <a href="#">
    <h2>{{ book.title }}</h2>
  </a>
  <span class="">{{ book.author.name }}</span>
  <hr>
{% endfor %}
```

Here you are looping over each `book` in `books`. You display the title of each book. 

Notice the author is an object with it's own properties, including `name`.

