---
layout: post
title: "WPF in 2020: Concepts and Responsibilities of MVVM"
date: 2020-08-14 20:00:00
description: Model-View-ViewModel Architecture for GUI applications
toc: true
wide: true
tags:
 - C#
 - WPF
 - MVVM
---

# Concepts
Model-View-ViewModel (MVVM) architecture is a commonly used pattern in XAML frameworks to implement the design principle of Separation of Concerns (SoC). While the number of components will grow compared to a solution which uses the View's code-behind for everything, each component in an MVVM application will be much simpler and specific. The result is an application that is easier to maintain, test, and develop as the application's complexity grows. The tradeoff is that MVVM has a learning curve and adds a degree of mostly fixed, one-time complexity cost when starting a project.

# Responsibilities
![MVVM Flowchart]({{ '/assets/images/wpf-mvvm/MVVM-Flowchart-Large.png' | relative_url }})

The above image demonstrates the dependency relationships for MVVM: The View knows the ViewModel and the ViewModel knows the Model (or Service Layer). Equally important is that the Model does not know about the View or ViewModel and the ViewModel does not know about the View.

## View
The View includes the visual layout, control behavior, control appearance, control events, and raw user input events. The View is your code/XAML that directly targets a specific GUI framework, any extensions created to establish compatibility with your ViewModels, as well as the GUI framework itself.

## ViewModel
The ViewModel (VM) is a `class` that contains methods to invoke and properties to bind to from the View. The VM also knows how to perform operations on the Model, either directly or indirectly through an application service layer. Ideally, the VM code should strive to look like neutral C# code with three exceptions:

1. The VM should implement `INotifyPropertyChanged`. Most often this is done by inheriting a base class like `PropertyChangedBase` shown later.
2. Properties that are updated from the VM and trigger a View update must raise the [`PropertyChanged`](https://docs.microsoft.com/en-us/dotnet/framework/wpf/data/how-to-implement-property-change-notification) event.
3. Methods on the VM to be invoked by the View should be wrapped in an `ICommand`.

Some MVVM Frameworks such as [Stylet](https://github.com/canton7/Stylet) allow you to skip point 3 and write even more neutral VMs.

Scenarios that are good VM commands:
* Pause/Play music
* Undo/Redo commands with complex changes
* Removing an item from a collection

Scenarios that are within the View's responsibility:
* A button was hovered and should be highlighted
* A mouse click event containing View pixel locations
* A ListBox event when a selected item has changed (more appropriately solved via bindings)

## Model
The Model is the most ambiguously defined component of MVVM. Most often, the Model is regarded a database entity. Other times, they were mixed with DTOs (data transfer objects), domain objects, POCOs (Plain Old CLR Objects), or even the application services layer itself. I prefer a more broadly defined concept of the Model:

    The Model is an external source of truth.

This means that changes made within the View and VM are not realized until the changes are synchronized with the Model. Furthermore, isolating your Model is key to not letting application logic leak into your GUI. Being external to your ViewModel, this approach isolates your core problem solving code into a happy little island that is loosely coupled from the Desktop, Mobile, Console, and Web. Your most vital code will have better testability and survive technology transitions.

# Auxillary MVVM Components

## INotifyPropertyChanged
[`INotifyPropertyChanged`](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.inotifypropertychanged) (INPC) is a core concept for all XAML-based frameworks and alerts the View when an update has occurred in the ViewModel. The typical INPC boilerplate is often wrapped into reusable class commonly named `PropertyChangedBase` or `ViewModelBase`:

```cs
public abstract class PropertyChangedBase : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;
    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
        => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

    protected bool SetField<T>(ref T field, T value, [CallerMemberName] string propertyName = null)
    {
        if (EqualityComparer<T>.Default.Equals(field, value))
            return false;

        field = value;
        OnPropertyChanged(propertyName);
        return true;
    }
}
```

Usage of this class will be demonstrated in the sample implementations in the next article. The basic idea is to use `SetField` within the property setter. Besides the property declarations themselves, properties are used in a natural fashion. The equality check prevents recursive updates that could lead to a `StackOverflowException`.

## ICommand
[`ICommand`](https://docs.microsoft.com/en-us/dotnet/api/system.windows.input.icommand) is another core concept and is how the View executes methods on a ViewModel. Like INPC, the `ICommand` boilerplate is also commonly wrapped into reusable classes such as `RelayCommand` and `RelayCommand<T>` shown below:

```cs
public class RelayCommand : ICommand
{
	private readonly Action _execute = null;
	private readonly Func<bool> _canExecute = null;

	public event EventHandler CanExecuteChanged
	{
		add { CommandManager.RequerySuggested += value; }
		remove { CommandManager.RequerySuggested -= value; }
	}

	public RelayCommand(Action methodToExecute)
	{
		_execute = methodToExecute ?? throw new ArgumentNullException(nameof(methodToExecute), $"{nameof(methodToExecute)} {Resources.IsRequired}");
	}

	public RelayCommand(Action methodToExecute, Func<bool> canExecuteEvaluator)
		: this(methodToExecute)
	{
		_canExecute = canExecuteEvaluator ?? throw new ArgumentNullException(nameof(canExecuteEvaluator), $"{nameof(canExecuteEvaluator)} {Resources.IsRequired}");
	}

	public bool CanExecute(object parameter)
	{
		return _canExecute == null ? true : _canExecute.Invoke();
	}

	[DebuggerStepThrough]
	public void Execute(object parameter)
	{
		_execute();
	}
}
```

```cs
public class RelayCommand<T> : ICommand
{
	private readonly Action<T> _execute = null;
	private readonly Predicate<T> _canExecute = null;

	public RelayCommand(Action<T> execute)
	{
		_execute = execute ?? throw new ArgumentNullException(nameof(execute), $"{nameof(execute)} {Resources.IsRequired}");
	}

	public RelayCommand(Action<T> execute, Predicate<T> canExecute)
		: this(execute)
	{
		_canExecute = canExecute ?? throw new ArgumentNullException(nameof(canExecute), $"{nameof(canExecute)} {Resources.IsRequired}");
	}

	public bool CanExecute(object parameter)
	{
		if (_canExecute == null)
			return true;

		if (parameter == null && typeof(T).IsValueType)
			return _canExecute.Invoke(default(T));

		if (parameter == null || parameter is T)
			return (_canExecute.Invoke((T)parameter));

		return false;
	}

	public event EventHandler CanExecuteChanged
	{
		add { CommandManager.RequerySuggested += value; }
		remove { CommandManager.RequerySuggested -= value; }
	}

	[DebuggerStepThrough]
	public void Execute(object parameter)
	{
		_execute((T)parameter);
	}
}
```

## Value Converters
Often your ViewModels exposes types that the View does not know how to interpret. Instead of modifying your ViewModel to expose View-friendly properties, Value Converters extend the View to become compatible with the ViewModel. One common built-in WPF converter is `BooleanToVisibilityConverter` which converts a `bool` type exposed by the VM to the `Visibility` enum that View controls understand.

## Event Aggregator
Most MVVM Frameworks will have an `IEventAggregator` implementation with a subscribe/publish event model to help VMs communicate one-way messages between each other. This is particularly valuable in deeply nested hierarchies to cut across layers where VMs may not easily reference one another.

## Window Manager
Another common MVVM Framework feature is a Window Manager. This component helps allows your ViewModel code to show dialogs and windows without explicitly referencing the View.

# References
[Xamarin.Forms MVVM](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/mvvm)
[RelayCommand](https://github.com/Insire/Maple/blob/master/src/Maple.Core/Commands/RelayCommand.cs) implementation from [Maple](https://github.com/Insire/Maple)