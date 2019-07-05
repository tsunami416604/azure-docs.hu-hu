---
title: Védett webes API - alkalmazás helykódot |} Az Azure Active Directory
description: Ismerje meg, hogyan hozhat létre egy védett webes API-t, és konfigurálja az alkalmazás kódjában.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23ff03316a1f9409d4d6e4b7ddf52d0c8cc7a909
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551539"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>Védett webes API-hoz: Engedélyt ad hozzá az API-hoz

Ez a cikk bemutatja, hogyan adhat engedélyt a webes API-hoz. Ez a védelem biztosítja, hogy az API-t kizárólag a neve:

- Alkalmazások, felhasználók, akik rendelkeznek a megfelelő hatókörök nevében.
- Démon alkalmazások, amelyek rendelkeznek a megfelelő alkalmazás-szerepkörök.

Egy ASP.NET/ASP.NET Core webes API-k védelme érdekében kell hozzáadni a `[Authorize]` attribútum a következők közül:

- A vezérlő magát, ha azt szeretné, hogy a védeni kívánt vezérlő a műveletek
- Az egyéni vezérlő művelet az API-hoz

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Azonban ez a védelem nem elegendő. Ez garantálja, hogy csak, hogy a jogkivonat érvényesítéséhez ASP.NET/ASP.NET Core lesz. Győződjön meg arról, hogy a webes API a jogcímek kérelmezte meghívásához használt token azt vár, különösen az API-t kell:

- A *hatókörök*, ha az API hívása egy felhasználó nevében.
- A *alkalmazás-szerepkörök*, ha az API nem hívható meg egy démon alkalmazásból.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>A felhasználók nevében nevű API-k hatókörök ellenőrzése

Ha egy ügyfélalkalmazás egy felhasználó nevében hívja meg az API-t, kell egy tulajdonosi jogkivonat, amely rendelkezik az API hatóköreinek adott kérelem. (Lásd: [helykódot |} Tulajdonosi jogkivonat](scenario-protected-web-api-app-configuration.md#bearer-token).)

```CSharp
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

A `VerifyUserHasAnyAcceptedScope` metódus az alábbihoz hasonlóra kellene tennie:

- Győződjön meg arról, hogy a jogcím nevű `http://schemas.microsoft.com/identity/claims/scope` vagy `scp`.
- Győződjön meg arról, hogy a jogcím értéke, amely tartalmazza az API által várt hatókörébe.

```CSharp
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

A mintakód olyan ASP.NET Core. Az ASP.NET, csak cserélje le `HttpContext.User` a `ClaimsPrincipal.Current`, és cserélje le a jogcímtípus `"http://schemas.microsoft.com/identity/claims/scope"` a `"scp"`. (Lásd a cikk későbbi részében a kódtöredék.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Alkalmazás-szerepkörök által démon alkalmazások API-k ellenőrzése

Ha a webes API-t hívja meg a [démon alkalmazás](scenario-daemon-overview.md), amelyet az alkalmazás van szükség a webes API-alkalmazás számára. Úgy tapasztaltuk [adatokhoz hozzáférést biztosító alkalmazás-engedélyeket (alkalmazás-szerepkörök)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) , hogy az API-t tesz elérhetővé az ilyen engedélyek (például a `access_as_application` alkalmazás szerepkör).
Most már van szüksége az API-k, ellenőrizze, hogy az tartalmazza a tokent kapott a `roles` és, hogy ez a jogcím értéke, vár a kérelmet. Ez az ellenőrzés során a kód hasonlít a kódot, amely ellenőrzi a delegált engedélyeket, kivéve, hogy a tesztelés helyett `scopes`, a tartományvezérlő műveleti tesztelést `roles`:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

A `ValidateAppRole()` metódus ehhez hasonló lehet:

```CSharp
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

A mintakód az ASP.NET-hez. Az ASP.NET Core, csak cserélje le `ClaimsPrincipal.Current` a `HttpContext.User`, és cserélje le a `"roles"` jogcím nevét `"http://schemas.microsoft.com/identity/claims/roles"`. (Lásd a cikk korábbi részeiben kódrészlet.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Csak az alkalmazásra vonatkozó jogkivonatokat fogad, ha hívandó meg a webes API-k csak a démon alkalmazások

A `roles` jogcímet is szolgál a felhasználó-hozzárendelés minták felhasználója számára. (Lásd: [hogyan: Alkalmazás-szerepkörök hozzáadása az alkalmazásban, és fogadhatók a jogkivonat](howto-add-app-roles-in-azure-ad-apps.md).) Csak ellenőrzése a szerepkörök jogosultak az alkalmazások jelentkezik be, a felhasználók és fordítva, ha a szerepköröket is hozzárendelhető. Azt javasoljuk, hogy a felhasználók és alkalmazások ezt zavarok megelőzése érdekében különböző szerepkörök deklarálható.

Ha azt szeretné, hogy a webes API hívása csak démon alkalmazások, feltétel hozzáadása, ha az alkalmazás-szerepkör, ellenőrizze, hogy, hogy a jogkivonat-e egy csak alkalmazás token:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

Ellenőrzi a más néven inverz feltételt lehetővé teszi a csak a felhasználó jelentkezzen be az API meghívása az alkalmazások.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyezze át az éles környezetbe](scenario-protected-web-api-production.md)
