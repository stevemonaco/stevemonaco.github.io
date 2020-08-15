---
layout: post
title: "WPF in 2020: Sample Implementations of MVVM"
date: 2020-08-14 21:00:00
description: Model-View-ViewModel Implementations in WPF
tags:
 - C#
 - WPF
 - MVVM
---

# Sample Implementations



Each implementation will be implemented without an MVVM Framework before moving to the Stylet (WPF exclusive) MVVM Framework in future articles. `PropertyChangedBase` and `RelayCommand` from the previous section will be used. Each implementation will show the same View in the screenshot below, but have different MVVM implementations.

# View-First Implementation without Dependency Injection

This is the simplest scenario to implement MVVM in WPF. This basic approach requires the View to explicitly know its associated ViewModel at compile-time, but requires only minor changes.

