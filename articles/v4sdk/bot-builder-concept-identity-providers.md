---
title: Identity providers Azure Bot Service - Bot Service
description: Learn about identity providers in the Azure Bot Service.
keywords: azure bot service, authentication, bot framework token service
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/11/2020
monikerRange: 'azure-bot-service-4.0'
---

# Identity providers

An identity provider authenticates user or client identities and issues consumable security tokens. It provides user authentication as a service.

Client applications, such as web applications, delegate authentication to a trusted identity provider. Such client applications are said to be federated, that is, they use federated identity.

Using a trusted identity provider:

- Enables single sign-on (SSO) features, allowing an application to access multiple secured resources.
- Facilitates connections between cloud computing resources and users, decreasing the need for users to re-authenticate.

## Single sign-on

Single sign-on refers to an authentication process that permits a user to log on to a system once with a single set of credentials to access multiple applications or services.

A user logs in with a single ID and password to gain access to any of several related software systems. For more information, see [Single sign on](./bot-builder-concept-sso.md).

Many identity providers support a sign-out operation that revokes the user token and terminates access to to the associated applications and services.


> [!IMPORTANT]
> SSO enhances usability by reducing the number of times a user must enter credentials. It also provides better security by decreasing the potential attack surface.

## Azure Active Directory identity provider

Azure Active Directory (AD) is the identity service in Microsoft Azure that provides identity management and access control capabilities. It allows you to securely sign in users using industry  standard protocols like **OAuth2.0**.

You can choose from two AD identity provider implementations which have different settings as shown below.

> [!Note]
> You use the settings described here when configuring the **OAuth Connection Settings** in the Azure bot registration application. See [Add authentication to a bot](bot-builder-authentication.md).

# [Azure AD v1](#tab/adv1)

### Azure AD v1

You use the settings shown to configure the Azure AD developer platform (v1.0), also known as **Azure AD v1** endpoint, which allows to build  apps that securely sign in users with a Microsoft work or school account.
For more information, see [Azure Active Directory for developers (v1.0) overview](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-overview).

[!INCLUDE [azure-ad-v1-settings](~/includes/authentication/auth-aad-v1-settings.md)]

# [Azure AD v2](#tab/adv2)

### Azure AD v2

You use the settings shown to configure the Microsoft identity platform (v2.0), also known as **Azure AD v2** endpoint which is an evolution of the Azure AD platform (v1.0).  It allows a bot to get tokens to call Microsoft APIs, such as Microsoft Graph or custom APIs. 
For more information, see the [Microsoft identity platform (v2.0) overview](https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview).

The AD v2 settings enable a bot to access Office 365 data via the Microsoft Graph API.

[!INCLUDE [azure-ad-v2-settings](~/includes/authentication/auth-aad-v2-settings.md)]

---

See also

- [Why update to Microsoft identity platform (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare)

- [Microsoft identity platform (formerly Azure Active Directory for developers)](https://docs.microsoft.com/azure/active-directory/develop/).

## Other identity providers

Azure supports several identity providers. You can get a complete list, along with the related details, by running this Azure console command:

```cmd
az bot authsetting list-providers
```

You can also see the list of these providers in the [Azure portal](https://ms.portal.azure.com/), when you define the OAuth connection settings for a bot registration app.

![azure identity providers](media/concept-bot-authentication/bot-auth-identity-providers.png)


### OAuth generic providers

Azure supports generic OAuth2 which allow you to use your own identity providers.

You can choose from two generic identity provider implementations which have different settings as shown below.

> [!Note]
> You use the settings described here when configuring the **OAuth Connection Settings** in the Azure bot registration application.


# [Generic OAuth 2](#tab/ga2)

### Generic OAuth 2

Use this provider to configure any generic OAuth2 identity provider that has similar expectations as Azure AD provider, particularly AD v2. You have a limited number of properties because the query strings and request body payloads are fixed. For the values you enter, you can see how parameters to the various URls, query strings, and bodies are in curly braces {}.

[!INCLUDE [generic-oauth2-settings](~/includes/authentication/auth-generic-oauth2-settings.md)]


# [OAuth 2 generic provider](#tab/a2gp)

### OAuth 2 generic provider

Use this provider to configure any generic OAuth 2 service provider when you need more flexibility. It requires more configuration parameters. With this configuration you specify the URL templates, the query string templates, and the body templates for authorization, refresh, and token conversion. For the values you enter, you can see how parameters to the various URls, query strings, and bodies are in curly braces {}.

[!INCLUDE [generic-provider-oauth2-settings](~/includes/authentication/auth-generic-provider-oauth2-settings.md)]

---
