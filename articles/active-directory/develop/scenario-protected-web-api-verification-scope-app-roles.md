---
title: Hatókör- és alkalmazásszerepkörök védett webes API-jának ellenőrzése | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy védett webes API-t, és konfigurálhatja az alkalmazás kódját.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768115"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Védett webes API: Hatókörök és alkalmazásszerepkörök ellenőrzése

Ez a cikk azt ismerteti, hogyan adhat hozzá engedélyezést a webes API-hoz. Ez a védelem biztosítja, hogy az API-t csak a következők hívják meg:

- Alkalmazások a megfelelő hatóköröket használó felhasználók nevében.
- A megfelelő alkalmazásszerepkörrel rendelkező démonalkalmazások.

> [!NOTE]
> A cikk kódrészletek a következő mintákból származnak, amelyek teljesen működőképesek:
>
> - [ASP.NET Core Web API inkinövetes oktatóanyaga](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) a GitHubon
> - [webes API-minta ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Egy ASP.NET vagy ASP.NET Core webes API `[Authorize]` védelme érdekében hozzá kell adnia az attribútumot az alábbi elemek egyikéhez:

- Maga a vezérlő, ha minden vezérlőműveletet védeni szeretne
- Az API-hoz való egyéni vezérlőművelet

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

De ez a védelem nem elég. Csak azt garantálja, hogy ASP.NET és ASP.NET Core érvényesíti a jogkivonatot. Az API-nak ellenőriznie kell, hogy az API-hívásához használt jogkivonat a várt jogcímekkel van-e megkövetelve. Ezeket az állításokat különösen ellenőrizni kell:

- A *hatókörök,* ha az API-t egy felhasználó nevében hívják meg.
- Az *alkalmazásszerepkörök,* ha az API-t egy démonalkalmazásból lehet meghívni.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>A felhasználók nevében meghívott API-kban lévő hatókörök ellenőrzése

Ha egy ügyfélalkalmazás meghívja az API-t egy felhasználó nevében, az API-nak egy tulajdonosi jogkivonatot kell kérnie, amely az API-hoz tartozó hatóköröket kell kérnie. További információ: [Code configuration | Bemutatóra szóló token](scenario-protected-web-api-app-configuration.md#bearer-token).

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

A `VerifyUserHasAnyAcceptedScope` módszer a következő lépésekhez hasonló lépéseket tesz:

- Ellenőrizze, hogy van-e jogcím neve `http://schemas.microsoft.com/identity/claims/scope` vagy `scp`.
- Ellenőrizze, hogy a jogcím olyan értékkel rendelkezik-e, amely tartalmazza az API által várt hatókört.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

Az előző [mintakód](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) ASP.NET Core.The megelőző minta kód van részére ASP.NET Core. A ASP.NET csak `HttpContext.User` `ClaimsPrincipal.Current`cserélje ki a `"http://schemas.microsoft.com/identity/claims/scope"` lehetőségre, és cserélje ki a jogcímtípusát a lehetőségre. `"scp"` Lásd még a kódrészletet a cikk későbbi részében.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>A démonalkalmazások által hívott API-k alkalmazásszerepköreinek ellenőrzése

Ha a webes API-t egy [démonalkalmazás](scenario-daemon-overview.md)hívja meg, az alkalmazásnak alkalmazásengedélyt kell kérnie a webes API-hoz. Ahogy az [alkalmazásengedélyek (alkalmazásszerepkörök) kiállítások](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), az API teszi elérhetővé az ilyen engedélyeket. Egy példa `access_as_application` erre az alkalmazásszerepkör.

Most kell az API ellenőrizze, hogy a jogkivonatot kap tartalmazza a `roles` jogcímet, és hogy ez a jogcím rendelkezik-e a várt értékkel. Az ellenőrző kód hasonló a delegált engedélyeket ellenőrző kódhoz, azzal a különbséggel, hogy a vezérlő művelete a szerepköröket teszteli a hatókörök helyett:

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

A `ValidateAppRole` módszer lehet valami ilyesmi:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

Ezúttal a kódrészlet ASP.NET. A ASP.NET Core `ClaimsPrincipal.Current` esetében `HttpContext.User`csak cserélje `"roles"` ki `"http://schemas.microsoft.com/identity/claims/roles"`a tapont, és cserélje ki a jogcím nevét a lehetőségre. Lásd még a cikk korábbi kódrészletét.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Csak alkalmazásjogkivonatok elfogadása, ha a webes API-t csak démonalkalmazások hívják meg

A felhasználók szerepkörjogcímeket is használhatnak a felhasználói hozzárendelési mintákban, ahogyan az alkalmazásszerepkörök hozzáadása az alkalmazásban látható, [és a jogkivonatban is megkapják őket.](howto-add-app-roles-in-azure-ad-apps.md) Ha a szerepkörök mindkettőhöz hozzárendelhetők, a szerepkörök ellenőrzése lehetővé teszi, hogy az alkalmazások felhasználóként és felhasználóként jelentkezzenek be, hogy alkalmazásként jelentkezzenek be. A félreértések elkerülése érdekében azt javasoljuk, hogy deklarálja a felhasználók és alkalmazások különböző szerepköreit.

Ha azt szeretné, hogy csak a démonalkalmazások hívják meg a webes API-t, adja hozzá azt a feltételt, hogy a token csak alkalmazásjogkivonat, amikor érvényesíti az alkalmazásszerepkört.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Az inverz feltétel ellenőrzése lehetővé teszi, hogy csak a felhasználóbejelentkező alkalmazások hívják meg az API-t.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Átállás éles üzemre](scenario-protected-web-api-production.md)
