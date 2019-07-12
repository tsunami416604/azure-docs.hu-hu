---
title: Microsoft-bejelentkezés hozzáadása egy ASP.NET-webalkalmazáshoz | Microsoft Docs
description: Ismerje meg a Microsoft-bejelentkezés hozzáadása egy ASP.NET-megoldás a hagyományos böngészőalapú webalkalmazás standard OpenID Connect használatával.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/21/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0a5f9c14e04e9a0b5b8b48524a425ea94ebf3af
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702987"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Gyors útmutató: Jelentkezzen be a Microsoft ASP.NET-webalkalmazás hozzáadása

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Ebben a rövid útmutatóban fog mutatja be a Microsoft az OpenID Connect használatával hagyományos webhely böngészőalapú alkalmazást egy ASP.NET modell nézet Controller (MVC) megoldás használatával valósíthatók meg. Azt is megtanulhatja, hogyan engedélyezze a munkahelyi és iskolai fiókokról való bejelentkezéseket az ASP.NET-alkalmazásában.

A rövid útmutató végén az alkalmazása el fogja fogadni a bejelentkezéseket az Azure Active Directoryval (Azure AD) integrált szervezetek munkahelyi és iskolai fiókjairól.

> [!NOTE]
> Ha engedélyezi a munkahelyi és iskolai fiókok mellett személyes fiókok bejelentkezések van szüksége, használhatja a  *[a Microsoft identity platform endpoint](azure-ad-endpoint-comparison.md)* . További információ: [Ez az oktatóanyag ASP.NET](tutorial-v2-asp-webapp.md) , valamint [Ez a cikk](active-directory-v2-limitations.md) elmagyarázza a *a Microsoft identity platform endpoint*.

## <a name="prerequisites"></a>Előfeltételek

Először is győződjön meg arról, hogy az alábbi előfeltételek teljesülnek:

* A Visual Studio 2015 Update 3 vagy újabb verziója szükséges. Nincs telepítve? [Töltse le az ingyenes Visual Studio 2019](https://www.visualstudio.com/downloads/)

## <a name="scenario-sign-in-users-from-work-and-school-accounts-in-your-aspnet-app"></a>Forgatókönyv: A felhasználók a munkahelyi és iskolai fiókjába az ASP.NET-alkalmazás

![Az útmutató működése](./media/quickstart-v1-aspnet-webapp/aspnet-intro.png)

Egy böngészőben fér hozzá egy ASP.NET-webhely, és kéri a felhasználót, hogy hitelesítést végezni a bejelentkezési gomb ebben a forgatókönyvben. A forgatókönyvben a webhely renderelésének nagy része a kiszolgálói oldalon történik.

A rövid útmutató azt ismerteti, hogyan jelentkezhet be egy ASP.NET-webalkalmazást, egy üres sablonnal kezdve a felhasználók. Tartalmazza a lépéseket, mint például egy bejelentkezés gombot, és minden vezérlő és módszerek hozzáadása és ezeket a feladatokat a fogalmakat tárgyalja. Egy projekt, jelentkezzen be az Azure AD-felhasználók (munkahelyi és iskolai fiókok esetében) használatával is létrehozhat a [Visual Studio web sablon](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) , és válassza **szervezeti fiókok** és majd közül a felhő - Ez a beállítás további vezérlők, a metódusok és a nézetek egy gazdagabb sablont használja.

## <a name="libraries"></a>Kódtárak

Ez a rövid útmutató a következő csomagokat használja:

| Kódtár | Leírás |
|---|---|
| [Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) | Közbenső szoftver, amely lehetővé teszi az alkalmazások számára az OpenIdConnect hitelesítésre való használatát |
| [Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies) |Közbenső szoftver, amely lehetővé teszi az alkalmazások számára a felhasználói munkamenet fenntartását cookie-k segítségével |
| [Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb) | Lehetővé teszi az OWIN-alapú alkalmazások számára, hogy az IIS-en fussanak az ASP.NET kérési folyamat használatával |
|  |  |

## <a name="step-1-set-up-your-project"></a>1\. lépés: A projekt beállítása

Ezek a lépések bemutatják, hogyan telepítheti és konfigurálhatja a hitelesítési folyamatot az OWIN közbenső szoftverrel egy OpenID Connectet használó ASP.NET-projekten.

Ha inkább a minta Visual Studio-projektjét szeretné letölteni, kövesse az alábbi lépéseket:
1. [Töltse le a projektet a GitHubról](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip).
1. Folytassa a konfigurációs lépés végrehajtása előtt a kód minta konfigurálásához.

## <a name="step-2-create-your-aspnet-project"></a>2\. lépés: Az ASP.NET-projekt létrehozása

1. A Visual Studióban lépjen a **File > New > Project** (Fájl > Új > Projekt) lehetőségre.
2. A **typ Projektu**válassza **webes**, majd **ASP.NET Web Application (.NET Framework)** .
3. Adjon nevet az alkalmazásnak, és válassza ki **létrehozás**.
4. Válassza ki **üres** majd **MVC** alatt **mappák hozzáadása & alapvető hivatkozások** az MVC mutató hivatkozásokat tudjon felvenni.
5. Kattintson a **Létrehozás** gombra.

## <a name="step-3-add-authentication-components"></a>3\. lépés: Adja hozzá a hitelesítés összetevői

1. A Visual Studióban lépjen a **Tools > Nuget Package Manager > Package Manager Console** (Eszközök > NuGet-csomagkezelő > Csomagkezelő konzol) elemre.
2. Az **OWIN közbenső szoftver NuGet-csomagjai** hozzáadásához írja az alábbiakat a Package Manager Console (Csomagkezelő konzol) ablakba:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>A csomagok ismertetése
>A fenti kódtárak engedélyezik az egyszeri bejelentkezést (SSO) az OpenID Connecttel, cookie-alapú hitelesítés használatával. Ha befejeződött a hitelesítés, és a rendszer elküldte a felhasználóhoz tartozó jogkivonatot az alkalmazásnak, az OWIN közbenső szoftver létrehoz egy munkameneti cookie-t. A böngésző ezután ezt a cookie-t használja a későbbi kérésekhez, így a felhasználónak nem kell újból hitelesítenie magát, és nincs szükség további jóváhagyásra.
<!--end-collapse-->

## <a name="step-4-configure-the-authentication-pipeline"></a>4\. lépés: A hitelesítési folyamat konfigurálásához

Kövesse ezeket a lépéseket a közbenső szoftver, az OWIN *indítási osztályának* létrehozásához és az OpenID Connect-hitelesítés konfigurálásához. A rendszer automatikusan végrehajtja ezt az osztályt.

> [!TIP]
> Ha a projekt gyökérmappája nem tartalmazza a `Startup.cs` fájlt:<br/>
> 1. Kattintson a jobb gombbal a projekt gyökérmappájára: >    **Add > New Item... > OWIN Startup class** (Hozzáadás > Új elem... > OWIN indítási osztály)<br/>
> 2. Adja neki a `Startup.cs` nevet.<br/>
>
>> Győződjön meg arról, hogy a kiválasztott osztály egy OWIN indítási osztály, és nem egy szabványos C#-osztály. Ehhez ellenőrizze, hogy látja-e a következőt a névtér fölött: `[assembly: OwinStartup(typeof({NameSpace}.Startup))]`.

Az OWIN közbenső szoftver *indítási osztályának* létrehozása:

1. Adja hozzá az *OWIN* és a *Microsoft.IdentityModel* névtereket a `Startup.cs` fájlhoz:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Cserélje le az indítási osztályt a következő kódra:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Startup.cs?name=Startup "Startup.cs")]

<!--start-collapse-->
> [!NOTE]
> Az *OpenIDConnectAuthenticationOptions* alatt megadott paraméterek koordinátaként szolgálnak az alkalmazás számára az Azure AD-vel való kommunikációhoz. Mivel az OpenID Connect közbenső szoftver cookie-kat használ, cookie-hitelesítést is be kell állítania, ahogy azt a fenti kód is mutatja. A *ValidateIssuer* értéke arra utasítja az OpenIdConnectet, hogy ne korlátozza a hozzáférést egy adott szervezetre.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="step-5-add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>5\. lépés: Bejelentkezési és kijelentkezési kérések kezelésére vezérlő hozzáadása

Hozzon létre egy új vezérlőt a be- és kijelentkezési metódusok felfedéséhez.

1.  Kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, majd válassza az **Add > Controller** (Hozzáadás > Vezérlő) lehetőséget.
2.  Válassza az **MVC {version} Controller – Empty** (MVC {verzió} vezérlő – Üres) elemet.
3.  Válassza a **Hozzáadás** lehetőséget.
4.  Adja neki a `HomeController` nevet, majd válassza az **Add** (Hozzáadás) lehetőséget.
5.  Adja hozzá az **OWIN** névtereket az osztályhoz:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Adja hozzá a következő metódusokat a vezérlőhöz a be- és kijelentkezés kezeléséhez. Ehhez kezdeményezzen hitelesítési kérdést egy kódon keresztül:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/HomeController.cs?name=SigInAndSignOut "HomeController.cs")]

## <a name="step-6-create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>6\. lépés: Hozzon létre a felhasználók keresztül egy bejelentkezés gombot az alkalmazás kezdőlapja

A Visual Studióban hozzon létre egy új nézetet a bejelentkezés gomb hozzáadásához és a felhasználói adatok hitelesítés utáni megjelenítéséhez:

1. Kattintson a jobb gombbal a **Views\Home** mappára, és válassza az **Add View** (Nézet hozzáadása) lehetőséget.
1. Adja neki az **Index** nevet.
1. Adja hozzá a fájlhoz a következő, bejelentkezés gombot tartalmazó HTML-kódot:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
Ezen a lapon fekete háttérrel SVG-formátumban ad hozzá egy bejelentkezés gombot:<br/>![Microsoft-bejelentkezés](./media/quickstart-v1-aspnet-webapp/aspnetsigninbuttonsample.png)<br/> A további bejelentkezés gombokért tekintse meg a [Védjegyzési útmutató az alkalmazásokhoz](howto-add-branding-in-azure-ad-apps.md) című cikket.
<!--end-collapse-->

## <a name="step-7-display-users-claims-by-adding-a-controller"></a>7\. lépés: Felhasználói jogcímek jelenít meg egy vezérlő hozzáadása

Ez a vezérlő bemutatja, hogy hogyan védheti meg a vezérlőt az `[Authorize]` attribútummal. Ez az attribútum oly módon korlátozza a hozzáférést, hogy csak a hitelesített felhasználókat engedi hozzáférni a vezérlőhöz. A következő kód arra használja az attribútumot, hogy megjelenítse a bejelentkezés során lekért felhasználói jogcímeket.

1. Kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, majd válassza az **Add > Controller** (Hozzáadás > Vezérlő) lehetőséget.
1. Válassza az **MVC {version} Controller – Empty** (MVC {verzió} vezérlő – Üres) elemet.
1. Válassza a **Hozzáadás** lehetőséget.
1. Adja neki a **ClaimsController** nevet.
1. Cserélje le a kódot, a vezérlő osztályhoz a következő kóddal – ebben a példában a `[Authorize]` attribútumot az osztályra:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Az `[Authorize]` attribútum használata miatt a vezérlő metódusait csak akkor lehet végrehajtani, ha a felhasználó hitelesítve van. Ha a felhasználó nincs hitelesítve, és megpróbál hozzáférni a vezérlőhöz, az OWIN hitelesítési kérdést kezdeményez, és hitelesítésre kényszeríti a felhasználót. A fenti kód megkeresi a felhasználói jogkivonatban található konkrét attribútumokat a felhasználó jogcímgyűjteményében. Ilyen attribútum például a felhasználó teljes neve és felhasználóneve, valamint a globális felhasználóazonosító tárgya. Emellett tartalmazza a *bérlőazonosítót* is, amely a felhasználó szervezetének azonosítóját jelöli.
<!--end-collapse-->

## <a name="step-8-create-a-view-to-display-the-users-claims"></a>8\. lépés: A felhasználói jogcímek megjeleníthető nézet létrehozása

A Visual Studióban hozzon létre egy új nézetet a felhasználói jogcímek weboldalon való megjelenítéséhez:

1. Kattintson a jobb gombbal a **Views\Claims** mappára, majd válassza az **Add View** (Nézet hozzáadása) lehetőséget.
1. Adja neki az **Index** nevet.
1. Adja hozzá a következő HTML-kódot a fájlhoz:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]

<!--end-use-->

<!--start-configure-->

## <a name="step-9-configure-your-webconfig-and-register-an-application"></a>9\. lépés: Konfigurálja a *web.config* és a egy alkalmazás regisztrálása

1. A Visual Studióban illessze be a következőt a (gyökérmappában található) `configuration\appSettings` fájl `web.config` szakasza alá:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" />
    ```
2. A Megoldáskezelőben válassza ki a projektet, és tekintse meg a <i>Tulajdonságok</i> ablakot (ha nem jelenik meg a Tulajdonságok ablak, nyomja le az F4 billentyűt).
3. Módosítsa az SSL Enabled (SSL engedélyezve) elemet <code>True</code> értékre.
4. Másolja a projekt SSL URL-címét a vágólapra:<br/><br/>![Projekt tulajdonságai](./media/quickstart-v1-aspnet-webapp/visual-studio-project-properties.png)<br />
5. A <code>web.config</code> fájlban cserélje le az <code>Enter_the_Redirect_URL_here</code> elemet a projekt SSL URL-címére.

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Az alkalmazás regisztrálása az Azure Portalon, majd adja meg a saját adatait *web.config*

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com/).
2. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
3. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
4. Válassza ki **új regisztrációs**.
5. Ha a **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét.
6. A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**.
7. Válassza ki a **webes** platform alapján a **átirányítási URI-t** szakaszt, és állítsa az értékét a Visual Studio-projekt *SSL URL-cím* (azt a helyet, amelyhez Azure ad-ben jogkivonatokat ad vissza).
78. Miután végzett, válassza a **Regisztrálás** lehetőséget. Az alkalmazás **áttekintése** lapon, másolja a **Alkalmazásazonosítót (ügyfél)** érték.
9. Lépjen vissza a Visual Studióba, és a `web.config`, cserélje le `Enter_the_Application_Id_here` regisztrálta az alkalmazást, az alkalmazás azonosítójával.

> [!TIP]
> Ha a fiók több címtárhoz is hozzáfér, győződjön meg arról, hogy a megfelelő címtárat választotta ki ahhoz a szervezethez, amelyhez regisztrálni szeretné az alkalmazást. Ehhez kattintson az Azure Portal jobb felső sarkában található fióknévre, majd ellenőrizze a kiválasztott címtárat az ábrán látható módon:<br/>![A megfelelő címtár kiválasztása](./media/quickstart-v1-aspnet-webapp/tenantselector.png)

## <a name="step-10-configure-sign-in-options"></a>10. lépés: Bejelentkezési beállítások konfigurálása

Konfigurálhatja az alkalmazás csak egy szervezet az Azure AD-példányra való bejelentkezéshez tartozó felhasználó, vagy fogadja el a felhasználók bármilyen szervezethez tartozó felhasználók történő bejelentkezések. Kövesse az alábbi lehetőségek egyikét:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Az alkalmazás konfigurálása oly módon, hogy minden vállalat vagy szervezet munkahelyi és iskolai fiókjairól elfogadja a bejelentkezést (több-bérlős)

Kövesse az alábbi lépéseket, ha el szeretné fogadni a bejelentkezést az összes Azure AD-vel integrált vállalat és szervezet munkahelyi és iskolai fiókjairól. Ebben a forgatókönyvben a közös *SaaS-alkalmazások*:

1. Lépjen vissza a [Microsoft Azure portal - alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) , és keresse meg a regisztrált alkalmazás.
2. A **Minden beállítás** területen válassza a **Tulajdonságok** lehetőséget.
3. Állítsa a **Több-bérlős** tulajdonságot **Igen** értékre, majd válassza a **Mentés** lehetőséget.

További információ erről a beállításról és a több-bérlős alkalmazások fogalmáról: [Több-bérlős áttekintés](howto-convert-app-to-be-multi-tenant.md).

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Csak egy adott szervezet Active Directory-példányának felhasználói jelentkezhetnek be az alkalmazásba (egybérlős)

Ez a lehetőség gyakori forgatókönyv az üzletági alkalmazások esetén.

Ha azt szeretné, hogy az alkalmazás csak egy adott Azure AD-példányhoz tartozó fiókokról fogadja el a bejelentkezéseket (a példány *vendégfiókjait* is beleértve), kövesse az alábbi lépéseket:

1. Cserélje le a `Common` *web.config* fájljában található `Tenant` paraméter értékét a szervezet bérlőnevére – például *contoso.onmicrosoft.com*.
1. Állítsa az [*OWIN indítási osztályban*](#step-4-configure-the-authentication-pipeline) található `ValidateIssuer` argumentumot `true` értékre.

Ha csak a meghatározott szervezetek listáján szereplő szervezetekhez tartozó felhasználókat szeretné engedélyezni, kövesse az alábbi lépéseket:

1. A `ValidateIssuer` paramétert állítsa igaz értékre.
1. Használja a `ValidIssuers` paramétert egy szervezetlista megadásához.

Másik lehetőségként valósítson meg egy egyéni metódust a kiállítók ellenőrzésére az *IssuerValidator* paraméterrel. További információ `TokenValidationParameters`, lásd: [MSDN-cikkben](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN-cikkben").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](./media/quickstart-v1-aspnet-webapp/vsprojectproperties.png)<br />
4.  Add the following in `web.config` file located in root’s folder, under section `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
```
-->
<!--end-configure-arp-->
<!--start-test-->

## <a name="step-11-test-your-code"></a>11. lépés: Tesztelheti a kódját

1. Nyomja le az **F5** billentyűt a projekt Visual Studióban való futtatásához. Megnyílik a böngésző, és átirányítja Önt a `http://localhost:{port}` helyre, ahol a **Microsoft-bejelentkezés** gombot láthatja.
1. Kattintson a gombra a bejelentkezéshez.

### <a name="sign-in"></a>Bejelentkezés

Ha készen áll a tesztelésre, jelentkezzen be egy munkahelyi fiókkal (Azure AD).

![Microsoft-bejelentkezés böngészőablak](./media/quickstart-v1-aspnet-webapp/aspnetbrowsersignin.png)

![Microsoft-bejelentkezés böngészőablak](./media/quickstart-v1-aspnet-webapp/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Várt eredmények

Miután a felhasználó bejelentkezik, a rendszer átirányítja a felhasználót a webhely, amely a HTTPS URL-cím, az alkalmazás regisztrációs információit a Microsoft alkalmazásregisztrációs portálon megadott kezdőlapja. Ez a lap most az *Üdvözöljük, {Felhasználó}* feliratot mutatja, valamint egy hivatkozást a kijelentkezéshez, és egy hivatkozást a felhasználói jogcímek megtekintéséhez – ez egy hivatkozás, amely a korábban létrehozott Engedélyezés vezérlőre mutat.

### <a name="see-users-claims"></a>A felhasználói jogcímek megtekintése

A felhasználói jogcímek megtekintéséhez kattintson a hivatkozásra. Ez a művelet átirányítja ahhoz a vezérlőhöz és nézethez, amelyeket csak a hitelesített felhasználók érhetnek el.

#### <a name="expected-results"></a>Várt eredmények

 Megjelenik egy táblázat, benne a bejelentkezett felhasználó alapvető tulajdonságaival:

| Tulajdonság | Érték | Leírás |
|---|---|---|
| Name (Név) | {Felhasználó teljes neve} | A felhasználó vezeték- és utóneve |
| Felhasználónév | <span>user@domain.com</span> | A bejelentkezett felhasználó azonosítására használt felhasználónév |
| Tárgy| {Subject} |A karakterlánc egyedi azonosítására szolgál a felhasználó bejelentkezési a weben |
| Bérlőazonosító | {Guid} | Egy *guid* azonosító, amely egyedileg jelöli a felhasználó Azure AD szervezetét |

Emellett egy táblázatot is látni fog a hitelesítési kérésben található összes felhasználói jogcímmel. Az azonosító jogkivonatokban található jogcímek listáját és a hozzájuk tartozó magyarázatokat lásd: [Az azonosító jogkivonatban előforduló jogcímek listája](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="optional-access-a-method-that-has-an-authorize-attribute"></a>(Nem kötelező) Hozzáférés az *[Authorize]* attribútummal rendelkező metódusokhoz

Ebben a lépésben névtelen felhasználóként teszteli a Jogcímek vezérlő elérését:<br/>
A felhasználó kijelentkeztetéséhez kattintson a hivatkozásra, és fejezze be a kijelentkezési folyamatot.<br/>
Ezután írja be a böngészőbe a `http://localhost:{port}/claims` címet az `[Authorize]` attribútummal védett vezérlő eléréséhez

#### <a name="expected-results"></a>Várt eredmények

Egy hitelesítési felkérést kap a nézet megtekintéséhez.

## <a name="additional-information"></a>További információ

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>A teljes webhely védelme

A teljes webhely védelméhez adja hozzá az `AuthorizeAttribute` elemet a `Global.asax` `Application_Start` metódus `GlobalFilters` eleméhez:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->

## <a name="next-steps"></a>További lépések

Most már továbbléphet a további forgatókönyvekre.

> [!div class="nextstepaction"]
> [ASP.NET-oktatóanyag](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-asp-webapp)
