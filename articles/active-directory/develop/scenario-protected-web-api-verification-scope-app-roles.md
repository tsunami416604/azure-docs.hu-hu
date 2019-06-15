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
ms.openlocfilehash: b700825be9a7fe23fe4b50a2d69d4de71f7dc038
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116453"
---
# <a name="protected-web-api---adding-authorization-to-your-api"></a>Védett webes API - engedélyezési ad hozzá az API-hoz

Ez a cikk bemutatja, hogyan adhat engedélyt a webes API-nak. Ez a védelem biztosítja, hogy, csak hívja meg:

- alkalmazások megfelelő hatókörökkel felhasználók nevében 
- vagy a megfelelő alkalmazás-szerepkörök démon alkalmazások.

Az ASP.NET / ASP.NET Core webes API, amellyel biztosítható, kell hozzáadni a `[Authorize]` attribútum:

- a vezérlő magát, ha azt szeretné, hogy a védeni kívánt vezérlő a műveletek
- vagy az egyéni vezérlő művelet az API-hoz.

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Azonban ez a védelem nem elegendő. Azt, hogy az ASP.NET csak guaranties / ASP.NET Core fogja ellenőrizni a jogkivonatot. Az API-t, győződjön meg arról, hogy a jogcímek, vár, különösen a webes API meghívásához használt token kérelmezte van szüksége:

- a **hatókörök** Ha egy felhasználó nevében az API neve
- a **alkalmazás-szerepkörök** , ha az API nem hívható meg egy démon alkalmazásból.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>A felhasználók nevében nevű API-k hatókörök ellenőrzése

Ha egy ügyfélalkalmazás egy felhasználó nevében hívja meg az API-t, majd egy adott hatókörökkel, az API tulajdonosi jogkivonat kéréséhez szükséges (lásd: [helykódot |} Tulajdonosi jogkivonat](scenario-protected-web-api-app-configuration.md#bearer-token))

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The Web API will only accept tokens 1) for users, 2) having the `access_as_user` scope for
    /// this API
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result
        ...
    }
...
}
```

A `VerifyUserHasAnyAcceptedScope` metódus az alábbihoz hasonlóra kellene tennie:

- Győződjön meg arról, hogy a jogcímek nevű `http://schemas.microsoft.com/identity/claims/scope` vagy `scp`
- Győződjön meg arról, hogy a jogcím tartalmazó a hatókör, az API által várt értékkel rendelkezik.

```CSharp
    /// <summary>
    /// When applied to an <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// Web API has any of the accepted scopes.
    /// If the authenticated user does not have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized with the message telling which scopes are expected in the token
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

A mintakód olyan ASP.NET Core. Az ASP.NET csak cserélje le a `HttpContext.User` által `ClaimsPrincipal.Current`, és a jogcímtípus `"http://schemas.microsoft.com/identity/claims/scope"` által `"scp"` (lásd az alábbi kódrészletben is)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Alkalmazás-szerepkörök által démon alkalmazások API-k ellenőrzése

A webes API-t hívja meg, ha egy [démon alkalmazás](scenario-daemon-overview.md), majd az alkalmazást a webes API-alkalmazás számára van szükség. Úgy tapasztaltuk [scenario-protected-web-api-app-registration.md#how-to-expose-application-permissions--app-roles-] az, hogy az API-t tesz elérhetővé az ilyen engedélyek (mint például a `access_as_application` alkalmazás szerepkör).
Most már van szüksége az API-k, ellenőrizze, hogy az tartalmazza a tokent kapott a `roles` jogcímek és az, hogy ez a jogcím értéke azt vár. Ez az ellenőrzés során a kód hasonlít a kódot, amely ellenőrzi a delegált engedélyeket, kivéve, hogy a tesztelés helyett `scopes`, a tartományvezérlő műveleti tesztelést `roles`:

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
    // In this case we look for a `role` value of `access_as_application`
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

A mintakód az ASP.NET-hez. ASP.NET Core, csak cserélje `ClaimsPrincipal.Current` által `HttpContext.User` és a `"roles"` jogcím neve alapján `"http://schemas.microsoft.com/identity/claims/roles"` (lásd a fenti kódrészletben)

### <a name="accepting-app-only-tokens-if-the-web-api-should-only-be-called-by-daemon-apps"></a>Csak alkalmazás-jogkivonatokról fogad, ha a webes API-t kell lze volat pouze démon alkalmazások

A `roles` jogcím használják a felhasználók a felhasználó-hozzárendelés minták (lásd: [hogyan: Alkalmazás-szerepkörök hozzáadása az alkalmazásban, és fogadhatók a jogkivonat](howto-add-app-roles-in-azure-ad-apps.md)). Csak ellenőrzése a szerepkörök jogosultak az alkalmazások jelentkezik be, a felhasználók és fordítva, ha a szerepköröket is hozzárendelhető. Javasoljuk, hogy a különböző szerepkörök deklarálva a felhasználók és alkalmazások ezt zavarok megelőzése érdekében.

Ha azt szeretné, hogy csak a webes API meghívásához démon alkalmazások, érdemes feltétel hozzáadása, ha az alkalmazás-szerepkör, ellenőrizze, hogy, hogy a jogkivonat-e egy csak alkalmazás token:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

A más néven inverz feltétel ellenőrzése csak egy felhasználót, jelentkezzen be az API meghívása az alkalmazások lehetővé teszi.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyezze át az éles környezetbe](scenario-protected-web-api-production.md)
