---
title: Overview of API Versioning in ASP.NET Core 3.0+
subtitle: Describing the most common API versioning methods using Microsoft.AspNetCore.Mvc.Versioning package functionality
tags: [WEB-API, Versioning, ENF]
categories: [WEB-API, ASP.NET Core]
---
### Overview of API Versioning in ASP.NET Core 3.0+ ###

Very often, API versioning is something that is being thought of only when there is a need for changing our API. However, we shouldn't wait until we need such a thing before we implement it. Rather, we should have a versioning strategy, that we will follow while building our API endpoints, right from the beginning of API development.

When we expose our API for use, it is assumed that we have clearly defined the contracts and that the consumers of our API can rely on these contracts. If we make some changes to our API contracts, these should be exposed as a new version of our API. This new version does not mean a new code base of the API. It should be the same code base that supports different versions of an API.

{% asset_img "reindeers-on-the-road.jpg" "One code base - multiple versions" %}

There are many approaches we can use to implement API versioning. In general, there isn't a good or a bad way when talking about API versioning. Which approach to choose depends on who is using our API, on our overall product development situation (are we starting a new project, or do we need to incorporate versioning to a mature, production API), and on how detailed we want to be with API versioning. Find an option that best suits your needs, and stick with it when building your API endpoints.

### Microsoft.AspNetCore.Mvc.Versioning ###

Microsoft has a package that offers a set of libraries which adds service API versioning in ASP.NET Core: [Microsoft.AspNetCore.Mvc.Versioning](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Versioning)

In this post, we will describe the most common use cases. For detailed rules and guidelines on API versioning, take a look at [Microsoft's official wiki pages](https://github.com/microsoft/aspnet-api-versioning/wiki) and [Microsoft REST API Guidelines for versioning](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md#12-versioning).

>If you are familiar with basic versioning concepts, or just want to go straight to the code, I have created a simple example that covers all of the API versioning methods described in this post. This project can be downloaded/cloned from a [GitHub repository](https://github.com/vladimirvozar/ApiVersioning).

The first step with API versioning is to install Microsoft's versioning package. From package manager console run following command:
```
	Install-Package Microsoft.AspNetCore.Mvc.Versioning
```
API versioning is achieved by setting versioning configuration in the `ConfigureServices` method of the `Startup.cs` file, and by annotating controllers and actions with appropriate attributes.

To activate API versioning, simply add `services.AddApiVersioning();` to the `ConfigureServices` method. With this line added, we have activated default API versioning settings, and by default, when calling our API, our clients MUST specify a version in their requests. Otherwise, our API will respond with a *BadRequest* and *ApiVersionUnspecified* error. It is a pretty strict rule to require explicit stating of an API version in every request. To make life a bit easier for the users of our API, we need to set a default API version to be used when it is not explicitly stated in the request. 

So, a more common versioning configuration would be:
```cs
	services.AddApiVersioning(options => {
			options.DefaultApiVersion = new ApiVersion(1, 0);
			options.AssumeDefaultVersionWhenUnspecified = true;
			options.ReportApiVersions = true;
		});
```
Let us explain what each of the above versioning settings implies:

- `options.DefaultApiVersion = new ApiVersion(1, 0);` - This setting is not mandatory, because by default it is assumed that the initial version is set to 1.0. But, it is a good practice to state it explicitly. With `DefaultApiVersion` being set, all controllers that do not have an API version attribute (`[ApiVersion("1.0")]`) applied on them, will implicitly bound to this default API version. However, for easier understanding, it is recommended to explicitly apply API version attributes on our controllers.

- `options.AssumeDefaultVersionWhenUnspecified = true;` - `DefaultApiVersion` just tells us the number of default API version, but we still need to set `AssumeDefaultVersionWhenUnspecified` to true for our default API version to be *activated*. Without this setting, any clients that do not specify an API version in their requests would get a *BadRequest* response with an *ApiVersionUnspecified* error stating that *An API version is required, but was not specified*.

- `options.ReportApiVersions = true;` - Reporting API versions is disabled by default. With enabling this option, responses from our API endpoints will carry a header telling our clients which versions are supported or deprecated (`api-supported-versions: 1.1, 2.0, api-deprecated-versions: 1.0`).

### Types of API versioning ###

{% asset_img "four-raindeers.jpg" "Four different API versioning methods" %}

There are four out-of-the-box supported approaches for versioning a service:

1. by query string parameter
2. by HTTP header
3. by URL path segment
4. by media type parameter

The default method is to use a query string parameter named '**api-version**'. We can also combine API versioning methods or define our custom method of API versioning.

### API Version Reader ###

API Version Reader defines how an API version is read from the HTTP request. If not explicitly configured, the default setting is that our API version will be a query string parameter named '**api-version**' (example: *../users?api-version=2.0* ). Another, probably more popular option is to store the API version in the HTTP header. We have also the possibility of having an API version both in a query string as well as in an HTTP header.

The next common versioning method is via the URL path. Version information will be one segment in the URL path of the endpoint. This means that our customers are always explicitly asking for a certain version (by going to that specific URL). In many cases, this is not appropriate, because it is not possible to have a default API version for a URL path segment, and there is no way to configure implicit matching to a certain version.

So, depending on which option we want, our configuration would be:

1. for query string parameter
```cs
	services.AddApiVersioning(options => 
		options.ApiVersionReader = new QueryStringApiVersionReader("v"));
```

2. for HTTP header
```cs
	services.AddApiVersioning(options => 
		options.ApiVersionReader = new HeaderApiVersionReader("api-version"));
```

3. API Version Reader Composition
```cs
	services.AddApiVersioning(options => {
		options.ApiVersionReader = ApiVersionReader.Combine(
						new QueryStringApiVersionReader("v"),
						new HeaderApiVersionReader("v"));});
```

4. versioning via the URL Path
```cs
	services.AddApiVersioning(options => options.ApiVersionReader = 
						new UrlSegmentApiVersionReader());
```

We can change the parameter name that represents the version (like in the query string method above, where we have used the letter '**v**' instead of default '**api-version**').

Microsoft's versioning library also supports some other (custom) versioning methods like versioning with *Accept Header*, versioning with *Content-Type Header*, versioning with *Custom Media Types*, versioning by namespaces, versioning by writing our own controller/action resolver. For versioning by *Media Type*, you can take a look at an example described on [Microsoft's official wiki page](https://github.com/microsoft/aspnet-api-versioning/wiki/Versioning-by-Media-Type) for this type of versioning. A nice example of more specific versioning, using the *Accept Header*, can be found on [this blog post](https://fearofoblivion.com/versioning-asp-net-core-api-using-header). These versioning strategies are more specific to implement, and their implementation differs from API to API. Anyway, the most common API versioning methods are with HTTP Header and/or with a  query-string parameter, as well as versioning by URL path segment.

### Adding versioning informations to controllers and methods ###

{% asset_img "reindeer.jpg" "Let us decorate our controllers" %}

After choosing a versioning strategy, and configuring it in the *ConfigureServices* method, we can start versioning our API endpoints. Microsofts versioning package provides several attributes that we can apply to our controllers and methods. 

When configuring versioning on controllers and methods, these are the rules to follow:

- The initial version of a controller may not have any API version attribution and will implicitly become the configured default API version. The default configuration uses the value 1.0.

- Annotating our controller with, for example, `[ApiVersion("1.0")]` attribute, means that this controller supports API version 1.0

- Controllers can support multiple API versions. Simply, apply multiple `[ApiVersion(...)]` attributes on the controller

- To differentiate between multiple versions supported by our controller, we annotate controller methods with `[MapToApiVersion(...)]` attribute. This approach is useful for small version differences (example: `[MapToApiVersion("1.0")]`).

- If we are versioning our API with URL path segment, beside `[ApiVersion(...)]` attributes we also need to set the URL segment of the route where the API version will be read from:
```cs
[Route("api/v{version:apiVersion}/[controller]")]
```
- To deprecate some version in our API controller, we need to set *Deprecated* flag to true: ```[ApiVersion("1.0", Deprecated = true)]```.

- It is a good practice to explicitly version all our services. However, if we have a service that is version-neutral, we will mark that controller with ```[ApiVersionNeutral]``` attribute.

- All of the service API version informations are accessible via extension methods. Beginning in the ASP.NET Core 3.0 version, Model Binding is also supported. So, in our controller methods, we can get the requested API version by calling: 
```cs
var apiVersion = HttpContext.GetRequestedApiVersion();
```
- And, for getting an API version with model binder, our method would look like:
```cs
// supported in 3.0+
public IActionResult Get( int id, ApiVersion apiVersion ) { ... }
```
	
### API Version Conventions ###

{% asset_img "reindeer-fight.jpg" "Attributes vs Conventions" %}

Besides attributing our controllers and methods, another way to configure service versioning is with API versioning conventions. There are several reasons why would we use API versioning conventions instead of attributes: 

- Centralized management and application of all service API versions
- Apply API versions to services defined by controllers in external .NET assemblies
- Dynamically apply API versions from external sources; for example, from the configuration

Example of setting API version to the controller, analog to applying `[ApiVersion("1.0")]` attribute:

```cs
	services.AddApiVersioning( options =>
	{
		options.Conventions.Controller<MyController>().HasApiVersion(1, 0);
	});
```

Example of setting deprecated API version as well as versioning controller actions:
```cs
	services.AddApiVersioning( options =>
	{
		options.Conventions.Controller<MyController>()	   
			    .HasDeprecatedApiVersion(1, 0)
                .HasApiVersion(1, 1)
                .HasApiVersion(2, 0)
                .Action(c => c.Get1_0()).MapToApiVersion(1, 0)
                .Action(c => c.Get1_1()).MapToApiVersion(1, 1)
                .Action(c => c.Get2_0()).MapToApiVersion(2, 0);
	});
```

It is possible to use both API version conventions together with versioning attributes at the same time.

There is also an option to define custom conventions. Beginning in .NET Core 3.0, there is a `IControllerConvention` interface for this purpose. Custom conventions are added to the convention builder through the API versioning options:
```cs
	options.Conventions.Add(new MyCustomConvention());
```

Lastly, we can version our API's by namespace convention they reside in:
```cs
	options.Conventions.Add(new VersionByNamespaceConvention());
```

The defined namespace name must conform to the API version format so that it can be parsed. 

For example, namespacing our controllers like on the following folder structure:

- api/v1/UsersController
- api/v2/UsersController
- api/v2_1/UsersController
	
would map to the following URL paths:

	- api/1.0/users
	- api/2.0/users
	- api/2.1/users

This was a brief overview of the API versioning in the ASP.NET Core 3.0+. Well configured API versioning can be really useful, both for the users and API developers, and there shouldn't be a reason why not to version every API. If you have read this far, I hope you are more confident about choosing an appropriate way to go with versioning your API.

{% asset_img "reindeer-looks.jpg" "What is your opinion on this matter?" %}

Have you used API Versioning in your ASP.NET Core projects? Was it useful, and did it save you from problems you might've seen otherwise? Let me know, and share it in the comments!
