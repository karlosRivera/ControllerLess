#ControllerLess ASP.NET MVC [![Build Status](https://api.travis-ci.org/brentj73/ControllerLess.svg)](https://travis-ci.org/brentj73/ControllerLess)

Using the default MVC HTTP handler means that regardless of whether you actually need server-side code 
for rendering each view, you must have a corresponding controller.

In many cases these controllers have a single action (e.g. Index) which simply returns the view. This 
seems inefficient and adds to the maintenance cost of the application.

So how can we get around this? Wouldn't it be great if we could just have a single re-usable controller 
with a single action instead of creating multiple copies of the same thing? The good news is that it's easy with ControllerLess!

And if you need a controller for a specific view or action, you can still add it in the normal way and it will work as expected.

Configuration
=============

Once installed, simply update your route config to use the ControllerLessRouteHandler.

```C#
using Anterec.ControllerLess.Mvc;

public class RouteConfig
{
	public static void RegisterRoutes(RouteCollection routes)
	{
		routes.IgnoreRoute("{resource}.axd/{*pathInfo}");

		var route = new Route(
			"{controller}/{action}/{id}",
			new RouteValueDictionary(new { controller = "Home", action = "Index", id = UrlParameter.Optional }),
			new ControllerLessRouteHandler());

		routes.Add(route);
	}
}
```

The ControllerLess plug-in can be configured to work with ASP.NET MVC areas.

```C#
using System.Web.Routing;
using Anterec.ControllerLess.Mvc;

public class CustomAreaRegistration : AreaRegistration 
{
	public override string AreaName 
	{
		get 
		{
			return "Custom";
		}
	}

	public override void RegisterArea(AreaRegistrationContext context) 
	{
		var route = new Route(
			"Custom/{controller}/{action}/{id}",
			new RouteValueDictionary(new { area = "Custom", controller = "Home", action = "Index", id = UrlParameter.Optional }),
			new ControllerLessRouteHandler());

		context.Routes.Add(route);
	}
}
```

It is also possible to create controllers with or without actions for specific views.

Advanced Configuration (optional)
=================================

You can configure ControllerLess to redirect requests to your own controllers and actions on a URL by URL basis in the Web.config file.

```XML
<configuration>
  <configSections>
	<section name="controllerLessSettings" type="Anterec.ControllerLess.Configuration.RouteConfiguration, Anterec.ControllerLess"/>
  </configSections>
  ...
  <controllerLessSettings>
    <routes>
      <clear />
      <add url="/Hello/Index" controller="Door" action="Enter"/>
      <add url="/Hello/Cutiepie" controller="Panic" action="Run"/>
      ...
    </routes>
  </controllerLessSettings>
</configuration>
```

You can also configure your own controller and action to receive all requests, replacing the ControllerLess default behaviour.

```XML
<configuration>
  <configSections>
	<section name="controllerLessSettings" type="Anterec.ControllerLess.Configuration.RouteConfiguration, Anterec.ControllerLess"/>
  </configSections>
  ...
  <controllerLessSettings defaultController="Default" defaultAction="Show">
    <routes>
      ...
    </routes>
  </controllerLessSettings>
</configuration>
```

In this example any view that doesn't have a controller will be routed to the "Show" action on the "Default" controller.
