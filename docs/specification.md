---
layout: page
title: Latest Specification (0.1.0-alpha)
permalink: /specification/
---

# Status

This page presents the latest published version of ISEF, which currently is **0.1.0-alpha**. The specification uses [semantic versioning](https://semver.org/) to clearly signal any breaking changes. The specification is still in an early alpha stage and is likely to change before the initial 1.0 release.

If you catch an error in the specification’s text, or if you want to contribute to evolving the specification, or if you write an implementation, please let us know by opening an issue or pull request at our [GitHub repository](https://github.com/biirrr/isef).

# Introduction

The IIR Study Exchange Format (ISEF) is a specification for the exchange of parts of or complete IIR Studies. In particular it focuses on those aspects that gather self-reported responses from study participants.

It defines both the core data-types to be used and generic instances for some of these data-types.

# Conventions

The key words “**MUST**”, “**MUST** NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

# Core and Optional

The ISEF is split into core and optional parts. Conforming implementations **MUST** implement all core parts of the specification and MAY implement all optional parts. Conforming implementations **MUST** be able to interoperate with other implementations that do or do not implement the optional parts.

# Data-Format

The ISEF uses [JSON:API](https://jsonapi.org) as its data-format. In particular it uses the compound JSON:API document structure to store all required data in a single file, without requiring access to additional, external resources. An ISEF document **MUST** also conform to the rules specified in the JSON:API specification.

# Data-Types

The ISEF consists of one core and four optional data-types. The ``Question`` is the only core data-type and represents both generic questions (single input, multiple choice, ...) and specific questions (gender, age, education, ...) and **MUST** be implemented by all conforming implementations. The three optional data-types are the ``Page``, representing a collection of ``Questions``, the ``Study``, representing a collection ``Pages``, and the ``Transition``, representing the transitions between ``Pages``.

## Question

The ``Question`` is the primary building block of the ISEF and the only data-type that is core and **MUST** thus be implemented by all conforming implementations. The ``Question`` is used to represent both generic questions (single input, multiple choice, ...), where the researcher using the ``Question`` must provide further details before it can be shown to study participants, and also specific questions (gender, age, education, ...) that can be re-used as they are.

The ``Question`` object **MUST** have the following members:

* ``id``: The identifier of the ``Question``, which must be unique with regards to all ``Questions`` in the document.
* ``type``: ``"Question"``.
* ``links``: A single [links object](#links)
* ``attributes``: A single [question attributes object](#question-attributes).

The ``Question`` object **MAY** also have the following member:

* ``relationships``: A single [question relationships object](#question-relationships).

The ``Question`` implements an inheritance hierarchy, which includes a set of 9 [standard questions](#standard_questions) that **MUST** be implemented. The inheritance is important for the [question attributes object](#question-attributes), as attributes are inherited from all ancestor ``Questions``.

An example question would look like this:

```
{
  "id": "Age"
  "type": "Question",
  "links": {
    "self": "https://example.org/my-study#gender"
  },
  "attributes": {
    "name": "age",
    "title": "Please select your age band:"
    "answers": [
      "1",
      "2",
      "3",
      "4",
      "5",
      "6",
      "7",
      "8"
    ],
    "labels": [
      "< 18",
      "18 - 25",
      "26 - 35",
      "36 - 45",
      "46 - 55",
      "56 - 65",
      "66 - 75",
      "> 75"
    ]
  },
  "relationships": {
    "parent": {
      "data": {
        "type": "Question",
        "id": "isefSingleChoice"
      }
    }
  }
}
```


### Question Attributes

The question attributes object **MUST** contain the following keys:

* ``version``: A [semantic version number](https://semver.org/).

The values for each key in the question attributes **MUST** fall into one of the following five categories:

* ``null``: This key **MUST** be ignored by conforming implementations.
* ``{user:singleValue}``: The value for this attribute **MUST** be acquired from the user setting up a study.
* ``{user:multipleValues}``: The list of values for this attribute **MUST** be acquired from the user setting up a study.
* ``{user:multilineText}``: The multi-line text value for this attribute **MUST** be acquired from the user setting up a study.
* other values: To be treated as a constant value that **MUST** be used as specified.

### Question Relationships

The question relationships object **MAY** contain the following key:

* ``parent``: A resource linkage object [as specified in JSON:API](https://jsonapi.org/format/#document-resource-object-linkage). This **MUST** include the ``data`` key to identify the linked object in the included [resource objects](https://jsonapi.org/format/#document-resource-objects). The type of the linked object **MUST** be another ``Question``. If a ``parent`` ``Question`` is provided, then any properties and relationships specified in the parent ``Question`` are inherited, but **MAY** be overriden by specifying that attribute. The exception is the ``version`` key, which **MUST** be provided for each ``Question``.

### Standard Questions

The ISEF provides the following eight base questions which **MUST** be supported:

* [ISEFQuestion](#isefquestion)
* [ISEFDisplay](#isefdisplay)
* [ISEFSingleLineInput](#isefsinglelineinput)
* [ISEFMuliLineInput](#isefmultilineinput)
* [ISEFSingleChoice](#isefsinglechoice)
* [ISEFMultiChoice](#isefmultichoice)
* [ISEFHidden](#isefhidden)
* [ISEFSingleChoiceGrid](#isefsinglechoicegrid)
* [ISEFMultiChoiceGrid](#isefmultichoicegrid)

#### ISEFQuestion

The ``ISEFQuestion`` is the root ``Question`` type, from which all other ``Questions`` **MUST** inherit.

```
{
  "id": "ISEFQuestion",
  "type": "Question",
  "links": {
    "self": "https://biirrr.github.io/isef/specification/questions/isef_question.json"
  },
  "attributes": {
    "version": "0.1",
    "name": "{user:singleValue}",
    "title": "{user:singleValue}"
  }
}
```

#### ISEFDisplay

```
{
  "id": "ISEFDisplay",
  "type": "Question",
  "links": {
    "self": "https://biirrr.github.io/isef/specification/questions/isef_display.json"
  },
  "attributes": {
    "version": "0.1",
    "name": null,
    "title": null,
    "content": "{user:multilineText}",
    "format": "{user:singleValue}"
  },
  "relationships": {
    "parent": {
      "data": {
        "type": "Question",
        "id": "ISEFQuestion"
      }
    }
  }
}
```

#### ISEFSingleLineInput

```
{
  "id": "ISEFSingleLineInput",
  "type": "Question",
  "links": {
    "self": "https://biirrr.github.io/isef/specification/questions/isef_single_line_input.json"
  },
  "attributes": {
    "version": "0.1",
    "validation": "{user:singleValue}"
  },
  "relationships": {
    "parent": {
      "data": {
        "type": "Question",
        "id": "ISEFQuestion"
      }
    }
  }
}
```

#### ISEFMultiLineInput

```
{
  "id": "ISEFMultiLineInput",
  "type": "Question",
  "links": {
    "self": "https://biirrr.github.io/isef/specification/questions/isef_multi_line_input.json"
  },
  "attributes": {
    "version": "0.1"
  },
  "relationships": {
    "parent": {
      "data": {
        "type": "Question",
        "id": "ISEFQuestion"
      }
    }
  }
}
```

#### ISEFSingleChoice

```
{
  "id": "ISEFSingleChoice",
  "type": "Question",
  "links": {
    "self": "https://biirrr.github.io/isef/specification/questions/isef_single_choice.json"
  },
  "attributes": {
    "version": "0.1",
    "values": "{user:multipleValue}",
    "labels": "{user:multipleValues}",
    "display": "{user:singleValue}"
  },
  "relationships": {
    "parent": {
      "data": {
        "type": "Question",
        "id": "ISEFQuestion"
      }
    }
  }
}
```

#### ISEFMultiChoice

```
{
  "id": "ISEFMultiChoice",
  "type": "Question",
  "links": {
    "self": "https://biirrr.github.io/isef/specification/questions/isef_multi_choice.json"
  },
  "attributes": {
    "version": "0.1",
    "values": "{user:multipleValues}",
    "labels": "{user:multipleValues}",
    "display": "{user:singleValue}"
  },
  "relationships": {
    "parent": {
      "data": {
        "type": "Question",
        "id": "ISEFQuestion"
      }
    }
  }
}
```

#### ISEFHidden

```
{
  "id": "ISEFSingleChoice",
  "type": "Question",
  "links": {
    "self": "https://biirrr.github.io/isef/specification/questions/isef_hidden.json"
  },
  "attributes": {
    "version": "0.1",
    "title": null,
    "value": "{user:singleValue}"
  },
  "relationships": {
    "parent": {
      "data": {
        "type": "Question",
        "id": "ISEFQuestion"
      }
    }
  }
}
```


#### ISEFSingleChoiceGrid

```
{
  "id": "ISEFSingleChoiceGrid",
  "type": "Question",
  "links": {
    "self": "https://biirrr.github.io/isef/specification/questions/isef_single_choice_grid.json"
  },
  "attributes": {
    "version": "0.1",
    "column_values": "{user:multipleValues}",
    "column_labels": "{user:multipleValues}",
    "row_values": "{user:multipleValues}",
    "row_labels": "{user:multipleValues}"
  },
  "relationships": {
    "parent": {
      "data": {
        "type": "Question",
        "id": "ISEFQuestion"
      }
    }
  }
}
```

#### ISEFMultiChoiceGrid

```
{
  "id": "ISEFMultiChoiceGrid",
  "type": "Question",
  "links": {
    "self": "https://biirrr.github.io/isef/specification/questions/isef_multi_choice_grid.json"
  },
  "attributes": {
    "version": "0.1",
    "column_values": "{user:multipleValues}",
    "column_labels": "{user:multipleValues}",
    "row_values": "{user:multipleValues}",
    "row_labels": "{user:multipleValues}"
  },
  "relationships": {
    "parent": {
      "data": {
        "type": "Question",
        "id": "ISEFQuestion"
      }
    }
  }
}
```

## Page

A ``Page`` is an ordered collection of ``Questions``, which together form a cohesive unit in the study. These might be a set of questions that together acquire descriptive demographics about participants, but they also might be something more focused, such as a single instrument.

## Study

The ``Study`` groups a set of ``Pages`` into a cohesive whole. The ordering of ``Pages`` in the ``Study`` **MUST** be used as the ordering when displaying the ``Pages`` to participants, except if ``Transitions`` are also provided, in which case those should be used.

## Transition

The ``Transition`` represents the link between two ``Pages``, the source ``Page`` the participant views first and the target ``Page`` the participant transitions to after completing the source ``Page``'s ``Questions``.

A ``Page`` can have multiple ``Transitions``, provided that the ``Transitions`` have conditions specified on them. In its initial version the ISEF only supports a single condition type, namely ``Transitions`` conditional on responses provided by participants to previous ``Questions``.
