---
title: Az Azure AD által védett ASP.NET webes API meghívása – Microsoft Identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan hívhat meg Azure Active Directory által védett ASP.NET webes API-t egy Windows asztali (WPF-) alkalmazásból. A WPF-ügyfél hitelesíti a felhasználót, hozzáférési jogkivonatot kér, és meghívja a webes API-t.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328415"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>Több-bérlős démon létrehozása a Microsoft Identity platform-végponttal

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Microsoft Identity platformot a Microsoft üzleti ügyfelei adatainak hosszú távú, nem interaktív folyamatokban való eléréséhez. A minta démon a [OAuth2-ügyfél hitelesítő adatait](v2-oauth2-client-creds-grant-flow.md) használja a hozzáférési jogkivonat beszerzéséhez, amelyet ezután a [Microsoft Graph](https://graph.microsoft.io) meghívására és a szervezeti adatok elérésére használ.

Az alkalmazás ASP.NET MVC-alkalmazásként készült, és a OWIN OpenID Connect middleware használatával jelentkezik be a felhasználókba.  Ennek a mintának a "démon" összetevője egy API-vezérlő, amely a híváskor lekéri az ügyfél Azure AD-bérlőben található felhasználók listáját Microsoft Graph.  Ezt a `SyncController.cs` egy AJAX-hívás indítja el a webalkalmazásban, és a [.net-hez készült Microsoft Authentication Library (MSAL)](msal-overview.md) használatával beszerzi a Microsoft Graph hozzáférési jogkivonatát.

Egy egyszerűbb Console Daemon-alkalmazás esetében tekintse meg a [.net Core Daemon](quickstart-v2-netcore-daemon.md)gyors útmutatóját.

## <a name="scenario"></a>Forgatókönyv

Mivel az alkalmazás egy olyan több-bérlős alkalmazás, amelyet bármely Microsoft üzleti ügyfél használ, meg kell adnia egy módot arra, hogy az ügyfelek "regisztráljanak" vagy "csatlakozzanak" az alkalmazáshoz a vállalati adatszolgáltatáshoz.  A kapcsolódási folyamat során a vállalati rendszergazda először közvetlenül az **alkalmazáshoz ad engedélyeket** , így nem interaktív módon férhet hozzá a céges adatbázisokhoz, és nincs bejelentkezett felhasználó.  A példában szereplő logika többsége bemutatja, hogyan érheti el ezt a csatlakozási folyamatot az Identity platform [rendszergazdai engedélyezési](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) végpontjának használatával.

![Topológia](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Az ebben a mintában használt fogalmakkal kapcsolatos további információkért olvassa el az [Identity platform Endpoint Client hitelesítő adatok protokoll dokumentációját](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Előfeltételek

A minta ebben a rövid útmutatóban való futtatásához a következőkre lesz szüksége:

- [Visual Studio 2017 vagy 2019](https://visualstudio.microsoft.com/downloads/)
- Egy Azure Active Directory (Azure AD) bérlő. Az Azure AD-bérlő beszerzésével kapcsolatos további információkért lásd: [Azure ad-bérlő beszerzése](quickstart-create-new-tenant.md)
- Egy vagy több felhasználói fiók az Azure AD-bérlőben. Ez a minta nem fog működni Microsoft-fiók (korábban Windows Live-fiókkal). Ezért ha a [Azure Portalba](https://portal.azure.com) bejelentkezett egy Microsoft-fiók, és korábban soha nem hozott létre felhasználói fiókot a címtárban, ezt most meg kell tennie.

## <a name="clone-or-download-this-repository"></a>A tárház klónozása vagy letöltése

A rendszerhéjból vagy parancssorból:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Vagy [töltse le a mintát egy zip-fájlba](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>A minta alkalmazás regisztrálása az Azure AD-Bérlővel

Ebben a példában egy projekt található. A regisztráláshoz a következőket teheti:

- Kövesse az alábbi lépéseket a [minta regisztrálásához a Azure Active Directory Bérlővel](#register-the-sample-application-with-your-azure-ad-tenant) , és [konfigurálja a mintát az Azure ad-bérlő használatára](#choose-the-azure-ad-tenant-for-the-applications)
- Vagy használjon olyan PowerShell-parancsfájlokat, amelyek:
  - **Automatikusan** létrehozza az Azure ad-alkalmazásokat és a kapcsolódó objektumokat (jelszavak, engedélyek, függőségek)
  - Módosítsa a Visual Studio-projektek konfigurációs fájljait.

Ha ezt az automatizálást szeretné használni:

1. Windows rendszeren futtassa a PowerShellt, és navigáljon a klónozott könyvtár gyökeréhez
1. A PowerShell futtatása:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Futtassa a szkriptet az Azure AD-alkalmazás létrehozásához, és ennek megfelelően konfigurálja a minta alkalmazás kódját.
1. A PowerShell futtatása:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > A parancsfájlok futtatásának egyéb módjairól az [alkalmazás-létrehozási parancsfájlok](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md) című témakörben olvashat.

1. Nyissa meg a Visual Studio-megoldást, és kattintson a Start gombra a kód futtatásához.

Ha nem szeretné használni ezt az automatizálást, kövesse az alábbi lépéseket.

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>Az Azure AD-bérlő kiválasztása az alkalmazásokhoz

Első lépésként a következőket kell tennie:

1. Jelentkezzen be [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, illetve személyes Microsoft-fiókjával.
1. Ha a fiókja egynél több Azure AD-bérlőn található, válassza ki a profilt a lap tetején található menü jobb felső sarkában, majd **váltson át a könyvtárra**.
   Módosítsa a portál munkamenetét a kívánt Azure AD-bérlőre.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Az ügyfélalkalmazás regisztrálása (DotNet-web-Daemon-v2)

1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
1. Válassza az **új regisztráció**lehetőséget.
1. Amikor megjelenik az **Alkalmazás regisztrálása lap**, adja meg az alkalmazás regisztrációs adatait:
   - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `dotnet-web-daemon-v2`).
   - A **támogatott fiókok típusai** szakaszban válassza a **fiókok lehetőséget bármely szervezeti címtárban**.
   - Az átirányítási URI (nem kötelező) szakaszban válassza a **web** elemet a kombinált listában, és adja meg a következő átirányítási URI-ket:
       - `https://localhost:44316/`
       - `https://localhost:44316/Account/GrantPermissions` ha egynél több átirányítási URI van, akkor az alkalmazás sikeres létrehozása után később hozzá kell adnia őket a **hitelesítés** lapról.
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás **– Áttekintés** oldalon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később. Ehhez a projekthez a Visual Studio konfigurációs fájlját kell konfigurálnia.
1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
   - A **Speciális beállítások** szakaszban a **KIJELENTKEZÉSI URL-cím** beállítása `https://localhost:44316/Account/EndSession`
   - A **Speciális beállítások** | **implicit támogatás** szakaszban tekintse meg a **hozzáférési jogkivonatokat** és az **azonosító jogkivonatokat** , mivel ez a minta megköveteli, hogy az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezze a bejelentkezést a felhasználó felé, és hívjon fel egy API-t.
1. Kattintson a **Mentés** gombra.
1. A **tanúsítványok & titkok** oldalon, az **ügyfél titkai** szakaszban válassza az **új ügyfél titka**elemet:

   - Írja be a kulcs leírását (például `app secret`),
   - Válassza ki a kulcs időtartamát **1 év**vagy **2 év**között, vagy **Soha ne járjon le**.
   - Amikor megnyomja a **Hozzáadás** gombot, a kulcs értéke megjelenik, másolja és menti az értéket egy biztonságos helyen.
   - Erre a kulcsra később szüksége lesz a projekt konfigurálásához a Visual Studióban. Ez a kulcs nem jelenik meg újra, és semmilyen más módon nem kérhető le, ezért jegyezze fel, amint a Azure Portal látható.
1. Az alkalmazáshoz tartozó lapok listájában válassza az **API-engedélyek** lehetőséget.
   - Kattintson az **engedély hozzáadása** gombra, majd
   - Győződjön meg arról, hogy a **Microsoft API** -k lap van kiválasztva
   - A *gyakran használt Microsoft API* -k szakaszban kattintson **Microsoft Graph**
   - Az **alkalmazás engedélyei** szakaszban győződjön meg arról, hogy a megfelelő engedélyek be vannak jelölve: **User. Read. All**
   - Válassza az **engedélyek hozzáadása** gombot

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>A minta beállítása az Azure AD-bérlő használatára

Az alábbi lépésekben a "ClientID" ugyanaz, mint az "Application ID" vagy a "AppId".

A projektek konfigurálásához nyissa meg a megoldást a Visual Studióban

### <a name="configure-the-client-project"></a>Az ügyfél-projekt konfigurálása

Ha a telepítési parancsfájlokat használta, a következő módosításokat alkalmazza a rendszer.

1. A `UserSync\Web.Config` fájl megnyitása
1. Keresse meg `ida:ClientId` az alkalmazás kulcsát, és cserélje le a meglévő értéket a Azure Portalról másolt `dotnet-web-daemon-v2` alkalmazás alkalmazás-azonosítójával (clientId).
1. Keresse meg `ida:ClientSecret` az alkalmazás kulcsát, és cserélje le a meglévő értéket a `dotnet-web-daemon-v2` alkalmazás létrehozása során mentett kulcsra a Azure Portal.

## <a name="run-the-sample"></a>Minta futtatása

Tisztítsa meg a megoldást, hozza létre újra a megoldást, majd futtassa a UserSync alkalmazást, majd jelentkezzen be rendszergazdaként az Azure AD-bérlőben.  Ha nem rendelkezik Azure AD-Bérlővel a teszteléshez, az [alábbi utasításokat követve](quickstart-create-new-tenant.md) kérhet le egyet.

Amikor bejelentkezik, az alkalmazás először kérni fogja, hogy jelentkezzen be & olvassa el a felhasználói profilt.  Ez az engedély lehetővé teszi az alkalmazás számára, hogy üzleti felhasználó legyen.

![Felhasználói beleegyezett](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Az alkalmazás ezután megpróbál szinkronizálni az Azure AD-bérlő felhasználóinak listáját a Microsoft Graphon keresztül.  Ha nem tudja megtenni ezt a lehetőséget, a megkéri Önt (a bérlői rendszergazdát), hogy csatlakozzanak a bérlőhöz az alkalmazáshoz.

Az alkalmazás ezután engedélyt kér a bérlőben lévő felhasználók listájának olvasásához.

![Rendszergazdai engedély](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

**Az engedély megadása után kijelentkezés az alkalmazásból**történik. Erre azért van szükség, hogy a Graph meglévő hozzáférési jogkivonatai el legyenek távolítva a jogkivonat-gyorsítótárból. Ha ismét bejelentkezett, a kapott friss jogkivonat rendelkezik a szükséges engedélyekkel az MS Graph-hívások kezdeményezéséhez.
Az engedély megadása után az alkalmazás bármikor lekérdezheti a felhasználókat.  Ezt úgy ellenőrizheti, hogy a felhasználók lapon a felhasználók **szinkronizálása** gombra kattint a felhasználók listájának frissítése lehetőségre kattintva.  Próbálkozzon egy felhasználó hozzáadásával vagy eltávolításával, majd a lista újraszinkronizálásával (de ne feledje, hogy csak a felhasználók első oldalát szinkronizálja).

## <a name="about-the-code"></a>A kód ismertetése

A példához tartozó kód a következő fájlokban található:

- Kezdeti bejelentkezés: `App_Start\Startup.Auth.cs`, `Controllers\AccountController.cs`.  Különösen a vezérlő műveletei rendelkeznek egy engedélyezési attribútummal, amely arra kényszeríti a felhasználót, hogy jelentkezzen be. Az alkalmazás az [engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md) használja a bejelentkezéshez a felhasználónak.
- A felhasználók listájának szinkronizálása a helyi memóriában tárolt tárolóban: `Controllers\SyncController.cs`
- A helyi memóriában tárolt tárolóban lévő felhasználók listájának megjelenítése: `Controllers\UserController.cs`
- Engedélyek beszerzése a bérlői rendszergazdától a rendszergazdai engedélyezési végpont használatával: `Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>A minta alkalmazás újbóli létrehozása

1. A Visual Studióban hozzon létre egy új `Visual C#` `ASP.NET Web Application (.NET Framework)` projektet. A következő képernyőn válassza ki a `MVC`-projekt sablonját. Adja hozzá a `Web API` mappáját és alapvető hivatkozásait is, mivel később egy webes API-vezérlőt szeretne hozzáadni.  Hagyja meg a projekt kiválasztott hitelesítési módját alapértelmezettként, azaz `No Authentication`".
2. Válassza ki a projektet a **megoldáskezelő** ablakban, és az **F4** billentyű lenyomásával vigye a projekt tulajdonságait. A projekt tulajdonságainál állítsa be `True`az **SSL** -t. Jegyezze fel az **SSL URL-címét**. Szüksége lesz rá az alkalmazás regisztrációjának Azure Portal való konfigurálásakor.
3. Adja hozzá a következő ASP.Net OWIN middleware-Nuget: `Microsoft.Owin.Security.ActiveDirectory`, `Microsoft.Owin.Security.Cookies` és `Microsoft.Owin.Host.SystemWeb`, `Microsoft.IdentityModel.Protocol.Extensions`, `Microsoft.Owin.Security.OpenIdConnect` és `Microsoft.Identity.Client`. 
4. A `App_Start` mappában hozzon létre egy osztályt `Startup.Auth.cs`. Távolítsa el `.App_Start` a névtér nevéből.  Cserélje le a `Startup` osztály kódját a minta alkalmazás ugyanazon fájljából származó kóddal.  Ügyeljen arra, hogy a teljes osztály definícióját használja!  A definíció `public class Startup`ról `public partial class Startup`re változik
5. A `Startup.Auth.cs` a hiányzó hivatkozásokat a Visual Studio IntelliSense által javasolt `using` utasítások hozzáadásával oldják fel.
6. Kattintson a jobb gombbal a projektre, válassza a Hozzáadás, majd a Class (osztály) lehetőséget, és a keresőmezőbe írja be a "OWIN" kifejezést.  A "OWIN indítási osztály" megjelenik kijelölésként; Jelölje ki, és nevezze el az osztályt `Startup.cs`.
7. `Startup.cs`cserélje le a `Startup` osztály kódját a minta alkalmazás ugyanazon fájljából származó kóddal.  Vegye figyelembe, hogy a definíció `public class Startup`ról `public partial class Startup`ra változik.
8. A mappában adjon hozzá egy új, `MsGraphUser.cs`nevű osztályt.  Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
9. Vegyen fel egy új **MVC 5 vezérlőt – üres** néven `AccountController`. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
10. Vegyen fel egy új **MVC 5 vezérlőt – üres** néven `UserController`. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
11. Új **webes API 2 vezérlő hozzáadása – üres** nevű `SyncController`. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
12. A felhasználói felületen a `Views\Account` mappában vegyen fel három **üres (modell nélküli) nézetet** `GrantPermissions`, `Index` és `UserMismatch` névvel, valamint egy `Index`t a `Views\User` mappában. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
13. Frissítse a `Shared\_Layout.cshtml` és `Home\Index.cshtml`, hogy megfelelően összekapcsolja a különböző nézeteket.

## <a name="deploy-this-sample-to-azure"></a>A minta üzembe helyezése az Azure-ban

A projekt WebApp/web API-projektekkel rendelkezik. Az Azure-webhelyekre történő üzembe helyezéséhez a következőkre lesz szüksége:

- Azure-webhely létrehozása
- a webalkalmazás/webes API-k közzététele a webhelyen, valamint
- frissítse az ügyfele (ke) t, hogy IIS Express helyett a webhelyet hívja meg.

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>A `dotnet-web-daemon-v2` létrehozása és közzététele egy Azure-webhelyen

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Kattintson a bal felső sarokban található `Create a resource` elemre **, válassza a web --> ** **webalkalmazás**lehetőséget, és adja meg a webhely nevét, például `dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Ezután válassza ki a `Subscription`, `Resource Group``App service plan and Location`. `OS` lesz a **Windows** , és `Publish` **kód**lesz.
1. Kattintson a `Create` gombra, és várjon, amíg a rendszer létrehozza a App Service.
1. Miután beolvasta a `Deployment succeeded` értesítést, a `Go to resource` gombra kattintva navigáljon az újonnan létrehozott app Service-hez.
1. A webhely létrehozása után keresse meg az **irányítópulton** , és kattintson rá **app Services** **Áttekintés** képernyő megnyitásához.
1. A App Service **Áttekintés** lapján töltse le a közzétételi profilt a **közzétételi profil beolvasása** hivatkozásra kattintva, és mentse.  Más üzembe helyezési mechanizmusok, például a verziókövetés, is használhatók.
1. Váltson a Visual Studióra, és lépjen a DotNet-web-Daemon-v2 projekthez.  Kattintson a jobb gombbal a projektre a Megoldáskezelő, majd válassza a **Közzététel**lehetőséget.  Kattintson az alsó sávban a **profil importálása** elemre, és importálja a korábban letöltött közzétételi profilt.
1. Kattintson a **configure (Konfigurálás** ) elemre, és a `Connection tab`frissítse a cél URL-címet úgy, hogy az a Kezdőlap URL-címe legyen `https`, például [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Kattintson a **Tovább** gombra.
1. A beállítások lapon győződjön meg arról, hogy a `Enable Organizational Authentication` nincs kiválasztva.  Kattintson a **Save** (Mentés) gombra. Kattintson a **Közzététel** elemre a fő képernyőn.
1. A Visual Studio közzéteszi a projektet, és automatikusan megnyit egy böngészőt a projekt URL-címére.  Ha a projekt alapértelmezett weblapja jelenik meg, a kiadvány sikeres volt.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Az Azure AD-bérlői alkalmazás regisztrálásának frissítése `dotnet-web-daemon-v2`

1. Váltson vissza a [Azure Portal](https://portal.azure.com).
A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást, majd válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Az eredő képernyőn válassza ki a `dotnet-web-daemon-v2` alkalmazást.
1. A **hitelesítésben** | az alkalmazáshoz tartozó oldal, frissítse a kijelentkezési URL-mezőket a szolgáltatás címével, például [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)
1. A *branding (védjegyezés* ) menüben frissítse a **Kezdőlap URL**-címét a szolgáltatás címére, például [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Mentse a konfigurációt.
1. Adja hozzá ugyanazt az URL-címet a *hitelesítés – > átirányítási URI-* k menü értékeinek listájához. Ha több átirányítási URL-címmel rendelkezik, győződjön meg arról, hogy az App Service URI-ja minden átirányítási URL-cím esetében új bejegyzést használ.

## <a name="community-help-and-support"></a>Közösségi Súgó és támogatás

A [stack overflow](http://stackoverflow.com/questions/tagged/msal) segítségével kaphat támogatást a Közösségtől.
Először Kérdezzen rá Stack Overflow és Böngésszen a meglévő problémák között, és ellenőrizze, hogy valaki megkérdezte-e a kérdést.
Győződjön meg arról, hogy a kérdéseit vagy megjegyzéseit a [`adal` `msal` `dotnet`] címkével jelölte meg.

Ha megtalálja és hibát észlel a mintában, próbálja meg a problémát a [GitHub-problémákkal](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues)kapcsolatban.

Ha hibát talál a MSAL.NET-ben, akkor emelje fel a problémát a [MSAL.net GitHub-problémákkal](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues)kapcsolatban.

A javaslatok megadásához látogasson el a következő felhasználói hangvételi [oldalra](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Következő lépések
További információ a Microsoft Identity platform által támogatott különböző [hitelesítési folyamatokról és alkalmazási forgatókönyvekről](authentication-flows-app-scenarios.md) .

További információkért tekintse meg a következő fogalmi dokumentációt:

- [Bérlet Azure Active Directory](single-and-multi-tenant-apps.md)
- [Az Azure AD-alkalmazások hozzájárulási folyamatának ismertetése](application-consent-experience.md)
- [Útmutató: bejelentkezés bármely Azure Active Directory felhasználó számára a több-bérlős alkalmazás mintájának használatával](howto-convert-app-to-be-multi-tenant.md)
- [A felhasználók és a rendszergazdák beleegyezésének ismertetése](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Alkalmazás-és egyszerű szolgáltatások objektumai Azure Active Directory](app-objects-and-service-principals.md)
- [Gyors útmutató: alkalmazás regisztrálása a Microsoft Identity platformmal](quickstart-register-app.md)
- [Gyors útmutató: ügyfélalkalmazás konfigurálása a webes API-k eléréséhez](quickstart-configure-app-access-web-apis.md)
- [Jogkivonat beszerzése az ügyfél hitelesítő adataival rendelkező alkalmazáshoz](msal-client-applications.md)

Egy egyszerűbb, több-bérlős konzol Daemon-alkalmazás esetében tekintse meg a [.net Core Daemon](quickstart-v2-netcore-daemon.md)gyors útmutatóját.
