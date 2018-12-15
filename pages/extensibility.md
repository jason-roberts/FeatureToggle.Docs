---
layout: default
title: Extensibility
---

# Extensibility

FeatureToggle is customisable and extensible in a number of ways.

## Creating Your Own Custom Toggles

Just implement `IFeatureToggle` and provide an implementation for `FeatureEnabled` - easy :)

Then just use this as a base class or as a toggle itself.

## Creating Your Own Providers

When using the built-in Toggle types, they default to getting their values from some configuration.

You can create your own provider and use it with the built-in Toggles.

For the `SimpleFeatureToggle` and `SqlFeatureToggle`, create a class that implements `IBooleanToggleValueProvider` and after you instantiate your Toggle set its `BooleanToggleValueProvider` property to an instance of your custom provider.

Alternatively, when you create your Toggle class and derive it from one of the base classes, override the `ToggleValueProvider` property to return an instance of your custom provider.
