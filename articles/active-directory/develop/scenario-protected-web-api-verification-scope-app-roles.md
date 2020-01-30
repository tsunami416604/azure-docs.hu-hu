---
title: Hatókörök és alkalmazás-szerepkörök által védett webes API ellenőrzése | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre védett webes API-t, és hogyan konfigurálhatja az alkalmazás kódját.
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768115"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Védett webes API: hatókörök és alkalmazás-szerepkörök ellenőrzése

Ez a cikk azt ismerteti, hogyan adhat hozzá engedélyt a webes API-hoz. Ez a védelem biztosítja, hogy az API-t csak a következő hívja meg:

- Alkalmazások a megfelelő hatókörökkel rendelkező felhasználók nevében.
- A megfelelő alkalmazás-szerepkörökkel rendelkező Daemon-alkalmazások.

> [!NOTE]
> A cikkből származó kódrészletek kinyerve a következő mintákból, amelyek teljes mértékben működőképesek:
>
> - [ASP.net Core web API növekményes oktatóanyag](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) a githubon
> - [ASP.NET web API-minta](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

A ASP.NET vagy ASP.NET Core webes API-k védetté tételéhez hozzá kell adnia a `[Authorize]` attribútumot a következő elemek egyikéhez:

- A vezérlőt, ha azt szeretné, hogy az összes vezérlő művelet védve legyen
- Az API-hoz tartozó egyéni vezérlő művelet

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ez a védelem azonban nem elég. Csak azt garantálja, hogy a ASP.NET és a ASP.NET Core érvényesítse a jogkivonatot. Az API-nak ellenőriznie kell, hogy az API meghívásához használt jogkivonat a várt jogcímeket kéri-e. A jogcímeknek különösen az ellenőrzésre van szükségük:

- A *hatókörök* , ha az API-t egy felhasználó nevében hívják meg.
- Az *alkalmazás szerepkörei* , ha az API-t egy Daemon-alkalmazásból lehet meghívni.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Az API-k hatókörének ellenőrzése felhasználók nevében

Ha egy ügyfélalkalmazás egy felhasználó nevében hívja meg az API-t, az API-nak olyan tulajdonosi jogkivonatot kell igényelnie, amely meghatározott hatókörökkel rendelkezik az API-hoz. További információ: [kód konfigurálása | Tulajdonosi jogkivonat](scenario-protected-web-api-app-configuration.md#bearer-token).

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

A `VerifyUserHasAnyAcceptedScope` metódus a következő lépésekhez hasonló:

- Ellenőrizze, hogy van-e `http://schemas.microsoft.com/identity/claims/scope` vagy `scp`nevű jogcím.
- Ellenőrizze, hogy a jogcím tartalmaz-e olyan értéket, amely tartalmazza az API által várt hatókört.

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

A fenti [mintakód](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) a ASP.net Core. A ASP.NET esetében egyszerűen cserélje le a `HttpContext.User`t a `ClaimsPrincipal.Current`ra, és cserélje le a jogcím típusát `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"`. A kódrészletet a cikk későbbi részében is láthatja.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Az alkalmazás szerepköreinek ellenőrzése Daemon-alkalmazások által hívott API-kon

Ha a webes API-t egy [Daemon-alkalmazás](scenario-daemon-overview.md)hívja meg, akkor az alkalmazásnak a webes API-hoz szükséges alkalmazási engedéllyel kell rendelkeznie. Ahogy az az [alkalmazás engedélyeinek](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)kifejtése című ábrán látható, az API elérhetővé teszi az ilyen engedélyeket. Az egyik példa a `access_as_application` alkalmazás-szerepkör.

Most az API-nak ellenőriznie kell, hogy a kapott jogkivonat tartalmazza-e a `roles` jogcímet, és hogy ez a jogcím a várt értékkel rendelkezik-e. Az ellenőrző kód hasonló a delegált engedélyeket ellenőrző kódhoz, kivéve, hogy a vezérlő műveleti teszteket végez a hatókörök helyett a szerepkörökhöz:

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

A `ValidateAppRole` metódus a következőhöz hasonló lehet:

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

Ezúttal a kódrészlet a ASP.NET. ASP.NET Core esetében csak cserélje le a `ClaimsPrincipal.Current`t a `HttpContext.User`re, és cserélje le az `"roles"` jogcím nevét `"http://schemas.microsoft.com/identity/claims/roles"`-ra. Lásd még a cikk korábbi részében szereplő kódrészletet is.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Csak alkalmazásbeli tokenek fogadása, ha a webes API-t csak Daemon-alkalmazások hívhatják meg

A felhasználók a felhasználói hozzárendelési mintákban is használhatják a szerepkörök jogcímeit, ahogy [azt az útmutató: alkalmazás-Szerepkörök hozzáadása az alkalmazásban című témakörben és a jogkivonatban](howto-add-app-roles-in-azure-ad-apps.md). Ha a szerepkörök mindkettőhöz hozzárendelhetők, a szerepkörök ellenőrzése lehetővé teszi, hogy az alkalmazások felhasználóként és felhasználóként jelentkezzenek be alkalmazásként. Javasoljuk, hogy a félreértések elkerülése érdekében különböző szerepköröket deklaráljon a felhasználók és az alkalmazások számára.

Ha azt szeretné, hogy a démon-alkalmazások meghívja a webes API-t, adja hozzá azt a feltételt, amely szerint a jogkivonat csak alkalmazási token, amikor érvényesíti az alkalmazás szerepkörét.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Az inverz feltétel ellenőrzése lehetővé teszi, hogy csak azok az alkalmazások jelentkezzenek be az API-ra, amelyek bejelentkeznek a felhasználókba.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Áthelyezés éles környezetbe](scenario-protected-web-api-production.md)
