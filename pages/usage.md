---
layout: default
title: Installing and Using FeatureToggle
---

# Installation

FeatureToggle can be installed via NuGet.


	PM> Install-Package FeatureToggle


	PM> Install-Package FeatureToggle.WPFExtensions


This will also install FeatureToggle.Core which is a PCL package.


## Getting Started

###1 Create a (strongly typed) toggle

Create a new class for each feature you want to toggle in your application.

This class needs to inherit from one of the FeatureToggle base classes (see list below).

This means feature toggling in the application can be dealt with in a strongly typed way.


	public class MyAwesomeFeature : SimpleFeatureToggle {}

###2 Configure the Toggle

The Toggle needs to get its enabled/disabled state from somewhere (unless you are using the `AlwaysOffFeatureToggle` or `AlwaysOnFeatureToggle`).

For Windows desktop applications and ASP.Net, the default place is in the app.config/web.config.


	<appSettings>
	   <add key="FeatureToggle.MyAwesomeFeature" value="false" />
	</appSettings>


For Windows Phone and Windows Store apps, configuration is via entries in the app.xaml resources.

	<Application
	    x:Class="ExampleWindows8StoreApp.App"
	    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
	    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
	    xmlns:local="using:ExampleWindows8StoreApp">
	<Application.Resources>
	        <x:String x:Key="FeatureToggle.MyAwesomeFeature">true</x:String>        
	</Application.Resources>
	</Application>

In both cases, notice the "FeatureToggle." prefix that is expected before the name of your Toggle (which is the name of your Toggle class).

###3 Use the toggle in code:

	var myAwesomeFeature = new MyAwesomeFeature ();
	
	if (!myAwesomeFeature.FeatureEnabled)  
	{
	   // code to disable stuff (e.g. UI buttons, etc)
	}

Or in XAML (if you've installed the FeatureToggle.WPFExtensions package):


	<Button Visibility="{Binding Path=MyAwesomeFeature, Converter={StaticResource toggleVisibilityConverter} }">My Awesome Feature</Button>


##Bundled Toggles

There are a number of Toggles that come bundled with FeatureToggle

* AlwaysOffFeatureToggle
* AlwaysOnFeatureToggle
* EnabledOnOrAfterDateFeatureToggle
* EnabledOnOrBeforeDateFeatureToggle
* EnabledBetweenDatesFeatureToggle
* SimpleFeatureToggle
* RandomFeatureToggle (from v2.1)
* EnabledOnDaysOfWeekFeatureToggle (from v2.1)
* SqlFeatureToggle (*not available in Windows Phone or Windows Store projects*)
* EnabledOnOrAfterAssemblyVersionWhereToggleIsDefinedToggle (from v3.0)

You can also [customize FeatureToggle]({{ site.url }}/FeatureToggle.Docs/pages/extensibility.html) in a number of ways.

There are also a number of decorators to wrap toggles and add additional features:

* CompositeAndDecorator - true if all "child" toggles are enabled
* DefaultToDisabledOnErrorDecorator - If an error occurs evaluating the toggle value, just default to disabled (use with caution)
* DefaultToEnabledOnErrorDecorator - If an error occurs evaluating the toggle value, just default to enabled (use with caution)
* FixedTimeCacheDecorator - Caches he underlying toggle value for expensive toggle lookups (sql server, remote http, etc)



## Using the Bundled Toggles

### SimpleFeatureToggle

Create a Toggle for the feature you want to control by inheriting from `SimpleFeatureToggle` - there's no need to override anything:

	private class SaveToPdfFeatureToggle : SimpleFeatureToggle { }

Add an entry to your AppSettings or Application.Resources to control the value of the `SaveToPdfFeatureToggle`:


	<appSettings>
	   <add key="FeatureToggle.SaveToPdfFeatureToggle" value="true"/>
	</appSettings>

	<Application.Resources>
	        <x:String x:Key="FeatureToggle.SaveToPdfFeatureToggle">true</x:String>        
	</Application.Resources>


**Again, note the convention FeatureToggle.class name**

Now you can use the Toggle in code:

	var savePdfFeature= new SaveToPdfFeatureToggle();
	 
	if (savePdfFeature.FeatureEnabled)
	   ShowSavePdfButton();
	else
	   HideSavePdfButton();

To disable the Toggle, just set the configured value to false.

### AlwaysOffFeatureToggle and AlwaysOnFeatureToggle

These toggle are not configurable they are either always on or off.

Create a Toggle for the feature you want to control by inheriting from `AlwaysOffFeatureToggle` or `AlwaysOnFeatureToggle`.


	private class SaveToPdfFeatureToggle : AlwaysOffFeatureToggle { }

Now you can use the Toggle in code (or XAML binding):

	var savePdfFeature= new SaveToPdfFeatureToggle();
	 
	if (savePdfFeature.FeatureEnabled)
	   ShowSavePdfButton();
	else
	   HideSavePdfButton();

### EnabledOnOrAfterDateFeatureToggle and EnabledOnOrBeforeDateFeatureToggle

Create a Toggle for the feature you want to control by inheriting from `EnabledOnOrAfterDateFeatureToggle` or `EnabledOnOrBeforeDateFeatureToggle`.


	private class SaveToPdfFeatureToggle : EnabledOnOrAfterDateFeatureToggle { }

Add an entry to your AppSettings or Application.Resources:


	<appSettings>
	   <add key="FeatureToggle.SaveToPdfFeatureToggle" value="01-Jan-2000 18:00:00"/>
	</appSettings>

	<Application.Resources>
	        <x:String x:Key="FeatureToggle.SaveToPdfFeatureToggle">01-Jan-2000 18:00:00</x:String>        
	</Application.Resources>


Notice the expected datetime format: **dd-MMM-yyyy HH:mm:ss**

Then use in the same way as other toggles.


### EnabledBetweenDatesFeatureToggle

Create a Toggle for the feature you want to control by inheriting from `EnabledBetweenDatesFeatureToggle`.


	private class SaveToPdfFeatureToggle : EnabledBetweenDatesFeatureToggle { }

Add an entry to your config int he format (start date | end date)

	<appSettings>
	   <add key="FeatureToggle.SaveToPdfFeatureToggle " value="01-Jan-2000 00:00:00 | 01-Feb-2000 00:00:00"/>
	</appSettings>

	<Application.Resources>
	        <x:String x:Key="FeatureToggle.SaveToPdfFeatureToggle">01-Jan-2000 00:00:00 | 01-Feb-2000 00:00:00</x:String>        
	</Application.Resources>

Again notice the expected datetime format: **dd-MMM-yyyy HH:mm:ss**


### SqlFeatureToggle

This toggle is not available on Windows Phone or Windows Store apps.

This Toggle gets it's value from a Sql Server database. You can use to enable a Feature from a single point when the Feature spans a number of applications, services, layers, etc.

Create a Toggle for the feature you want to control by inheriting from `SqlFeatureToggle`:


	private class SaveToPdfFeatureToggle : SqlFeatureToggle{ }


Add an entries to your AppSettings section for the Sql Server database connection string and a sql statement that returns either "true" or "false" from a SQL Server `bit` field.


	<add key="FeatureToggle.SaveToPdfFeatureToggle.ConnectionString" value="Data Source=.\SQLEXPRESS;Initial Catalog=FeatureToggleDatabase;Integrated Security=True;Pooling=False"/>
	<add key="FeatureToggle.SaveToPdfFeatureToggle.SqlStatement" value="select Value from Toggle where ToggleName = 'SaveToPdfFeatureToggle'"/>

Alternatively as from v3.1 the connection string can be specified in the <connectionStrings> section without the postfixed ".ConnectionString" as shown below:

	<connectionStrings>    
		<add name="FeatureToggle.MySqlServerToggleFalse" connectionString="Data Source=.\SQLEXPRESS;Initial Catalog=FeatureToggleIntegrationTests;Integrated Security=True;Pooling=False" />    
	</connectionStrings>	
	
### RandomFeatureToggle

This toggle requires no configuration, it will randomly enable or disable a feature.

### EnabledOnDaysOfWeekFeatureToggle

This toggle will be enabled on specified days of the week.

Configuration for this toggle requires week days to be specified in long format, e.g.

	<add key="FeatureToggle.MondayAndFridayToggle" value="Monday, Friday"/>
	
or in XAML:

	<system:String  x:Key="FeatureToggle.FridayImInLoveFeature">Friday</system:String>

### BooleanRavenDBFeatureToggle

This toggle is not available on Windows Phone or Windows Store apps.

This toggle get it's value from a RavenDB database. 

Create a Toggle for the feature you want to control by inheriting from `BooleanRavenDBFeatureToggle`:


	private class SaveToPdfFeatureToggle : BooleanRavenDBFeatureToggle{ }

Add RavenDB connection string to app/web config:

	<connectionStrings>
		<add name="FeatureToggle.SaveToPdfFeatureToggle" connectionString="Url = http://localhost:8080;database=FeatureToggleInterationTests"/>    
	</connectionStrings>

The database should contain a collection called "BooleanToggleSettings" with a document for every toggle that contains a property "Enabled":

	{
	  "Enabled": true
	}

Check out the [test setup](https://github.com/jason-roberts/FeatureToggle/blob/master/src/Tests/FeatureToggle.RavenDB.Integration.Tests/RavenDBProviderShould.cs#L71) to see an example of writing some toggle config entries.

### HttpJsonFeatureToggle

This toggle gets it's enabled state from a JSON HTTP endpoint.

Configuration should be the url where the JSON configuration lives, e.g:

	<add key="FeatureToggle.HttpJsonTrueToggle" value="http://localhost:8084/api/test/getenabled" />

As with all the toggles, if url is not available then an exception will be raised, rather than assuming either enabled or disabled.

The expected JSON that should be returned is in the form:

	{"enabled": true}

or

	{"enabled": false}

## XAML Binding - WPF, Windows Phone and Windows Store

###Binding the Visibility of a UI Element to a Toggle

Create a Toggle as described above.

For a WPF application install the FeatureToggle.WFPExtensions NuGet package.

Windows Phone and Windows Store have it build in to the NuGet package.

In your XAML declare a converter:

	<Wpf:FeatureToggleToVisibilityConverter x:Key="toggleVisibilityConverter"/>

Create a property in your view-model or code-behind:


	public IFeatureToggle SaveToPdfFeature
	{
	   get 
	   {
	      return _saveToPdfFeature;
	   }
	   set
	   {
	      _saveToPdfFeature = value;
	      // Do INotifyPropertyChanged stuff here if required
	   }
	}

Now bind your UI elements visibility:

	<Button Visibility="{Binding Path=SaveToPdfFeature, Converter={StaticResource toggleVisibilityConverter} }">Save To PDF</Button>

