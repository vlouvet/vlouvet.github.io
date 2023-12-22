# Daily Feeling Tracker App Development Guide #

## A simple micro-blogging app meant to be used to be used anonymously ##

### Table of Contents ###

- [Application Features](#features)
- [Project Summary](#project-summary)
- [Directory Setup Section](#directory-setup)
- [Python Library Setup](#python-library-setup)
- [Application code breakdown](#python-application-code-breakdown)
- [Complete App.py code](#complete-apppy-python-code)
- [Jinja Templating](#jinja-templating)
- [test run the app](#)
- [Deploying our code](#deploying-our-code)

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

### Python Application code breakdown ###

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

In this next section, we define some basic database tables. 

Each table includes a name, and one or more columns. The columns must be defined as a specific data type.


1. The first table is 'form_entry_tags' which associates the 'form entries' table to the 'tags' table.
2. The most basic table is 'form_entries' which stores the blog posts made by a user.
3. Tags are the second most basic table, tags allow a user to add short labels to the form entry before submitting.
    - Form entries can have many tags.
4. Categories are the next table, the category table stores the different feelings a user can select when making a form entry.
    - Form entries can have one, and only one category.


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


Next, we define a function that initializes the database. 

Taking care to not overwrite the database if one exists.

Once the database is created, seed values are entered for some tables.

At the end of this section we call the init_db function we defined above.

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

In this code block we define our first Flask application route.

This route accepts both GET and POST HTTP requests.

If the user sends a GET request, a Jinja template 'form.html' is rendered.

When POSTing data, a new entry is added to the SQLite3 database, then the browser is re-directed to the entry form.

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

Last of all, this code block tells flask to run the application, binding to all available IP addresses on the host, using port 5001

```
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5001)
```
[Back to top](#daily-feeling-tracker-app-development-guide)
### Complete app.py Python code ###

```
import os
import datetime
from flask import Flask, request, render_template, redirect, flash, jsonify, url_for
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy_utils import database_exists
from sqlalchemy import text
#initialize an application object
app = Flask(__name__)
app.config["SECRET_KEY"] = os.environ.get("SECRET_KEY")
# configure the database
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:////app/database/app.db"
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False
# create the database
db = SQLAlchemy(app)
#define the database tables
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

# define the database initialization function
def init_db():
    with app.app_context():
        if not database_exists(db.engine.url):
            db.create_all()
            default_categories = [("Happy", "You are feeling happy")]
            for label, description in default_categories:
                category_obj = Category(label=label, description=description)
                db.session.add(category_obj)
            db.session.commit()

# initialize the database
init_db()

# define the root application route
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

# run the application, bind to all addresses on port 5001
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5001)
```
[Back to top](#daily-feeling-tracker-app-development-guide)

### Jinja templating ###

Flask supports a templating language called Jinja to render static content.

One feature that Flask and Jinja provide is extending templates. This allows us to define a page layout in a 'base.html' file, then import elsewhere.

Create a file 'base.html' in the templates folder folder example: /dft/templates/base.html

The contents of the base.html file should be as follows:

```
  <!DOCTYPE html>
    <html>
      <head>
        <title>Daily Feelings Tracker</title>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css">
        <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"></script>
        <script src="https://unpkg.com/@popperjs/core@2" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/js/bootstrap.min.js"></script>
      </head>
      <body>
        <header>
          <nav class="navbar navbar-expand-lg navbar-light bg-light">
            <ul class="navbar-nav">

              <li class="nav-item">
                <a class="nav-link" href="{{ url_for('form') }}">Home</a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="{{ url_for('entries') }}">Entries</a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="{{ url_for('tags') }}">Tags</a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="{{ url_for('categories') }}">Categories</a>
              </li>
            </ul>
          </nav>
        </header>
        
        <main>
          {% block content %}{% endblock %}
        </main>
      </body>
    </html>
```

Create a file 'form.html' in the templates folder folder example: /dft/templates/form.html

The template file should contain the following code:

```

  {% extends "base.html" %}

  {% block content %}
  <div class="container mt-4">
      <h1>Instant Feeling Tracker Form</h1>
      <form method="post" class="form" enctype="multipart/form-data">
          <div class="form-group">
              <label for="category">Current Feeling:</label>
              <select name="category" class="form-control">
                {% for option in categories %}
                  <option value="{{ option.id }}">{{ option.label }}</option>
                {% endfor %}
              </select>
            </div>
          <div class="form-group">
              <label for="image">Upload Image:</label>
              <input type="file" name="image" class="form-control" accept="image/*" />
          </div>
          <div class="form-group">
              <label for="message">Message:</label>
              <textarea name="message" class="form-control form-control-lg"></textarea>
          </div>
          <div class="form-group">
              <label for="tags">Tag your feelings:</label>
              <input type="text" name="tags" class="form-control" />
          </div>
          <div class="form-group">
              <input type="submit" value="Submit" class="btn btn-primary" />
          </div>
      </form>
      <a href="/entries" class="btn btn-secondary">View Previous Entries</a>
  </div>
  {% endblock %}
```
As you can see, Jinja allows passing and rendering variables from the python code in HTML which is then consumed by a web browser.

The full documentation on Jinja can be found [here.](https://flask.palletsprojects.com/en/2.3.x/templating/)

[Back to top](#daily-feeling-tracker-app-development-guide)
### Test run the app ###

In a linux terminal, within the project directory /dft/, run the following command to start the flask application server:

```
python3 -m flask run
```

The flask application should start running. 

But how does this work you might ask? The Flask application server looks for a file in the current working directory named app.py by default.

If you got this far, stop the flask server by pressing CTRL-C in the terminal and proceed to the last section.

[Back to top](#daily-feeling-tracker-app-development-guide)
### deploying our code ###

Previously, we used the built-in web server functionality provided by Flask to run our app. During development it can be useful

to use the built-in wsgi/http server available in flask - but it has known limitations. In this section we cover how to deploy our app 

directly to a linux host using NGINX and gunicorn. 

In general, a request will be sent by a web browser, received by NGINX, forwarded to gunicorn as a WSGI request, then processed by our Flask application.

A response would then travel in reverse order through these services before finally arriving back at the web browser. Each 'layer' in the 'stack'

#### NGINX configuration ####

On Debian based Linux distributions, Nginx can be installed using the following apt command:

```
sudo apt install nginx
```

Once installed, the nginx server must be configured to proxy requests from the web browser to our WSGI application server.

Locate, or create, the default nginx configuration file (/etc/nginx/conf/nginx.conf) and open it in a text editor.

Replace the contents with the following:

```
events {}

http {
  upstream flask_app {
    server app:5001;
  }

  server {
    listen 5000;
    server_name localhost;
    client_max_body_size 10M;
    location / {
      proxy_pass http://flask_app;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
  }
}

```
Keep in mind this nginx configuration assumes the nginx server will run on the same host as the Gunicorn WSGI application.

save the file and restart the Nginx server with the following command:

```
sudo systemctl restart nginx
```

[Back to top](#daily-feeling-tracker-app-development-guide)
#### Gunicorn ####

Gunicorn is a production grade WSGI server that features caching, worker pooling, among other features.

For a full list of features offered by Gunicorn visit the official site [here.](https://gunicorn.org/#docs)

the gunicorn wsgi server was installed in a previous step, but we can confirm it is installed with the following command:

```
python3 -m pip install gunicorn
```

from the top level project directory (/dft/) run the following command to run the gunicorn WSGI server to host our app.py application.

```
gunicorn -w 4 -b 0.0.0.0:5001 app:app
```

Explanation of command options:

- '-w 4' - run a pool of four workers to server requests
- '-b 0.0.0.0:5001' bind to all IP addresses, on port 5001
- 'app:app' run the application named 'app.py' from the current working directory

[Back to top](#daily-feeling-tracker-app-development-guide)