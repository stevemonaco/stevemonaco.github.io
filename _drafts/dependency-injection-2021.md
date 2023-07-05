---
layout: post
title: "Dependency Injection and Inversion of Control in C# / .NET"
description: Introduction to DI / IoC Concepts
tags:
  - C#
---

# Objective

This article will teach the fundamental concepts for Dependency Injection (DI) and Inversion of Control (IoC) to those with basic programming skills in C#, but no other prior knowledge. A practical example will be implemented using .NET's `Microsoft.Extensions.DependencyInjection` library, but should be achievable with most other libraries. This article will not teach how to design or implement your own IoC library from scratch.

** Create Multilayer image with High-Level and Low-Level components to demonstrate fully DI and fully not DI-able objects) **

# What Problems Can DI and IoC Solve?

Implementing these strategies into your application will promote looser coupling between components, improve component flexibility, manage component lifecycles, and enable better unit testing. Additionally, your components will be more tidy because much of your object creation code will become centralized and automatic.

# Scenario

We are designing a complex program with many interacting components. We have developed it in a modular fashion so that components can be orchestrated by top-level components instead of having a massive "God class". Each top-level component requires multiple components which may require components themselves.

# What is a Dependency?

In a relationship where `A` requires `B` and `C` to operate, `B` and `C` are dependencies of `A`. The dependent is `A`. Within this article, dependencies are assumed to be instances of types required by a `class`.

# Localized creation with `new`

# External creation with `new`

# Dependency Injection

Dependency Injection (DI) is the process of giving a dependent its dependencies: giving `B` and `C` to `A`. There are three main ways to inject a dependency: as a constructor parameter, as a property, and as a method parameter. Constructor injection is preferred as the system developer can add a required parameter to all constructors. In property injection, the caller must know and remember that they must assign a dependency to a property. Property injection is error-prone relative to constructor injection.

# Inversion of Control

Inversion of Control (IoC) is a design philosophy where portions of flow control of a system can be supplied by the caller instead of the details being fully implemented by the system itself.

# Inversion of Control Containers

IoC Containers are libraries that allow you to create type registrations and scope their lifetimes.

> IoC containers enable the centralization of complex object creation

# The Composition Root

The Composition Root is the section of code where type registrations are configured so components can be composed. This is typically written in an application's startup logic alongside tasks like reading configuration files.

# Service Locator Pattern

The Service Locator pattern shares the traits of object creation centralization, but is considered an anti-pattern. Many implementations would use a globally-accessible static class to resolve and instantiate types. Another common approach used constructor injection to inject the locator for the component itself to resolve types with. These approaches obscure a component's dependencies.

# Guidelines

## DO

Design high-level components to contain only registered types as constructor dependencies
Use Factories when a component requires creating multiple components of a registered type
Consider property injection when circular dependencies exist

## DON'T

Pass the IoC container to a non-factory type
Use Service Locator pattern
