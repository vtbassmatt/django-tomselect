# TomSelect for MIZDB

Autocomplete widgets and views using TomSelect for the MIZDB app.

## Usage

Configure an endpoint for autocomplete requests:

```python
# urls.py
from django.urls import path

from mizdb_tomselect.views import AutocompleteView

urlpatterns = [
    ...
    path('autocomplete/', AutocompleteView.as_view(), name='my_autocomplete_view')
]
```

Use the widgets:

```python
from django import forms

from mizdb_tomselect.widgets import MIZSelect, TabularMIZSelect
from .models import MyModel


class MyForm(forms.Form):
    mizselect = forms.ModelChoiceField(
        MyModel.objects.all(),
        widget=MIZSelect(
            MyModel,
            url='my_autocomplete_view',
        ),
    )
    # Display results in a table, optionally with additional columns:
    mizselect_tabular = forms.ModelChoiceField(
        MyModel.objects.all(),
        widget=TabularMIZSelect(
            MyModel,
            url='my_autocomplete_view',
            # extra_columns is a mapping of model field: column header label for extra columns
            # (besides the two columns for valueField and labelField)
            extra_columns={'name': 'Name', 'something_else': 'Something Else'},
            # The column header label for the labelField column
            label_field_label='My Model Objects',
        ),
    )
```

### Option creation / add page link

The dropdown will include an 'Add' button if you pass the URL pattern name for
the add page of the given model to the widget:

```python
widget = MIZSelect(MyModel, url='my_autocomplete_view', add_url='my_model_add')
```

Clicking on that button sends the user to the add page of the model.

If `create_field` was also passed to the widget, clicking on the button will
create a new object using an AJAX POST request to the autocomplete URL. The
autocomplete view will use the search term that the user put in on the
`create_field` to create the object:

```python
self.model.objects.create(**{self.create_field: data[self.create_field]})
```

Override the view's `create_object` method to alter the creation process.

### Changelist link

The dropdown will include a link to the changelist of the given model if you
pass in the URL pattern name for the changelist.

```python
widget = MIZSelect(MyModel, url='my_autocomplete_view', changelist_url='my_model_changelist')
```

## Development & Demo

```bash
python3 -m venv venv
source venv/bin/activate
pip install -e .
pip install -r requirements.txt
```

Then see the demo for a preview: `python demo/manage.py runserver`

