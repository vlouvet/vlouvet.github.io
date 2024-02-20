## Intermediate Flask Functionality ##

#### Table of Contents ####

- [Summary of Changes](#summary)
- [New Features](#new-features)
- [Add form entry index page](#part-1-showing-previous-form-entries)
- [Add tag index page](#part-2-showing-previous-tags)
- [Creating new feeling categories](#part-3-adding-feeling-categories)
- [Adding Global Search](#part-4-adding-global-search-functionality)
- [Conclusion](#next-steps-adding-advanced-functionality)

### Summary ###

In this post, we expand on the groundwork laid by the previous blog post located [here.](/2023/09/05/log-your-feelings)

The focus will be on adding index pages that allow the user to see previously added form entries, and tags.

Then, we will create a simple form that allows a user to add new feeling categories to be used in future form entries.

Last of all, we will implement a site-wide search functionality by updating the base.html template.

### Part 1: showing previous form entries ###

In this section, we will expand our app to show an 'index' page for past Form Entries. The page will be a basic table of records queried from the SQLite3 database.

Start by creating a new file 'entries.html' in the templates folder. Example: /dft/templates/entries.html

Copy in the following content, the save the file [entries.html](/supplementary/entries.html)

Most of the template is similar to other templates we've created previously (ex: form.html), except this page uses a table as follows:

```
<table class="table table-striped">
    <thead>
        <tr>
            <th>ID</th>
            <th>Rating</th>
            <th>Message</th>
            <th>Tags</th>
            <th>Date</th>
        </tr>
    </thead>
    <tbody>
        {% for entry in entries %}
        <tr>
            <td>{{ entry.id }}</td>
            <td>{{ entry.rating }}</td>
            <td>{{ entry.message }}</td>
            <td>{ % for tag in entry.tags % }{{ tag.name }}{ % endfor % }</td>
            <td>{{ entry.timestamp }}</td>
        </tr>
        {% endfor %}
    </tbody>
</table>
```

The magic in the code above comes from the block that starts ``` for entry in entries ```

Passing a list of entries from our backend application code into the template, items on the list (in this case entries) are rendered dynamically by the Jinja templating engine.

Each row contains five columns, each displaying a separate field/attribute of the entry record: 

```
<td>{{ entry.id }}</td>
<td>{{ entry.rating }}</td>
<td>{{ entry.message }}</td>
<td>{% for tag in entry.tags %}{{ tag.name }}{% endfor %}</td>
<td>{{ entry.timestamp }}</td>
```

### Part 2: showing previous tags ###

### Part 3: Adding feeling categories ###

### Part 4: Adding global search functionality ###

### Next Steps: Adding Advanced Functionality ###
