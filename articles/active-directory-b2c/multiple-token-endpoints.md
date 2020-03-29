---
title: OWIN-alapú webes API-k áttelepítése b2clogin.com
titleSuffix: Azure AD B2C
description: Megtudhatja, hogy miként engedélyezheti, hogy a .NET webes API támogassa a több jogkivonat-kibocsátó által kibocsátott jogkivonatokat, miközben áttelepíti az alkalmazásokat b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184094"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>OWIN-alapú webes API áttelepítése b2clogin.com

Ez a cikk a [.NET (OWIN) webes felületét](http://owin.org/)megvalósító webes API-k több tokenkibocsátójának támogatását lehetővé tevő módszert ismerteti. Több tokenvégpont támogatása akkor hasznos, ha az Azure Active Directory B2C (Azure AD B2C) API-kat és azok alkalmazásait *login.microsoftonline.com-ról* *b2clogin.com*telepíti át.

Azáltal, hogy támogatja az API-t a b2clogin.com és login.microsoftonline.com által kiadott jogkivonatok fogadásához, szakaszosan telepítheti a webalkalmazásokat, mielőtt eltávolítaná a login.microsoftonline.com kiadott jogkivonatok támogatását az API-ból.

A következő szakaszok egy példát mutatnak be arra, hogyan engedélyezz több kibocsátót egy webes API-ban, amely a [Microsoft OWIN][katana] köztes szoftver összetevőit (Katana) használja. Bár a kódpéldák a Microsoft OWIN köztes szoftverre vonatkoznak, az általános technikát más OWIN-függvénytárakra is alkalmazni kell.

> [!NOTE]
> Ez a cikk az Azure AD B2C-ügyfelek számára készült, akik jelenleg telepített API-kkal és alkalmazásokkal rendelkező, `login.microsoftonline.com` és akik át szeretnének térni az ajánlott `b2clogin.com` végpontra. Ha új alkalmazást állít be, használja [b2clogin.com](b2clogin.md) az utasítás szerint.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések folytatása előtt a következő Azure AD B2C-erőforrásokra van szükség:

* [A](tutorial-create-user-flows.md) bérlőben létrehozott felhasználói folyamatok vagy [egyéni házirendek](custom-policy-get-started.md)

## <a name="get-token-issuer-endpoints"></a>Token kiállítói végpontok bekéselése

Először be kell szereznie a token kiállító végpontURI-kat minden olyan kibocsátóhoz, amelyet támogatni szeretne az API-ban. Az Azure AD B2C-bérlő által támogatott *b2clogin.com* és *login.microsoftonline.com* végpontok lehívásához kövesse az alábbi eljárást az Azure Portalon.

Először válassza ki az egyik meglévő felhasználói folyamatot:

1. Keresse meg az Azure AD B2C-bérlőt az [Azure Portalon](https://portal.azure.com)
1. A **Házirendek**csoportban válassza a **Felhasználói folyamatok (házirendek) lehetőséget.**
1. Jelöljön ki egy meglévő házirendet, például *B2C_1_signupsignin1,* majd válassza **a Felhasználói folyamat futtatása lehetőséget.**
1. A lap tetején található **Felhasználói folyamat futtatása** fejlécalatt jelölje ki a hivatkozást az adott felhasználói folyamat OpenID Connect felderítési végpontjára való navigáláshoz.

    ![Jól ismert URI-hivatkozás az Azure Portal Futtatás a lapján](media/multi-token-endpoints/portal-01-policy-link.png)

1. A böngészőben megnyíló lapon `issuer` jegyezze fel az értéket, például:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. A **Tartomány kiválasztása legördülő** menüvel jelölje ki a másik tartományt, `issuer` majd ismét hajtsa végre az előző két lépést, és jegyezze fel annak értékét.

Most már két, a következőhöz hasonló URI-t kell rögzítenie:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Egyéni szabályzatok

Ha a felhasználói folyamatok helyett egyéni házirendek vannak, hasonló folyamattal lejuthat a kibocsátó URI-inak leigazolására.

1. Navigálás az Azure AD B2C-bérlőre
1. **Identitáskezelési keretrendszer** kiválasztása
1. Válassza ki az egyik függő entitás házirendet, például *B2C_1A_signup_signin*
1. Tartomány **kiválasztása legördülő** menüvel válasszon ki egy tartományt, például *yourtenant.b2clogin.com*
1. Az **OpenID Connect felderítési végpontja** alatt megjelenő hivatkozás kijelölése
1. Az `issuer` érték rögzítése
1. Hajtsa végre a *login.microsoftonline.com* 4-6.

## <a name="get-the-sample-code"></a>A mintakód letöltése

Most, hogy mindkét tokenvégpont URI-val rendelkezik, frissítenie kell a kódot, hogy mindkét végpont érvényes kibocsátó. Egy példa áttekintéséhez töltse le vagy klónozza a mintaalkalmazást, majd frissítse a mintát, hogy mindkét végpontot érvényes kibocsátóként támogassa.

Töltse le az archívumot: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Több kibocsátó engedélyezése a webes API-ban

Ebben a szakaszban frissíti a kódot, és adja meg, hogy mindkét jogkivonat-kibocsátó végpontok érvényesek.

1. A **B2C-WebAPI-DotNet.sln** megoldás megnyitása a Visual Studióban
1. A **TaskService** projektben nyissa meg a *TaskService\\App_Start\\**Startup.Auth.cs*** fájlt a szerkesztőben
1. Adja hozzá `using` a következő direktívát a fájl tetejéhez:

    `using System.Collections.Generic;`
1. Adja [`ValidIssuers`][validissuers] hozzá a [`TokenValidationParameters`][tokenvalidationparameters] tulajdonságot a definícióhoz, és adja meg az előző szakaszban rögzített mindkét URI-t:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`MSAL.NET biztosítja, és az OWIN köztes szoftver a kód következő szakaszában *Startup.Auth.cs.* Ha több érvényes kibocsátó van megadva, az OWIN alkalmazásfolyamat tudatában van annak, hogy mindkét jogkivonat-végpont érvényes kibocsátó.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Mint korábban említettük, más OWIN-könyvtárak általában hasonló lehetőséget biztosítanak több kibocsátó támogatására. Bár a jelen cikk hatókörén kívül esik példák minden könyvtárra, a legtöbb kódtárhoz hasonló technikát használhat.

## <a name="switch-endpoints-in-web-app"></a>Végpontok váltása a webalkalmazásban

Mindkét URI-k most már támogatja a webes API-t, most frissítenie kell a webalkalmazást, hogy lekéri a jogkivonatokat a b2clogin.com végpont.

Beállíthatja például, hogy a mintawebalkalmazás az új `ida:AadInstance` végpontot használja a **TaskWebApp-projekt** *\\TaskWebApp **Web.config*** fájljában lévő érték módosításával.

Módosítsa `ida:AadInstance` a TaskWebApp *web.config* fájljának értékét `{your-b2c-tenant-name}.b2clogin.com` úgy, hogy a `login.microsoftonline.com`helyett a hivatkozásokra hivatkozzon.

Előtte:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Utána `{your-b2c-tenant}` (cserélje le a B2C bérlő nevét):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Amikor a végponti karakterláncok a webalkalmazás végrehajtása során jönnek létre, a b2clogin.com-alapú végpontok, amikor jogkivonatokat kér.

## <a name="next-steps"></a>További lépések

Ez a cikk a Microsoft OWIN köztes szoftver (Katana) által több kibocsátóvégponttokenek fogadására implementáló webes API konfigurálásának módszerét mutatja be. Amint azt észreveheti, a TaskService és taskwebapp-projektek *Web.Config* fájljaiban számos más karakterlánc is található, amelyeket módosítani kell, ha ezeket a projekteket saját bérlője ellen szeretné megépíteni és futtatni. Nyugodtan módosíthatja a projekteket megfelelően, ha szeretné látni őket akcióban, azonban a teljes végigjárni ennek hatókörén kívül esik a jelen cikk.

Az Azure AD B2C által kibocsátott biztonsági tokenek különböző típusairól az [Azure Active Directory B2C tokenek áttekintése című témakörben](tokens-overview.md)olvashat bővebben.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
