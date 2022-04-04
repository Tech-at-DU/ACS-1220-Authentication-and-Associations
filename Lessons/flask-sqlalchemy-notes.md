# Books App Notes

Here are some notes on how to perform a range of operations in Flask with SQLAlchemy. 

## Adding a new genre

```python
g = Genre(name="biography")
db.session.add(g)
db.session.commit()
```


## Add a new Author

```python
a = Author(name="Isaac Walter")
db.session.add(a)
db.session.commit()
```

## Add a book

Notice you are using the Author object `a` created above!

```python
b = Book(title="Steve Jobs", author=a)
db.session.add(b)
db.session.commit()
```

## Add a new user 

```python
me = User(username="Me")
db.session.add(b)
db.session.commit()
```

## Add a favorite book to a user

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

## Add a user to a book

Here you are adding a user to a book. This is like saying that this book is a favorite of this user. 
Again you'll user `me` created earlier. This time you'll get a book from the database first. 
Then add the user to that book's user list. 

```python
m = Book.query.all()[0] # get the first book
m.users.append(me)
db.session.add(m)
db.session.commit()
```


