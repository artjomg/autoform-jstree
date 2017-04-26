dguedry:autoform-jstree
=========================

An add-on Meteor package for [aldeed:autoform](https://github.com/aldeed/meteor-autoform). Provides a custom input type for jsTree (https://www.jstree.com).

## Prerequisites

### AutoForm

In a Meteor app directory, enter:

```bash
$ meteor add aldeed:autoform
```

### A jstree package

There are several jstree packages available.  I used the jss:jstree and the "proton" theme:

```
meteor add jss:jstree
meteor add jss:jstree-proton-theme
```

## Installation

In a Meteor app directory, enter:

```bash
$ meteor add dguedry:autoform-jstree
```

## Usage

Specify "jstree" for the `type` attribute of a [String] input. This can be done in a number of ways:

In the schema, which will then work with a `quickForm` or `afQuickFields`:

```js
    myselection: {
        label: "Categories",
        type: [String],
        optional: true,
        autoform: {
            type: 'jstree',
            afFieldInput: {
                settings: {
                    plugins: ["checkbox", "wholerow", "sort"],
                    search: {
                       show_only_matches: true,
                       show_only_matches_children: true,
                       fuzzy: true
                    },
                    themes: {
                        name: 'proton',
                        dots: true,
                        icons: false,
                        responsive: true
                    },
                },
                nodes: function () {
                    return Categories.findOne().node
                }
            }
        }
    },
```

Here's an example of a tree schema.  Note that each node must contain an id, text and, if the node has children, a children field.

```
Categories.attachSchema(new SimpleSchema({
    node: {
        type: [Object],
        optional: true,
        label: 'Category'
    },
    'node.$.id': {
        type: String,
        autoValue: function() {
            if (!this.isSet) {
                return new Meteor.Collection.ObjectID()._str;;
            }
        },
        autoform: {
            type: "hidden",
            label: false
        }  
    },
    'node.$.text': {
        type: String
    },
    'node.$.children': {
        type: [Object],
        optional: true
    },
    'node.$.children.$.id': {
        type: String,
        autoValue: function() {
            if (!this.isSet) {
                return new Meteor.Collection.ObjectID()._str;;
            }
        },
        autoform: {
            type: "hidden",
            label: false
        }
    },
    'node.$.children.$.text': {
        type: String
    },
    'node.$.children.$.children': {
        type: [Object],
        optional: true
    },
    'node.$.children.$.children.$.id': {
        type: String,
        autoValue: function() {
            if (!this.isSet) {
                return new Meteor.Collection.ObjectID()._str;;
            }
        },
        autoform: {
            type: "hidden",
            label: false
        }
    },
    'node.$.children.$.children.$.text': {
        type: String
    }
}))
```
Here is an example on how to add a search field to your jstree instance.

Template Helper
```
Template.adminEditEmployeeTargetDef.events({

  'click #clearsearch'(e) {
    $("div[name='myselection']").jstree(true).clear_search();
    $("#typessearch").val('');
  },

  'keypress #typessearch': function (evt) {
      var value = evt.currentTarget.value;
      $("div[name='myselection']").jstree(true).search(value);
  },

  'keydown #typessearch': function (evt) {
      var value = evt.currentTarget.value;
      console.log(value);
      if (evt.keyCode==8 && value.length <=1){
        $("div[name='myselection']").jstree(true).clear_search();
      }else if (evt.keyCode==8 && value.length >1){
        var value = evt.currentTarget.value;
        $("div[name='myselection']").jstree(true).search(value);
      }
  }
```

Blaze Template

```
<div class="form-group has-feedback has-clear">
<input id="typessearch"  type="text" class="form-control" ng-model="ctrl.searchService.searchTerm" placeholder="Suche"/>
<a id="clearsearch" class="glyphicon glyphicon-remove-sign form-control-feedback form-control-clear" style="pointer-events: auto; text-decoration: none;cursor: pointer;"></a>
</div>

{{>afFormGroup name="myselection"}}
```

## Contributing

Anyone is welcome to contribute. Fork, make your changes, and then submit a pull request.
