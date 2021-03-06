
## AspNetCore.Identity.DocumentDB [![AppVeyor build status](https://ci.appveyor.com/api/projects/status/b27a4wvconad0c5k?svg=true)](https://ci.appveyor.com/project/FelschR/aspnetcore-identity-documentdb) [![Travis CI build status](https://travis-ci.org/FelschR/AspNetCore.Identity.DocumentDB.svg?branch=master)](https://travis-ci.org/FelschR/AspNetCore.Identity.DocumentDB?branch=master)

This is a DocumentDB provider for the ASP.NET Core Identity framework. This was ported from the MongoDB ASP.NET Core Identity framework (Microsoft.AspNetCore.Identity.MongoDB NuGet package) by g0t4.

It's fully compatible with ASP.NET Core 1.0 & 2.0 and can also be used within standard .NET Framework projects.

## Usage

Add this package to your project via NuGet:
```
dotnet add package AspNetCore.Identity.DocumentDB
```

Register the Identity service with DocumentDB stores. Usually in the ConfigureServices method within your Startup.cs:

```csharp
services.AddIdentityWithDocumentDBStores(documentClient, databaseLink);
```
No need to call `services.AddIdentity()` before; this is already included in the code above.

This framework provides you with `IdentityUser` and `IdentityRole` classes that you need to use. You can create your own inherited classes if you like (see [Inheriting section](#inheriting-identityuser-and-identityrole) below). 

Now you can go ahead and inject the `UserManager` and/or `RoleManager` provided by the Identity framework into your ASP.NET Controllers as you would normally do:

```csharp
public YourController(UserManager<IdentityUser> userManager)
{
	// use the userManager instance as you desire:
	userManager.CreateAsync(new IdentityUser(), "password123"); 
}
```

### Alternative service registration

There are a few different ways to register the DocumentDB stores that allow for more customization:

Register DocumentDB Stores separately from the AddIdentity() call:
```csharp
services.AddIdentity<IdentityUser, IdentityRole>()
		.RegisterDocumentDBStores<IdentityUser, IdentityRole>(documentClient, databaseLink);
```
If you do this make sure the provided `IdentityUser` and `IdentityRole` classes are matching.

Instead of providing a DocumentClient & database link you can also use DocumentDbOptions and optionally IdentityOptions to allow for as much customization as possible:
```csharp
services.AddIdentityWithDocumentDBStores<IdentityUser, IdentityRole>(
	dbOptions => {
		dbOptions.DocumentUrl = "...";
		dbOptions.DocumentKey = "...";
		dbOptions.CollectionId = "...";
	},
	identityOptions => {
		identityOptions.User.RequireUniqueEmail = true;
	});
```
The `RegisterDocumentDBStores` also allows setting DocumentDbOptions. The IdentityOptions cannot be set there for obvious reasons (hopefully).

### Inheriting IdentityUser and IdentityRole

If you want to use customized classes for `UserManager` and `RoleManager` you need to reference them during the DocumentDB stores registration:
```csharp
services.AddIdentityWithDocumentDBStores<CustomUser, CustomRole>(documentClient, databaseLink);
```
All methods provided by this library can be used with custom classes.

## Sample projects
Have a look into the `samples` folder in the repository.

## Information

What frameworks are targeted, with rationale:

- Microsoft.AspNetCore.Identity - supports net451 and netstandard1.3
- Microsoft.Azure.DocumentDB v1.10.0 - supports net45
- Microsoft.Azure.DocumentDB v1.21.0 - supports netstandard1.6
- Thus, the lowest common denominators are net451 and netstandard1.6

For support for the now deprecated `Microsoft.Azure.DocumentDB.Core` package you can still use v1.0.0.

Additionally this projects targets netstandard2.0 explicitly due to its incompaibility with older versions of Microsoft.AspNetCore.Identity.
For netstandard2.0 Microsoft.AspNetCore.Identity v2.0 is required.

## Building instructions

run commands in [](build.sh)
