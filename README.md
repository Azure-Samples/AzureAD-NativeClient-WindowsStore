NativeClient-WindowsStore
=========================

This sample demonstrates a Windows Store application calling a web API that is secured using Azure AD.  The Windows Store application uses the Active Directory Authentication Library (ADAL) to obtain a JWT access token through the OAuth 2.0 protocol.  The access token is sent to the web API to authenticate the user.

For more information about how the protocols work in this scenario and other scenarios, see [Authentication Scenarios for Azure AD](http://go.microsoft.com/fwlink/?LinkId=394414).

## How To Run This Sample

To run this sample you will need:
- Visual Studio 2013
- Windows 8 or higher
- An Internet connection
- An Azure subscription (a free trial is sufficient)
- A Microsoft account

Every Azure subscription has an associated Azure Active Directory tenant.  If you don't already have an Azure subscription, you can get a free subscription by signing up at [http://wwww.windowsazure.com](http://www.windowsazure.com).  All of the Azure AD features used by this sample are available free of charge.

### Step 1:  Clone or download this repository

From your shell or command line:

`git clone git@github.com:WindowsAzureADSamples/NativeClient-WindowsStore.git`

### Step 2:  Create a user account in your Azure Active Directory tenant

If you already have a user account in your Azure Active Directory tenant, you can skip to the next step.  This sample will not work with a Microsoft account, so if you signed in to the Azure portal with a Microsoft account and have never created a user account in your directory before, you need to do that now.  If you create an account and want to use it to sign-in to the Azure portal, don't forget to add the user account as a co-administrator of your Azure subscription.

### Step 3:  Register the sample with your Azure Active Directory tenant

There are two projects in this sample.  Each needs to be separately registered in your Azure AD tenant.

#### Register the TodoListService web API

1. Sign in to the [Azure management portal](https://manage.windowsazure.com).
2. Click on Active Directory in the left hand nav.
3. Click the directory tenant where you wish to register the sample application.
4. Click the Applications tab.
5. In the drawer, click Add.
6. Click "Add an application my organization is developing".
7. Enter a friendly name for the application, for example "TodoListService", select "Web Application and/or Web API", and click next.
8. For the sign-on URL, enter the base URL for the sample, which is by default `https://localhost:44321`.
9. For the App ID URI, enter `https://<your_tenant_name>/TodoListService`, replacing `<your_tenant_name>` with the name of your Azure AD tenant.  Click OK to complete the registration.
10. While still in the Azure portal, click the Configure tab of your application.
11. Find the Client ID value and copy it aside, you will need this later when configuring your application.
12. Using the Manage Manifest button in the drawer, download the manifest file for the application.
13. Add a permission to the application by replacing the appPermissions section with the block of JSON below.  You will need to create a new GUID and replace the example permissionId GUID.
14. Using the Manage Manfiest button, upload the updated manifest file.  Save the configuration of the app.

```JSON
"appPermissions": [
{
	"claimValue": "user_impersonation",
	"description": "Allow full access to the To Do List service on behalf of the signed-in user",
	"directAccessGrantTypes": [],
	"displayName": "Have full access to the To Do List service",
	"impersonationAccessGrantTypes": [
		{
			"impersonated": "User",
		    "impersonator": "Application"
		}
	],
	"isDisabled": false,
	"origin": "Application",
	"permissionId": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
	"resourceScopeType": "Personal",
	"userConsentDescription": "Allow full access to the To Do service on your behalf",
	"userConsentDisplayName": "Have full access to the To Do service"
	}
],
```

#### Find the TodoListClient app's redirect URI

Before you can register the TodoListClient application in the Azure portal, you need to find out the application's redirect URI.  Windows 8 provides each application with a unique URI and ensures that messages sent to that URI are only sent to that application.  To determine the redirect URI for your project:

1. Open the solution in Visual Studio 2013.
2. In the TodoListClient project, open the `MainPage.xaml.cs` file.
3. Find this line of code and set a breakpoint on it.

```C#
Uri redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```

4. Right-click on the TodoListClient project and Debug --> Start New Instance.
5. When the breakpoint is hit, use the debugger to determine the value of redirectURI, and copy it aside for the next step.
6. Stop debugging, and clear the breakpoint.

The redirectURI value will look something like this:

```
ms-app://s-1-15-2-2123189467-1366327299-2057240504-936110431-2588729968-1454536261-950042884/
```

#### Register the TodoListClient app

1. Sign in to the [Azure management portal](https://manage.windowsazure.com).
2. Click on Active Directory in the left hand nav.
3. Click the directory tenant where you wish to register the sample application.
4. Click the Applications tab.
5. In the drawer, click Add.
6. Click "Add an application my organization is developing".
7. Enter a friendly name for the application, for example "TodoListClient-WindowsStore", select "Native Client Application", and click next.
8. Enter the Redirect URI value that you obtained during the previous step.  Click finish.
9. Click the Configure tab of the application.
10. Find the Client ID value and copy it aside, you will need this later when configuring your application.
11. In "Permissions to Other Applications", select the TodoListService, and request the delegated permission "Have full access to the To Do List service".  Save the configuration.

### Step 4:  Configure the sample to use your Azure AD tenant

#### Configure the TodoListService project

1. Open the solution in Visual Studio 2013.
2. Open the `web.config` file.
3. Find the app key `ida:Tenant` and replace the value with your AAD tenant name.
4. Find the app key `ida:Audience` and replace the value with the App ID URI you registered earlier, for example `https://<your_tenant_name>/TodoListService`.
5. Find the app key `ida:ClientId` and replace the value with the Client ID for the TodoListService from the Azure portal.

#### Configure the TodoListClient project

1. Open `MainPage.xaml.cs'.
2. Find the declaration of `tenant` and replace the value with the name of your Azure AD tenant.
3. Find the declaration of `clientId` and replace the value with the Client ID from the Azure portal.
4. Find the declaration of `todoListResourceId` and `todoListBaseAddress` and ensure their values are set properly for your TodoListService project.

### Step 5:  Trust the IIS Express SSL certificate

Since the web API is SSL protected, the client of the API (the web app) will refuse the SSL connection to the web API unless it trusts the API's SSL certificate.

Coming soon.

### Step 6:  Run the sample

You know what to do!  Explore the sample by signing in, adding items to the To Do list, removing the user account, and starting again.  Notice that if you stop the application without removing the user account, the next time you run the application you won't be prompted to sign-in again - that is because ADAL has a persistent cache, and remembers the tokens from the previous run.

## How To Deploy This Sample to Azure

Coming soon.

## About The Code

Coming soon.

## How To Recreate This Sample

First, in Visual Studio 2013 create an empty solution to host the  projects.  Then, follow these steps to create each project.

### Creating the TodoListService Project

1. In the solution, create a new ASP.Net MVC web API project called TodoListService and while creating the project, click the Change Authentication button, select Organizational Accounts, Cloud - Single Organization, enter the name of your Azure AD tenant, and set the Access Level to Single Sign On.  You will be prompted to sign-in to your Azure AD tenant.  NOTE:  You must sign-in with a user that is in the tenant; you cannot, during this step, sign-in with a Microsoft account.
2. In the `Models` folder add a new class called `TodoItem.cs`.  Copy the implementation of TodoItem from this sample into the class.
3. Add a new, empty, Web API 2 controller called `TodoListController`.
4. Copy the implementation of the TodoListController from this sample into the controller.  Don't forget to add the `[Authorize]` attribute to the class.
5. In `TodoListController` resolving missing references by adding `using` statements for `System.Collections.Concurrent`, `TodoListService.Models`, `System.Security.Claims`.

### Creating the TodoListClient Project

1. In the solution, create a new project for a Windows Store --> Blank App (XAML) called TodoListClient.
2. Add the pre-release Active Directory Authentication Library (ADAL) NuGet, Microsoft.IdentityModel.Clients.ActiveDirectory, version 2.6.0-alpha (or higher), to the project.
3. Open `Package.appxmanifest`, click the Capabilities tab, and enable the following four capabilities: Enterprise Authentication, Internet (Client), Private Networks (Client & Server), Shared User Certificates
4. Copy the markup from `MainPage.xaml` in the sample project to `MainPage.xaml` in the new project.
5. Copy the code from `MainPage.xaml.cs` in the sample project to `MainPage.xaml.cs` in the new project.

Finally, in the properties of the solution itself, set both projects as startup projects.