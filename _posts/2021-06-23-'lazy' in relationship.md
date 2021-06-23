---
layout: post
published: true
tags:
- sqlalchemy
- db

---



I have been confused about the 'lazy' parameter in SQLAlchemy's relationship() for a long time, and I came across with this problem again today when dealing with a project that needs a many-to-many relationship. As a result, I am tring to figure it out when to use each lazy strategy and summrize it here.

```python
tags = db.Table('tags',
    db.Column('tag_id', db.Integer, db.ForeignKey('tag.id'), primary_key=True),
    db.Column('page_id', db.Integer, db.ForeignKey('page.id'), primary_key=True)
)

class Page(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    tags = db.relationship('Tag', secondary=tags, lazy=<???>,
        backref=db.backref('pages', lazy=<???>))
     # what lazy strategy should I use???

class Tag(db.Model):
    id = db.Column(db.Integer, primary_key=True)
```



## What are the options?

- 'select' (LAZY)
- 'dynamic' (LAZY)
- 'joined' (EAGER)
- 'subquery' (EAGER)

Now, let's dive into each of them.

### 'select' or True

The simpliest one; it would issue the query when accessing the related attribute **for the first time**.

If we have 100 pages to load, and then access tags on each page, we need to firstly query these pages with one SQL statements, and then query tags on each page with one SQL statements; a total of 101 SQL statements would be issued.

```python
class Page(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    tags = db.relationship('Tag', secondary=tags, lazy=True,
        backref=db.backref('pages', lazy=True))
session.query(Page).first().tags
# -> List[Tag]
```

### 'dynamic'

When accessing the related attribute **for the first time**, it would return **an aggregate object** which you can apply certain actions, such as 'all()', 'filter()', 'filter_by()' and 'order_by(),' on it. When actions are applied, the query would be issued.

``` python
class Page(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    tags = db.relationship('Tag', secondary=tags, lazy='dynamic',
        backref=db.backref('pages', lazy='dynamic'))
session.query(Page).first().tags
# -> a sqlalchemy query object
session.query(Page).first().tags.all()
# -> List[Tag]
```

### 'joined' and 'subquery'

These strategies would load the related attribute when returning the parent object.

 Let's go back to the previous example, to load 100 pages and their tags with 'joined' strategy will only issued 1 SQL statement since it literally '**left joins**' two tables.(slightly more expensive)

With the 'subquery' strategy, to load 100 pages and their tags will issued 2 SQL statement, one of them is the subquery doing '**inner join**.'

```python
class Page(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    tags = db.relationship('Tag', secondary=tags, lazy='subquery',
        backref=db.backref('pages', lazy='subquery'))
session.query(Page).first().tags
# -> List[Tag]
```



