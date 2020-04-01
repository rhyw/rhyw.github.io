---
layout: post
title:  "jsl step by step tutorial"
date:   2016-05-25 15:33:38 +0800
categories: jsl tutorial
---

I've came across an internal project and noticed it's using `jsl` to manipulate [json schema][json-schema] updates. After walked through the tutorial, I noticed it's interesting and worth figuring out how it works. Thus I've tried several examples as abstracted from the [tutorial][jsl-tutorial]:

{% highlight python %}
import jsl

class Entry(jsl.Document):
    name = jsl.StringField(required=True)

class File(Entry):
    content = jsl.StringField(required=True)

class Directory(Entry):
    content = jsl.ArrayField(jsl.OneOfField([
        jsl.DocumentField(File, as_ref=True),
        jsl.DocumentField(jsl.RECURSIVE_REFERENCE_CONSTANT)
    ]), required=True)
{% endhighlight %}

The above snippets is pretty much like what we define models under Django. `jsl.Document` get a classmethod `get_schema`, sample output:

{% highlight python %}
In [16]: Directory.get_schema()
Out[16]: 
{'$ref': '#/definitions/__main__.Directory',
 '$schema': 'http://json-schema.org/draft-04/schema#',
 'definitions': {'__main__.Directory': {'additionalProperties': False,
   'properties': {'content': {'items': {'oneOf': [{'$ref': '#/definitions/__main__.File'},
       {'$ref': '#/definitions/__main__.Directory'}]},
     'type': 'array'},
    'name': {'type': 'string'}},
   'required': ['name', 'content'],
   'type': 'object'},
  '__main__.File': {'additionalProperties': False,
   'properties': {'content': {'type': 'string'}, 'name': {'type': 'string'}},
   'required': ['name', 'content'],
   'type': 'object'}}}
{% endhighlight %}

So from the output we see for each instance we get following attributes automatically:
{% highlight python %}
{
    "$schema": "http://json-schema.org/draft-04/schema#",
    "$ref": "#/definitions/directory"
    "definitions": {
    },
 }
{% endhighlight %}

Where `$ref` is what I understand as its identifier, which can be referenced by other objects, and the `$shema` is a common attribute, all user defined attributes goes to `definitions`. Below is a sample snippet extracting all the details of attribute `definitions`:
{% highlight python %}
from collections imoprt OrderedDict
import json

schema_file = "directory.schema"

schema = Directory.get_schema(ordered=True)
definitions = schema["definitions"].items()
schema["definitions"] = OrderedDict(sorted(definitions))
with open(schema_file, "w") as f:
    json.dump(schema, f, indent=4)
{% endhighlight %}

## Main features

JSL introduces the notion of a `document` and provides a set of `fields`.

The schema of a document is always `{"type": "object"}`, whose `properties` contain the schemas of the fields of the document. A document may be thought of as a `DictField` with some special abilities. A document is a class, thus it has a name, by which it can be referenced from another document and either inlined or included using the `{"$ref": "..."}` syntax (see DocumentField and its `as_ref` parameter). Also documents can be **recursive**.

The most useful method of Document and the fields is `Document.get_schema()`.

Fields and their parameters are named correspondingly to the keywords described in the JSON Schema standard. So getting started with JSL will be easy for those familiar with the standard.

## Variables and Scopes
### Using variables

See below sample of using variables:
{% highlight python %}
# to describe structures of POST requests
REQUEST_ROLE = 'request'
# to describe structures of responses
RESPONSE_ROLE = 'response'
# to describe structures of database documents
DB_ROLE = 'db'
true_if_not_request = jsl.Var({
    jsl.not_(REQUEST_ROLE): True
})

class User(jsl.Document):
    """
    `id` required or not depends on the roles defined above.
    i.e., it's only required if the role is a 'POST' request.
    """
    id = jsl.StringField(required=true_if_not_request)
    login = jsl.StringField(required=True, min_length=3, max_length=20)
{% endhighlight %}

To sepcify role while getting role-dependent schema: `User.get_schema(role=REQUEST_ROLE)`.

### Using Scopes
Varaibles VS Scopes on document definition:
{% highlight python %}
class User(jsl.Document):
    id = jsl.StringField(required=true_if_not_request)
    login = jsl.StringField(required=True, min_length=3, max_length=20)
    version = jsl.Var({
        DB_ROLE: jsl.StringField(required=True)
    })

# =======
class User(jsl.Document):
    id = jsl.StringField(required=true_if_not_request)
    login = jsl.StringField(required=True, min_length=3, max_length=20)

    with jsl.Scope(DB_ROLE) as db_scope:
        db_scope.version = jsl.StringField(required=True)        
{% endhighlight %}

A scope is *a set of* `fields` and a `matcher`. A scope can be added to a document, and if the matcher of a scope returns True, its fields will be present in the resulting schema. A document may contain arbitrary number of scopes, e.g.:
{% highlight python %}
class Message(jsl.Document):
    created_at = jsl.IntField(required=True)
    content = jsl.StringField(required=True)

class User(jsl.Document):
    id = jsl.StringField(required=true_if_not_request)
    login = jsl.StringField(required=True, min_length=3, max_length=20)

    with jsl.Scope(jsl.not_(REQUEST_ROLE)) as full_scope:
        # a new user can not have messages
        full_scope.messages = jsl.ArrayField(
            jsl.DocumentField(Message), required=True)

    with jsl.Scope(DB_ROLE) as db_scope:
        db_scope.version = jsl.StringField(required=True)
{% endhighlight %}

## Document inheritance

There are four inheritance modes available in JSL: `inline`, `all-of`, `any-of`, and `one-of`, there's an [example][inheritence-sample] about how the generated schema varies between `inline`/`all-of`.

[json-schema]:  http://json-schema.org/documentation.html
[jsl-tutorial]: https://jsl.readthedocs.io/en/latest/tutorial.html
[inheritence-sample]: https://jsl.readthedocs.io/en/latest/tutorial.html#example