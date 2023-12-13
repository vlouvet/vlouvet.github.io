# Daily Feeling Tracker App Development Guide #

## A simple micro-blogging app meant to be used to be used anonymously ##

### Features: ###
  - Post anonymously, no user account required
  - Posts can include text, tags, feeling category (picklist), and attached images
  - Users can add new feeling categories
  - Search tags by keyword
  - Data is stored in a SQLite3 database


### Project Summary ###

    The purpose of this project is to create an example application that is actually pretty useful.

While it is a toy application, lacking features that would be required to make it a _product_
it includes all the components of a normal web application (Frontend presentation, Backend app logic, Database to store records).

This application is used in a separate blog post ["Dev Setup Guide"](2023-08-10-dev-setup-guide)


### Directory Setup ###

We will be using a basic folder structure from the Flask web framework ["Link"] (LINK).

- /dft (project_root)
  - /Database
  - /templates
  - /tests
  
### Python Library Setup ###

Create a requirements.txt file at the project root (/dft) and enter the following:
```
flask
flask-sqlalchemy
sqlalchemy_utils
gunicorn
```

Next, use pip to install the required libraries

```
python3 -m pip install -r requirements.txt
```

Now we're ready to start creating our app.py file which will receive requests from NGINX. 

Create a file 'app.py' at the project root (/dft) and enter the following:

The first section of the file specifies all the python libraries to be imported.

```
import os
import datetime
from flask import Flask, request, render_template, redirect, flash, jsonify, url_for
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy_utils import database_exists
from sqlalchemy import text
```

Next, we set up the Flask application configuration parameters. We define a secret key, and a SQL database.

```
app = Flask(__name__)
app.config["SECRET_KEY"] = os.environ.get("SECRET_KEY")
# configure the database
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:////app/database/app.db"
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False
# create the database
db = SQLAlchemy(app)

```

In this next section, we define all the database tables. Each table contains a number of columns, some tables contain relationships (links) to other tables.

```
form_entry_tags = db.Table(
    "form_entry_tags",
    db.Model.metadata,
    db.Column("form_entries", db.Integer, db.ForeignKey("form_entry.id")),
    db.Column("tags", db.Integer, db.ForeignKey("tag.id")),
)


class Tag(db.Model):
    __name__ = "tags"
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100))


class FormEntry(db.Model):
    __name__ = "form_entries"
    id = db.Column(db.Integer, primary_key=True)
    uastring = db.Column(db.String(200))
    message = db.Column(db.String(200))
    timestamp = db.Column(db.DateTime)
    tags = db.relationship("Tag", secondary=form_entry_tags)
    image = db.Column(db.LargeBinary)
    category_id = db.Column(db.Integer, db.ForeignKey("category.id"))
    category = db.relationship("Category", backref="form_entries")


class Category(db.Model):
    __name__ = "Category"
    id = db.Column(db.Integer, primary_key=True)
    label = db.Column(db.Integer, nullable=False)
    description = db.Column(db.String(200))

```

Next, we define a function that initializes the database. Taking care to not overwrite the database if one exists. Once the database is created, seed values are entered for some tables. at the end of this section we call the init_db function we defined above.

```
def init_db():
    with app.app_context():
        if not database_exists(db.engine.url):
            db.create_all()
            default_categories = [("Happy", "You are feeling happy")]
            for label, description in default_categories:
                category_obj = Category(label=label, description=description)
                db.session.add(category_obj)
            db.session.commit()


init_db()
```

In this code block we define our first Flask application route. This route accepts both GET and POST HTTP requests.

When POSTing data, a new entry is added to the SQLite3 database, then the browser is re-directed to the entry form.

If the user sends a GET request, a Jinja template 'form.html' is rendered.

```

@app.route("/", methods=["GET", "POST"])
def form():
    if request.method == "POST":
        # Load categories table from the database
        category = Category.query.filter_by(label=request.form["category"]).first()
        # Create the form entry
        entry = FormEntry(
            category=category,
            message=request.form["message"],
            timestamp=datetime.datetime.now(),
            uastring=request.headers.get("User-Agent"),
        )
        entry.image = request.files["image"].read()

        # Create a new tag
        tag_name = request.form.get("tags")
        tag = Tag.query.filter_by(name=tag_name).first()
        if not tag:
            tag = Tag(name=tag_name)
            db.session.add(tag)
        # Add the tag to the FormEntry's tags list
        entry.tags.append(tag)
        # Save the form entry to the database
        db.session.add(entry)
        db.session.commit()
        flash(f"{request.form['tags']}")
        return redirect(url_for("form"))
    else:
        categories = Category.query.all()
        return render_template("form.html", categories=categories)
```

Last of all, this code block tells flask to run the application, binding to all available IP addresses, on port 5001

```
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5001)
```