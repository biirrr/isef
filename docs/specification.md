---
layout: page
title: Latest Specification (0.1.0-alpha)
permalink: /specification/
---

# Status

This page presents the latest published version of ISEF, which currently is **0.1.0-alpha**. The specification uses [semantic versioning](https://semver.org/) to clearly signal any breaking changes.

If you catch an error in the specification’s text, or if you want to contribute to evolving the specification, or if you write an implementation, please let us know by opening an issue or pull request at our [GitHub repository](https://github.com/biirrr/isef).

# Introduction

The IIR Study Exchange Format (ISEF) is a specification for the exchange of parts of or complete IIR Studies. In particular it focuses on those aspects that gather self-reported responses from study participants.

It defines both the core data-types to be used and generic instances for some of these data-types.

# Conventions

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

# Core and Optional

The ISEF is split into core and optional parts. Conforming implementations MUST implement all core parts of the specification and MAY implement all optional parts. Conforming implementations MUST be able to interoperate with other implementations that do or do not implement the optional parts.

# Data-Format

The ISEF uses [JSON:API](https://jsonapi.org) as its data-format.

# Data-Types

The ISEF consists of one core and four optional data-types. The ``Question`` is the only core data-type and represents both generic questions (single input, multiple choice, ...) and specific questions (gender, age, education, ...). The three optional data-types are the ``Page``, representing a collection of ``Questions``, the ``Study``, representing a collection ``Pages``, and the ``Transition``, representing the transitions between ``Pages``.

## Question

The ``Question`` is the primary building block of the ISEF and the only data-type that is core and MUST thus be implemented by all conforming implementations. The ``Question`` is used to represent both generic questions (single input, multiple choice, ...), where the researcher using the ``Question`` must provide further details before it can be shown to study participants, and also specific questions (gender, age, education, ...) that can be re-used as is.

## Page

A ``Page`` is an ordered collection of ``Questions``, which together form a cohesive unit in the study. These might be a set of questions that together acquire descriptive demographics about participants, but they also might be something more focused, such as a single instrument.

## Study

The ``Study`` groups a set of ``Pages`` into a cohesive whole. The ordering of ``Pages`` in the ``Study`` MUST be used as the ordering when displaying the ``Pages`` to participants, except if ``Transitions`` are also provided, in which case those should be used.

## Transition

The ``Transition`` represents the link between two ``Pages``, the source ``Page`` the participant views first and the target ``Page`` the participant transitions to after completing the source ``Page``'s ``Questions``.

A ``Page`` can have multiple ``Transitions``, provided that the ``Transitions`` have conditions specified on them. In its initial version the ISEF only supports a single condition type, namely ``Transitions`` conditional on responses provided by participants to previous ``Questions``.

# Instances
