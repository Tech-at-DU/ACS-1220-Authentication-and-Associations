# Flask WTForms Notes

Here are some code snippets and notes for using WTForms and Flask. 

## What is WTForms?

WTForms is a library for Python and Flask that generates a form and it's elements, and 
handles form input. 

When using WTForms you will be work with it in two places: in the routes and in your
templates.  

In your routes you will define a class that generates a form object responsible for a 
specific form your site will serve. 

In your template you will use the form object to generate the html elements that display 
the form elements in your html pages. 

## Creating a Form

Create a form by extending FlaskForm. 

```Python
from flask_wtf import FlaskForm

class CreateEventForm(FlaskForm):
  # Define your form elements here
  # ...
```

### Creating Form elements 

For elements are the elements that accept form input. Things like: text input, select menu, 
checkboxes, radio buttons etc. 

Create a form text input like this: 

```Python
from flask_wtf import FlaskForm
from wtforms import StringField

class CreateEventForm(FlaskForm):
  # Define your form elements here
  title = StringField("Event title:")
```

### Passing the form to the template

To use the form you need to create an instance of the form and pass it to your template 
via your route.

```Python
@main.route('/create', methods=['GET', 'POST'])
def create():
  form = CreateEventForm() # create the form object
  # ...
  return render_template('create.html', form=form) # pass form to template
```

## Displayng form elements 

Display a form element in your template you will use methods of the form object in your 
template to display the piecves of your form. 

In the example above the `CreateEventForm` has a `title` attribute. This is a text input. 
In the template you can display it like this: 

```HTML
<form>
...
{{ form.title.label }} <!-- Display a label for the element -->
{{ form.title(size=20) }} <!-- Display the element -->
...
</form>
```

The output would look like this: 

```HTML
<form>
...
<label for="title">Event title:</label>
<input id="title" name="title" size="20" type="text" value="">

...
</form>
```

## Form elements

There are many different types of form element. Some of these require some extra
work. 

### Submit button

Use a submit button to submit your form. 

```Python
from wtforms import SubmitField

class CreateEventForm(FlaskForm):
  ...
  submit = SubmitField('Submit') # set the title of the button here! 
```

Display the submit button: 

```HTML
{{ form.submit }}
```

Generates: 

```HTML
<input id="submit" name="submit" type="submit" value="Add new event">
```

### TextArea

TextArea displays a multiline text input. 

```Python
from wtforms.widgets import TextArea # get TextArea from widgets!

class CreateEventForm(FlaskForm):
  ...
  description = StringField('Text', widget=TextArea())
```

Display the TextArea:

```HTML
{{ form.description.label }} <!-- Display the label -->
{{ form.description(size=20) }} <!-- Display the TextArea  with font size 20 -->
```

### Display a Date input 

A date input takes a date and or time. 

There was a lot of confusing information about this. It seems there are 
different ways to do it based on different versions of WTForms. This is 
what worked for me. 

```Python
from wtforms.fields import DateField
from wtforms.widgets import DateTimeInput

class CreateEventForm(FlaskForm):
  ...
  date = DateField("Date", format="%Y-%m-%d", widget=DateTimeInput())
```

Notice: format is setting the date format we are expecting. In this case: 
Year-month-day. For example: 2022-05-6. 

You have also included the date widget: `widget=DateTimeInput()`

Use this in your template: 

```HTML
{{ form.date.label }}
{{ form.date }}
```

## Validating Form input 

WTForms includes validators. Use these to guarantee input is the type and 
format you are asking for. 

```Python
from wtforms.validators import DataRequired

class CreateEventForm(FlaskForm):
    title = StringField('Event title:', validators=[DataRequired()])
```

Here you added a list of validators that includes the `DataRequired()` 
validator. DataRequired checks that field is not empty. Read more here: 
https://wtforms.readthedocs.io/en/2.3.x/validators/

## Handling Form input

You'll handle form input at a route. This could be the same route that 
issued the form. This is what happens in the example above. The `/create` 
route handles both `GET` and `POST` requests. The form is issued with a `GET` 
request and is handled (results returned) for the `POST` request. 

```Python
@main.route('/create', methods=['GET', 'POST'])
def create():
  form = CreateEventForm() # create the form object
  if request.method == 'POST':
    # Handle form here
    input_title = request.form.get('title') # get the title input 
    ...
  else: 
    # Render the form here!
    return render_template('create.html', form=form) # pass form to template
```

Use `reqest.form.get('field-name')` to get the value from a field. 

Notice that you are getting the values from your form by the name of the 
attribute. 

