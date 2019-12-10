---
title: OWIN-alapú webes API-k migrálása a b2clogin.com-be
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan engedélyezheti a .NET-es webes API-k számára, hogy támogassa a több jogkivonat-kiállítók által kiállított jogkivonatokat, miközben az alkalmazásokat a b2clogin.com
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f07eb65243b4f797a2955e33aca50ed8c46d256e
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950986"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>OWIN-alapú webes API migrálása b2clogin.com

Ez a cikk egy olyan technikát ismertet, amely lehetővé teszi a több jogkivonat-kiállítók támogatását a webes API- [k nyílt webes felületének (OWIN)](http://owin.org/)megvalósításában. A több jogkivonat-végpont támogatása akkor hasznos, ha Azure Active Directory B2C (Azure AD B2C) API-kat és azok alkalmazásait a *login.microsoftonline.com* -ből a *b2clogin.com*-be telepíti.

Ha az API-ban támogatást ad a b2clogin.com és a login.microsoftonline.com által kiállított jogkivonatok fogadásához, áttelepítheti a webalkalmazásokat, mielőtt eltávolítja a login.microsoftonline.com által kiadott tokenek támogatását az API-ból.

A következő részek egy példát mutatnak arra, hogyan engedélyezhető több kiállító egy webes API-ban, amely a [Microsoft OWIN][katana] middleware-összetevőket (Katana) használja. Bár a kód például a Microsoft OWIN-alapú middleware-re vonatkozik, az általános technikának más OWIN-könyvtárakra is érvényesnek kell lennie.

> [!NOTE]
> Ez a cikk olyan Azure AD B2C ügyfelek számára készült, akik a jelenleg telepített API-kkal és alkalmazásokkal foglalkoznak `login.microsoftonline.com` és az ajánlott `b2clogin.com` végpontra való áttelepítéshez. Ha új alkalmazást állít be, használja a [b2clogin.com](b2clogin.md) utasítást.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek folytatása előtt a következő Azure AD B2C erőforrásokra van szükség:

* A bérlőben létrehozott [felhasználói folyamatok](tutorial-create-user-flows.md) vagy [Egyéni házirendek](active-directory-b2c-get-started-custom.md)

## <a name="get-token-issuer-endpoints"></a>Jogkivonat-kiállítói végpontok beolvasása

Először le kell kérnie a jogkivonat kiállítói végpontjának URI azonosítóit minden olyan kiállítónál, amelyet támogatni szeretne az API-ban. A Azure AD B2C-bérlő által támogatott *b2clogin.com* és *login.microsoftonline.com* végpontok beszerzéséhez használja az alábbi eljárást a Azure Portal.

Először válassza ki az egyik meglévő felhasználói folyamatot:

1. Navigáljon a Azure AD B2C-bérlőhöz a [Azure Portal](https://portal.azure.com)
1. A **házirendek**területen válassza a **felhasználói folyamatok (szabályzatok) lehetőséget.**
1. Válasszon ki egy meglévő szabályzatot, például *B2C_1_signupsignin1*, majd válassza a **felhasználói folyamat futtatása** lehetőséget.
1. A lap tetején található **felhasználói folyamat futtatása** szakaszban válassza ki a hiperhivatkozást, hogy navigáljon az OpenID Connect Discovery-végponthoz az adott felhasználói folyamathoz.

    ![Jól ismert URI-hivatkozás a Azure Portal futtatása lapon](media/multi-token-endpoints/portal-01-policy-link.png)

1. A böngészőben megnyíló lapon jegyezze fel a `issuer` értéket, például:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. A **tartomány kiválasztása** legördülő listából válassza ki a másik tartományt, majd ismét hajtsa végre az előző két lépést, és jegyezze fel a `issuer` értékét.

Ekkor a következőhöz hasonló két URI-t kell rögzítenie:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Egyéni szabályzatok

Ha felhasználói folyamatok helyett egyéni szabályzatokkal rendelkezik, a kiállító URI-k beszerzéséhez hasonló eljárás használható.

1. Navigáljon a Azure AD B2C bérlőhöz
1. **Identitási élmény keretrendszerének** kiválasztása
1. Válassza ki a függő entitások egyik házirendjét, például *B2C_1A_signup_signin*
1. A tartomány **kiválasztása** legördülő listából válasszon ki egy tartományt, például *yourtenant.b2clogin.com*
1. Válassza ki az **OpenID Connect Discovery-végpont** alatt megjelenő hiperhivatkozást
1. A `issuer` értékének rögzítése
1. Hajtsa végre a 4-6 lépéseket a másik tartományhoz, például *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>A mintakód letöltése

Most, hogy mindkét jogkivonat-végpont URI-val rendelkezik, frissítenie kell a kódot annak megadásához, hogy mindkét végpont érvényes kiállítók legyenek. Ha egy példát szeretne megtekinteni, töltse le vagy klónozott módon a minta alkalmazást, majd frissítse a mintát úgy, hogy mindkét végpontot érvényes kiállítóként támogassa.

Töltse le az archívumot: [Active-Directory-B2C-DotNet-WebApp-and-webapi-Master. zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Több kiállító engedélyezése a webes API-ban

Ebben a szakaszban a kód frissítésével adja meg, hogy a jogkivonat-kiállítói végpontok is érvényesek-e.

1. A **B2C-WebAPI-DotNet. SLN** megoldás megnyitása a Visual Studióban
1. A **TaskService** projektben nyissa meg a *TaskService\\App_Start\\* * Startup.auth.cs** * fájlt a szerkesztőben
1. Adja hozzá a következő `using` direktívát a fájl elejéhez:

    `using System.Collections.Generic;`
1. Adja hozzá a [`ValidIssuers`][validissuers] tulajdonságot a [`TokenValidationParameters`][tokenvalidationparameters] definícióhoz, és adja meg mindkét URI-t, amelyet az előző szakaszban rögzített:

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

a `TokenValidationParameters` a MSAL.NET által biztosított, és a *Startup.auth.cs*-ben a kód következő szakaszában a OWIN middleware használja fel. Ha több érvényes kiállító van megadva, a OWIN-alkalmazás folyamata arról tájékoztat, hogy mindkét jogkivonat-végpont érvényes kiállító.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Ahogy azt korábban említettük, a többi OWIN-tár általában hasonló lehetőséget biztosít több kiállító támogatásához. Bár az egyes könyvtárakra vonatkozó példák a jelen cikk hatókörén kívül esnek, a legtöbb könyvtárhoz hasonló módszert használhat.

## <a name="switch-endpoints-in-web-app"></a>Végpontok váltása a webalkalmazásban

A webes API mostantól támogatja mindkét URI-t, most frissítenie kell a webalkalmazást, hogy lekérje a jogkivonatokat a b2clogin.com-végpontról.

Beállíthatja például a minta webalkalmazást úgy, hogy az új végpontot használja a **TaskWebApp** projekt *TaskWebApp\\* * web. config** * fájljának `ida:AadInstance` értékének módosításával.

Módosítsa a TaskWebApp *web. config fájljának* `ida:AadInstance` értékét úgy, hogy az a `login.microsoftonline.com`helyett `{your-b2c-tenant-name}.b2clogin.com`ra hivatkozik.

Előtte:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Ezután (cserélje le a `{your-b2c-tenant}`t a B2C-bérlő nevére):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Ha a végponti karakterláncok a webalkalmazás végrehajtása során jönnek létre, a rendszer a b2clogin.com-alapú végpontokat használja, amikor jogkivonatokat kér.

## <a name="next-steps"></a>Következő lépések

Ez a cikk bemutatja a Microsoft OWIN middleware (Katana) megvalósítására szolgáló webes API konfigurálásának módszerét, hogy több kiállítói végponttól fogadja el a jogkivonatokat. Észreveheti, hogy több más karakterlánc is szerepel a *web. config* fájlban mind a TaskService, mind a TaskWebApp projektek esetében, amelyeket módosítania kell, ha a saját bérlője alapján szeretné felépíteni és futtatni ezeket a projekteket. Szívesen módosítja a projekteket megfelelően, ha szeretné megtekinteni őket működés közben, a teljes körű megvalósítás azonban a jelen cikk hatókörén kívül esik.

További információ a Azure AD B2C által kibocsátott biztonsági jogkivonatok különböző típusairól: a [Azure Active Directory B2C tokenek áttekintése](active-directory-b2c-reference-tokens.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
