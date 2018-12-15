---
layout: default
title: FeatureToggle Documentation Home
---

# Simple, Reliable Feature Toggles in .NET

## [Pluralsight Feature Toggle Course](http://bit.ly/psfeaturetoggle)

## [Installation and Basic Usage](pages/usage.html)

## [Customization and extensibility](pages/extensibility.html)

## Design Goals

### Flexible Provider Model

Can get the configured toggle value from the built-in default providers, or easily create and supply your own providers

### No Magic Strings

Toggles should be real things (objects) not just a loosely typed string. This helps with removing the toggle after use:

- Can perform a "find uses" of the Toggle class to see where it's used
- Can just delete the Toggle class and see where build fails.

### No Default Fallback Value

If a toggle gets its value from a config file, and the config doesn't exist or is invalid, then you'll get an exception.

FeatureToggle will never attempt to guess whether a toggle is enabled or not.


[View on GitHub](https://github.com/jason-roberts/FeatureToggle)

[Examples](https://github.com/jason-roberts/FeatureToggle/tree/master/src/Examples)

[Documentation for v1.x](pages/v1x.html)

