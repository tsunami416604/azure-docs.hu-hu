---
title: Több-bérlős démon létrehozása, amely a Microsoft identity platform végpontját használja
description: Ebben az oktatóanyagban megtudhatja, hogyan hívhat ja meg az Azure Active Directory által védett ASP.NET webes API-t egy Windows-asztali (WPF) alkalmazásból. A WPF-ügyfél hitelesíti a felhasználót, hozzáférési jogkivonatot kér, és meghívja a webes API-t.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: a4d7030f7a58a6252c6e596fc2c248163694a1e8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880873"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Oktatóanyag: Több-bérlős démon létrehozása, amely a Microsoft identity platform végpontját használja

Ebben az oktatóanyagban megtudhatja, hogyan használhatja a Microsoft identitásplatformot a Microsoft üzleti ügyfelei adatainak eléréséhez egy hosszú ideig futó, nem interaktív folyamat során. A minta démon használja az [OAuth2 ügyfél hitelesítő adatok megadása](v2-oauth2-client-creds-grant-flow.md) egy hozzáférési jogkivonat megszerzéséhez. A démon ezután a jogkivonat segítségével hívja meg a [Microsoft Graphot,](https://graph.microsoft.io) és hozzáférjen a szervezeti adatokhoz.

> [!div class="checklist"]
> * Démonalkalmazás integrálása a Microsoft identitásplatformjával
> * Alkalmazásengedélyek megadása közvetlenül az alkalmazásnak egy rendszergazda által
> * A Microsoft Graph API hívásához hozzon be egy hozzáférési jogkivonatot
> * Hívja meg a Microsoft Graph API-t.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

Az alkalmazás ASP.NET MVC alkalmazásként készült. Az OWIN OpenID Connect köztes szoftvert használja a felhasználók bejelentkezéséhez.  

A mintában szereplő "démon" összetevő egy API-vezérlő, `SyncController.cs`. Amikor a vezérlő neve, lekéri a felhasználók listáját az ügyfél Azure Active Directory (Azure AD) bérlőa a Microsoft Graph.When the controller is called, it pulls in a list of the customer's Azure Active Directory (Azure AD) tenant from Microsoft Graph. `SyncController.cs`egy AJAX-hívás váltja ki a webalkalmazásban. A [.NET microsoftos hitelesítési könyvtárát (MSAL)](msal-overview.md) használja a Microsoft Graph hozzáférési jogkivonatának beszerzéséhez.

>[!NOTE]
> Ha még nem ismeri a Microsoft identitásplatformot, javasoljuk, hogy kezdje a [.NET Core démon rövid](quickstart-v2-netcore-daemon.md)útmutatójával.

## <a name="scenario"></a>Forgatókönyv

Mivel az alkalmazás egy több-bérlős alkalmazás a Microsoft üzleti ügyfelek számára, le kell biztosítania az ügyfelek számára, hogy "regisztráljanak" vagy "összekapcsolják" az alkalmazást a vállalati adataikhoz. A kapcsolati folyamat során a vállalati rendszergazda először közvetlenül az alkalmazásnak adja meg az *alkalmazásengedélyeket,* hogy nem interaktív módon, bejelentkezett felhasználó jelenléte nélkül férhessen hozzá a vállalati adatokhoz. A minta logikájának többsége bemutatja, hogyan érheti el ezt a kapcsolati folyamatot az identitáskezelési platform [rendszergazdai hozzájárulási](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) végpontjának használatával.

![Topológia](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Az ebben a példában használt fogalmakról az [identitásplatform végpontjának ügyfélhitelesítő hitelesítő adatok protokolldokumentációját](v2-oauth2-client-creds-grant-flow.md)olvassa el.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához ebben a rövid útmutatóban a következőkre van szükség:

- [Visual Studio 2017 vagy 2019](https://visualstudio.microsoft.com/downloads/).
- Egy Azure AD-bérlő. További információ: [Hogyan kaphat be egy Azure AD-bérlőt.](quickstart-create-new-tenant.md)
- Egy vagy több felhasználói fiókok az Azure AD-bérlőben. Ez a minta nem működik Microsoft-fiókkal (korábbi windows Live-fiókkal). Ha Microsoft-fiókkal jelentkezett be az [Azure Portalra,](https://portal.azure.com) és még soha nem hozott létre felhasználói fiókot a címtárban, ezt most kell megtennie.

## <a name="clone-or-download-this-repository"></a>A tárház klónozása vagy letöltése

A rendszerhéjból vagy a parancssorból írja be ezt a parancsot:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Vagy [töltse le a mintát egy zip fájlban](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Ez a minta egy projektből rendelkezik. Az alkalmazás regisztrálásához az Azure AD-bérlő, akkor vagy:

- Kövesse a [minta regisztrálása az Azure Active Directory-bérlővel](#register-your-application) című lépéseket, és [konfigurálja a mintát az Azure AD-bérlő használatára.](#choose-the-azure-ad-tenant)
- Olyan PowerShell-parancsfájlok használata, amelyek:
  - *Automatikusan* hozza létre az Azure AD-alkalmazásokat és a kapcsolódó objektumokat (jelszavakat, engedélyeket, függőségeket) az Ön számára.
  - Módosítsa a Visual Studio-projektek konfigurációs fájljait.

Ha szeretné használni az automatizálás:

1. Windows rendszerben futtassa a PowerShellt, és lépjen a klónozott könyvtár gyökerére.
1. Futtassa ezt a parancsot:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Futtassa a parancsfájlt az Azure AD-alkalmazás létrehozásához, és ennek megfelelően konfigurálja a mintaalkalmazás kódját:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   A parancsfájlok futtatásának egyéb módjait az [alkalmazáslétrehozási parancsfájlok ismertetik.](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)

1. Nyissa meg a Visual Studio-megoldást, és válassza az **Indítás** lehetőséget a kód futtatásához.

Ha nem szeretné használni az automatizálást, kövesse a következő szakaszokban ismertetett lépéseket.

### <a name="choose-the-azure-ad-tenant"></a>Válassza ki az Azure AD-bérlőt

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. Ha fiókja egynél több Azure AD-bérlőben van, válassza ki a profilját a lap tetején található menüben, majd válassza a **Címtár váltása lehetőséget.**
1. Módosítsa a portálmunkamenetet a kívánt Azure AD-bérlőre.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Az ügyfélalkalmazás regisztrálása (dotnet-web-daemon-v2)

1. Nyissa meg az [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot a Microsoft identitásplatformján a fejlesztők számára.
1. Válassza **az Új regisztráció lehetőséget.**
1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
   - A **Név szakaszban** adjon meg egy értelmes alkalmazásnevet, amely megjelenik az alkalmazás felhasználói számára. Írja be például **a dotnet-web-daemon-v2 értéket.**
   - A **Támogatott fióktípusok** csoportban válassza a **Fiókok lehetőséget bármely szervezeti címtárban.**
   - Az **Átirányítás URI (nem kötelező)** szakaszban válassza a **Web** elemet a kombinált listában, és írja be a következő átirányítási URI-kat:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**
          
     Ha kettőnél több átirányítási URI-t kell hozzáadnia a **Hitelesítés** lapról, miután az alkalmazás sikeresen létrejött.
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás **áttekintése** lapon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és rögzítse későbbre. Szüksége lesz rá a Visual Studio konfigurációs fájljának konfigurálásához ehhez a projekthez.
1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet. Ezután:
   - A **Speciális beállítások** csoportban állítsa **https://localhost:44316/Account/EndSession**a **Kijelentkezés URL-címét** a-ra.
   - A **Speciális beállítások** > **Implicit támogatás** csoportban válassza az **Access-jogkivonatok** és **azonosítótokenek lehetőséget.** Ez a minta megköveteli, hogy az [implicit támogatási folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezve legyen a felhasználó bejelentkezéséhez és egy API-hívásához.
1. Kattintson a **Mentés** gombra.
1. A **Tanúsítványok & titkos kulcsok** lap **Ügyféltitkos kulcsok** szakaszában válassza az Új **ügyféltitok**lehetőséget. Ezután:

   1. Adjon meg egy kulcsleírást (például **alkalmazástitkos),**
   1. Válassza ki a legfontosabb **időtartamot: 1 év**, 2 **év**vagy soha nem **jár le**.
   1. Kattintson a **Hozzáadás** gombra. 
   1. Amikor megjelenik a kulcsérték, másolja és mentse biztonságos helyre. A projekt Visual Studióban való konfigurálásához később szüksége lesz erre a kulcsra. Nem jelenik meg újra, és nem lehet visszakereshető bármilyen más módon.
1. Az alkalmazás laplistájában válassza az **API-engedélyek lehetőséget.** Ezután:
   1. Nyomja meg **Az engedély hozzáadása** gombot.
   1. Győződjön meg arról, hogy a **Microsoft API-k** lap ki van jelölve.
   1. A **Gyakran használt Microsoft API-k csoportban** válassza a **Microsoft Graph**lehetőséget.
   1. Az **Alkalmazásengedélyek** csoportban győződjön meg arról, hogy a megfelelő engedélyek vannak kiválasztva: **User.Read.All**.
   1. Válassza az **Engedélyek hozzáadása** gombot.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurálja a mintát az Azure AD-bérlő használatára

A következő lépésekben az **Ügyfélazonosító** megegyezik az "alkalmazásazonosító" vagy az **AppId azonosítóval.**

Nyissa meg a megoldást a Visual Studióban a projektek konfigurálásához.

### <a name="configure-the-client-project"></a>Az ügyfélprojekt konfigurálása

Ha a beállítási parancsfájlokat használta, a rendszer a következő módosításokat alkalmazza.

1. Nyissa meg a **UserSync\Web.Config** fájlt.
1. Keresse meg az **alkalmazáskulcs-azonosítót:ClientId**. Cserélje le a meglévő értéket az Azure Portalról másolt **dotnet-web-daemon-v2** alkalmazás alkalmazásazonosítójára.
1. Keresse meg az alkalmazáskulcs **ida:ClientSecret**. Cserélje le a meglévő értéket a **dotnet-web-daemon-v2** alkalmazás létrehozása során az Azure Portalon mentett kulcsra.

## <a name="run-the-sample"></a>Minta futtatása

Tisztítsa meg a megoldást, építse újra a megoldást, futtassa a UserSync alkalmazást, majd jelentkezzen be rendszergazdaként az Azure AD-bérlőben. Ha nem rendelkezik egy Azure AD-bérlő tesztelésre, [kövesse ezeket az utasításokat,](quickstart-create-new-tenant.md) hogy egy.

Amikor bejelentkezik, az alkalmazás először engedélyt kér a bejelentkezéshez, és olvassa el a felhasználói profilját. Ez a hozzájárulás lehetővé teszi, hogy az alkalmazás biztosítsa, hogy Ön üzleti felhasználó.

![Felhasználói beleegyezés](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Az alkalmazás ezután megpróbálja szinkronizálni a felhasználók listáját az Azure AD-bérlő, a Microsoft Graph segítségével. Ha nem, azt kéri, hogy (a bérlői rendszergazda) csatlakoztassa a bérlőt az alkalmazáshoz.

Az alkalmazás ezután engedélyt kér a bérlőben lévő felhasználók listájának olvasásához.

![Rendszergazdai jóváhagyás](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Miután engedélyezte, kijelentkezett az alkalmazásból. Ez a kijelentkezés biztosítja, hogy a Microsoft Graph meglévő hozzáférési jogkivonatai törlődjenek a jogkivonat-gyorsítótárból. Amikor újra bejelentkezik, a beszerzett friss jogkivonat rendelkezik a Microsoft Graph hívásához szükséges engedélyekkel.


Amikor megadja az engedélyt, az alkalmazás ezután bármikor lekérdezheti a felhasználókat. Ezt a **Felhasználók szinkronizálása** gombra kattintva és a felhasználók listájának frissítésével ellenőrizheti. Próbáljon meg hozzáadni vagy eltávolítani egy felhasználót, és szinkronizálja újra a listát. (De vegye figyelembe, hogy az alkalmazás csak a felhasználók első oldalát szinkronizálja.)

## <a name="about-the-code"></a>A kódról

A mintához tartozó kód a következő fájlokban található:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: Kezdeti bejelentkezés. A vezérlőn végrehajtott műveletek rendelkeznek egy **Authorize** attribútummal, amely a felhasználót bejelentkezésre kényszeríti. Az alkalmazás az [engedélyezési kód folyamat](v2-oauth2-auth-code-flow.md) segítségével jelentkezzen be a felhasználó.
- **Vezérlők\SyncController.cs**: A felhasználók listájának szinkronizálása a helyi memóriatárolóval.
- **Controllers\UserController.cs**: A helyi memóriatárolófelhasználóinak listájának megjelenítése.
- **Controllers\AccountController.cs**: Engedélyek beszerzése a bérlői rendszergazdától a rendszergazdai hozzájárulási végpont használatával.

## <a name="re-create-the-sample-app"></a>A mintaalkalmazás újbóli létrehozása

1. A Visual Studio alkalmazásban hozzon létre egy új **Visual C#** **ASP.NET webalkalmazás (.NET Framework)** projektet. 
1. A következő képernyőn válassza az **MVC** projektsablont. Adja hozzá a web **API-hoz**is a mappát és az alapvető hivatkozásokat, mivel később hozzáad egy webes API-vezérlőt. Hagyja a projekt által választott hitelesítési módot alapértelmezettként: **Nincs hitelesítés**.
1. Jelölje ki a projektet a **Megoldáskezelő** ablakban, és válassza az **F4** billentyűt. 
1. A projekt tulajdonságaiban állítsa az **SSL engedélyezve van** **True**értékre. Jegyezze fel az **ssl URL-címben**szereplő információkat. Szüksége lesz rá, amikor konfigurálja az alkalmazás regisztrációját az Azure Portalon.
1. Adja hozzá a következő ASP.NET OWIN middleware NuGet csomagok: 
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Ügyfél 
1. A **App_Start** mappában:
   1. **Hozzon**létre egy Startup.Auth.cs nevű osztályt. 
   1. Távolítsa el **a it. App_Start** a névtér nevéből. 
   1. Cserélje le az **Indítási** osztály kódját a mintaalkalmazás ugyanazon fájljából származó kódra.       
   Ügyeljen arra, hogy az egész osztály definícióját. A definíció **nyilvános osztályindításról** **nyilvános részleges indítási osztályra változik.**
1. A **Startup.Auth.cs**a hiányzó hivatkozásokat a Visual Studio IntelliSense által javasolt utasítások **használatával** oldhatja fel.
1. Kattintson a jobb gombbal a projektre, válassza a **Hozzáadás**parancsot, majd válassza **az Osztály parancsot.**
1. A keresőmezőbe írja be az **OWIN értéket.** **Az OWIN indítási osztály** a kijelölésként jelenik meg. Jelölje ki, és nevezze el az **osztályt Startup.cs.**
1. A **Startup.cs**cserélje le az **Indítási** osztály kódját a mintaalkalmazás ugyanazon fájljából származó kódra. Ismét vegye figyelembe, hogy a definíció nyilvános **osztályindításról** **nyilvános részleges indítási**osztályra változik.
1. A **Modellek** mappában vegyen fel egy új osztályt, **a MsGraphUser.cs**. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
1. Új **MVC 5 vezérlő hozzáadása -** **AccountController**nevű üres példány hozzáadása . Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
1. Új **MVC 5 vezérlő hozzáadása - A** **UserController**nevű üres példány hozzáadása . Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
1. Új **Web API 2 vezérlő hozzáadása – SyncController** nevű üres példány . **SyncController** Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
1. A felhasználói felülethez a **Nézetek\Fiók** mappában adjon hozzá három **Üres (modell nélküli) nézetpéldányt** **GrantPermissions**, **Index**és **UserMismatch**néven. Adjon hozzá egy **Index** et a **Nézetek\Felhasználó** mappába. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
1. A **\_Shared Layout.cshtml** és a **Home\Index.cshtml** fájl frissítése a különböző nézetek megfelelő összekapcsolására.

## <a name="deploy-the-sample-to-azure"></a>A minta üzembe helyezése az Azure-ban

Ez a projekt webalkalmazás- és web API-projektekkel rendelkezik. Az Azure-webhelyeken való üzembe helyezésükhöz tegye a következő lépéseket mindegyikhez:

1. Hozzon létre egy Azure-webhelyet.
1. Tegye közzé a webalkalmazást és a webes API-kat a webhelyen.
1. Frissítse az ügyfeleket, hogy az IIS Express helyett a webhelyet hívják.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Dotnet-web-daemon-v2 létrehozása és közzététele egy Azure-webhelyen

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza az **Erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Válassza a **Web** > **Web App**lehetőséget, majd adjon nevet a webhelynek. Nevezze el például **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Válassza ki az **Előfizetés**, **Erőforrás csoport**, valamint az App service csomag és a **hely adatait.** **Az operációs rendszer** **a Windows**, **a Közzététel** pedig **kód**.
1. Válassza **a Létrehozás lehetőséget,** és várja meg az alkalmazásszolgáltatás létrehozását.
1. Amikor megkapja a **központi telepítés sikeres** értesítést, válassza az Ugrás az **erőforrásra** az újonnan létrehozott alkalmazásszolgáltatás hoz.
1. A webhely létrehozása után keresse meg az **irányítópulton,** és jelölje ki az alkalmazásszolgáltatás **áttekintése** képernyő megnyitásához.
1. Az **alkalmazásszolgáltatás Áttekintés lapján** töltse le a közzétételi profilt a **Közzétételi profil** hivatkozásának kiválasztásával, és mentse azt. Más központi telepítési mechanizmusokat is használhat, például a forrásvezérlőből történő üzembe helyezést.
1. Váltson visual studióra, majd:
   1. Nyissa meg a **dotnet-web-daemon-v2** projektet. 
   1. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza **a Közzététel parancsot.**
   1. Válassza a **Profil importálása** lehetőséget az alsó sávon, és importálja a korábban letöltött közzétételi profilt.
1. Válassza a **Konfigurálás** lehetőséget.
1. A **Kapcsolat** lapon frissítse a cél URL-címét úgy, hogy az "https" legyen. Használja például [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)a használatát. Válassza a **Tovább lehetőséget.**
1. A **Beállítások** lapon győződjön meg arról, hogy a **szervezeti hitelesítés engedélyezése** nincs törölve.  
1. Kattintson a **Mentés** gombra. Válassza a **Közzététel** lehetőséget a főképernyőn.

A Visual Studio közzéteszi a projektet, és automatikusan megnyit egy böngészőt a projekt URL-címére. Ha a projekt alapértelmezett weblapját látja, a kiadvány sikeres volt.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Az Azure AD-bérlői alkalmazás regisztrációjának frissítése a dotnet-web-daemon-v2-hez

1. Lépjen vissza az [Azure Portalra](https://portal.azure.com).
1. A bal oldali ablaktáblában válassza ki az **Azure Active Directory** szolgáltatást, majd válassza az **Alkalmazásregisztrációk**lehetőséget.
1. Válassza ki a **dotnet-web-daemon-v2** alkalmazást.
1. Az alkalmazás **hitelesítése** lapján frissítse a **Kijelentkezés URL-mezőit** a szolgáltatás címével. Használja például [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)a használatát.
1. A **Márkajelzés** menüben frissítse a **kezdőlap URL-címét** a szolgáltatás címére. Használja például [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)a használatát.
1. Mentse a konfigurációt.
1. Adja meg ugyanazt az URL-címet a **Hitelesítési** > **átirányítási URI-k** menü értéklistájához. Ha több átirányítási URL-címekkel rendelkezik, győződjön meg arról, hogy van egy új bejegyzés, amely az alkalmazásszolgáltatás URI-ját használja minden egyes átirányítási URL-hez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje az alkalmazásobjektumot, amelyet az [alkalmazás regisztrálása](#register-your-application) lépésben hozott létre.  Az alkalmazás eltávolításához kövesse az Ön vagy a szervezet által készített alkalmazás eltávolítása című útmutató [utasításait.](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization)

## <a name="get-help"></a>Segítségkérés

Használja [Verem túlcsordulás](http://stackoverflow.com/questions/tagged/msal) kap támogatást a közösségtől.
Először tegye fel kérdéseit a Veremtúlcsordulás sal kapcsolatban, és böngésszen a meglévő problémák között, és nézze meg, hogy valaki korábban feltette-e a kérdését.
Győződjön meg arról, hogy kérdéseit vagy megjegyzéseit "adal", "msal" és "dotnet" címkével látták el.

Ha hibát talál a mintában, kérjük, emelje fel a problémát a [GitHub-problémákkal kapcsolatban.](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues)

Ha hibát talál MSAL.NET, kérjük, vesse fel a problémát [MSAL.NET GitHub problémákkal kapcsolatban.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues)

Javaslat megadásához nyissa meg a [User Voice lapot.](https://feedback.azure.com/forums/169401-azure-active-directory)

## <a name="next-steps"></a>További lépések
További információ a Microsoft identitáskezelési platform által támogatott különböző [hitelesítési folyamatokról és alkalmazásforgatókönyvekről.](authentication-flows-app-scenarios.md)

További információt az alábbi általános dokumentációban talál:

- [Bérlő az Azure Active Directoryban](single-and-multi-tenant-apps.md)
- [Az Azure AD-alkalmazások hozzájárulási folyamatának ismertetése](application-consent-experience.md)
- [Bejelentkezés bármely Azure Active Directory-felhasználóba a több-bérlős alkalmazásmintával](howto-convert-app-to-be-multi-tenant.md)
- [A felhasználók és a rendszergazdai jóváhagyás megismerése](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Alkalmazás- és szolgáltatásnév-objektumok az Azure Active Directoryban](app-objects-and-service-principals.md)
- [Rövid útmutató: Alkalmazás regisztrálása a Microsoft identitásplatformmal](quickstart-register-app.md)
- [Rövid útmutató: Ügyfélalkalmazás konfigurálása webes API-k eléréséhez](quickstart-configure-app-access-web-apis.md)
- [Jogkivonat beolvasása ügyfélhitelesítő adatokkal rendelkező alkalmazáshoz](msal-client-applications.md)

Egyszerűbb több-bérlős konzoldémon-alkalmazásról a [.NET Core démonrövid útmutatóban](quickstart-v2-netcore-daemon.md)lehet része.
