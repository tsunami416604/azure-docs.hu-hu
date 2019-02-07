---
title: Microsoft-bejelentkezés hozzáadása egy ASP.NET-webalkalmazáshoz | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá Microsoft-bejelentkezést egy hagyományos webböngésző-alapú alkalmazással rendelkező ASP.NET-megoldáshoz az OpenID Connect szabvány használatával.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: andret
ms.openlocfilehash: d23110f341cdc76306c5cc4c78ab5d6afb5dd2b3
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816271"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Gyors útmutató: Jelentkezzen be a Microsoft ASP.NET-webalkalmazás hozzáadása

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Ebből az oktatóanyagból megtudhatja, hogyan valósíthat meg Microsoft-bejelentkezést egy hagyományos webböngésző-alapú alkalmazással rendelkező ASP.NET MVC-megoldással, az OpenID Connect használatával. Azt is megtanulhatja, hogyan engedélyezze a munkahelyi és iskolai fiókokról való bejelentkezéseket az ASP.NET-alkalmazásában.

A rövid útmutató végén az alkalmazása el fogja fogadni a bejelentkezéseket az Azure Active Directoryval (Azure AD) integrált szervezetek munkahelyi és iskolai fiókjairól.

> [!NOTE]
> Ha a munkahelyi és iskolai fiókok mellett személyes fiókok számára is engedélyeznie kell a bejelentkezést, használhatja a [2.0-s verziójú végpontot](azure-ad-endpoint-comparison.md). További információért tekintse meg [ezt a 2.0-s verziójú végponthoz készült ASP.NET-oktatóanyagot](tutorial-v2-asp-webapp.md), valamint [ezt a cikket](active-directory-v2-limitations.md), amely a 2.0-s verziójú végpont jelenlegi korlátozásait ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Először is győződjön meg arról, hogy az alábbi előfeltételek teljesülnek:

* A Visual Studio 2015 Update 3-nak vagy a Visual Studio 2017-nek telepítve kell lennie. Nincs telepítve? [Töltse le ingyen a Visual Studio 2017-et](https://www.visualstudio.com/downloads/)

## <a name="scenario-sign-in-users-from-work-and-school-accounts-in-your-aspnet-app"></a>Forgatókönyv: A felhasználók a munkahelyi és iskolai fiókjába az ASP.NET-alkalmazás

![Az útmutató működése](./media/quickstart-v1-aspnet-webapp/aspnet-intro.png)

Ebben a forgatókönyvben egy böngésző hozzáfér egy ASP.NET webhelyhez, és arra kér egy felhasználót, hogy végezzen hitelesítést a bejelentkezés gombbal. A forgatókönyvben a webhely renderelésének nagy része a kiszolgálói oldalon történik.

Ez a rövid útmutató bemutatja, hogyan lehet felhasználókat bejelentkeztetni egy ASP.NET-webalkalmazásba egy üres sablonból kiindulva, és olyan lépéseket tartalmaz, mint például egy bejelentkezés gomb, illetve a vezérlők és metódusok hozzáadása. Emellett a feladatokban szereplő fogalmakat is ismerteti. Másik lehetőségként egy projektet is létrehozhat az Azure AD-felhasználók bejelentkeztetéséhez (munkahelyi és iskolai fiókokról). Ehhez használja a [Visual Studio websablont](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options), és válassza ki a **Szervezeti fiókok** elemet, majd az egyik felhőlehetőséget – ez a lehetőség egy további vezérlőkkel, metódusokkal és nézetekkel rendelkező, gazdagabb sablont használ.

## <a name="libraries"></a>Kódtárak

Ez a rövid útmutató a következő csomagokat használja:

| Kódtár | Leírás |
|---|---|
| [Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) | Közbenső szoftver, amely lehetővé teszi az alkalmazások számára az OpenIdConnect hitelesítésre való használatát |
| [Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies) |Közbenső szoftver, amely lehetővé teszi az alkalmazások számára a felhasználói munkamenet fenntartását cookie-k segítségével |
| [Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb) | Lehetővé teszi az OWIN-alapú alkalmazások számára, hogy az IIS-en fussanak az ASP.NET kérési folyamat használatával |
|  |  |

## <a name="step-1-set-up-your-project"></a>1. lépés: A projekt beállítása

Ezek a lépések bemutatják, hogyan telepítheti és konfigurálhatja a hitelesítési folyamatot az OWIN közbenső szoftverrel egy OpenID Connectet használó ASP.NET-projekten.

Ha inkább a minta Visual Studio-projektjét szeretné letölteni, kövesse az alábbi lépéseket:
1. [Töltse le a projektet a GitHubról](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip).
1. Folytassa a konfigurációs lépés végrehajtása előtt a kód minta konfigurálásához.

## <a name="step-2-create-your-aspnet-project"></a>2. lépés: Az ASP.NET-projekt létrehozása

1. A Visual Studióban lépjen a **File > New > Project** (Fájl > Új > Projekt) lehetőségre.
2. A **Visual C#\Web** területen válassza az **ASP.NET Web Application (.NET Framework)** (ASP.NET-webalkalmazás (.NET-keretrendszer)) lehetőséget.
3. Nevezze el az alkalmazást, és kattintson az **OK** gombra.
4. Válassza ki az **Empty** (Üres) lehetőséget, majd jelölje be a jelölőnégyzetet az **MVC** hivatkozások hozzáadásához.

## <a name="step-3-add-authentication-components"></a>3. lépés: Adja hozzá a hitelesítés összetevői

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

## <a name="step-4-configure-the-authentication-pipeline"></a>4. lépés: A hitelesítési folyamat konfigurálásához

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

## <a name="step-5-add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>5. lépés: Bejelentkezési és kijelentkezési kérések kezelésére vezérlő hozzáadása

Hozzon létre egy új vezérlőt a be- és kijelentkezési metódusok felfedéséhez.

1.  Kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, majd válassza az **Add > Controller** (Hozzáadás > Vezérlő) lehetőséget.
2.  Válassza az **MVC (.NET version) Controller – Empty** (MVC (.NET verzió) vezérlő – Üres) elemet.
3.  Válassza a **Hozzáadás** lehetőséget.
4.  Adja neki a `HomeController` nevet, majd válassza az **Add** (Hozzáadás) lehetőséget.
5.  Adja hozzá az **OWIN** névtereket az osztályhoz:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Adja hozzá a következő metódusokat a vezérlőhöz a be- és kijelentkezés kezeléséhez. Ehhez kezdeményezzen hitelesítési kérdést egy kódon keresztül:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/HomeController.cs?name=SigInAndSignOut "HomeController.cs")]

## <a name="step-6-create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>6. lépés: Hozzon létre a felhasználók keresztül egy bejelentkezés gombot az alkalmazás kezdőlapja

A Visual Studióban hozzon létre egy új nézetet a bejelentkezés gomb hozzáadásához és a felhasználói adatok hitelesítés utáni megjelenítéséhez:

1. Kattintson a jobb gombbal a **Views\Home** mappára, és válassza az **Add View** (Nézet hozzáadása) lehetőséget.
1. Adja neki az **Index** nevet.
1. Adja hozzá a fájlhoz a következő, bejelentkezés gombot tartalmazó HTML-kódot:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse--> Ez a lap hozzáad egy SVG formátumú bejelentkezés gombot, fekete háttérrel:<br/>![Microsoft-bejelentkezés](./media/quickstart-v1-aspnet-webapp/aspnetsigninbuttonsample.png)<br/> A további bejelentkezés gombokért tekintse meg a [Védjegyzési útmutató az alkalmazásokhoz](howto-add-branding-in-azure-ad-apps.md) című cikket.
<!--end-collapse-->

## <a name="step-7-display-users-claims-by-adding-a-controller"></a>7. lépés: Felhasználói jogcímek jelenít meg egy vezérlő hozzáadása

Ez a vezérlő bemutatja, hogy hogyan védheti meg a vezérlőt az `[Authorize]` attribútummal. Ez az attribútum oly módon korlátozza a hozzáférést, hogy csak a hitelesített felhasználókat engedi hozzáférni a vezérlőhöz. A következő kód arra használja az attribútumot, hogy megjelenítse a bejelentkezés során lekért felhasználói jogcímeket.

1. Kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, majd válassza az **Add > Controller** (Hozzáadás > Vezérlő) lehetőséget.
1. Válassza az **MVC {version} Controller – Empty** (MVC {verzió} vezérlő – Üres) elemet.
1. Válassza a **Hozzáadás** lehetőséget.
1. Adja neki a **ClaimsController** nevet.
1. Cserélje le a vezérlőosztály kódját a következő kódra – ez hozzáadja az `[Authorize]` attribútumot az osztályhoz:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Az `[Authorize]` attribútum használata miatt a vezérlő metódusait csak akkor lehet végrehajtani, ha a felhasználó hitelesítve van. Ha a felhasználó nincs hitelesítve, és megpróbál hozzáférni a vezérlőhöz, az OWIN hitelesítési kérdést kezdeményez, és hitelesítésre kényszeríti a felhasználót. A fenti kód megkeresi a felhasználói jogkivonatban található konkrét attribútumokat a felhasználó jogcímgyűjteményében. Ilyen attribútum például a felhasználó teljes neve és felhasználóneve, valamint a globális felhasználóazonosító tárgya. Emellett tartalmazza a *bérlőazonosítót* is, amely a felhasználó szervezetének azonosítóját jelöli.
<!--end-collapse-->

## <a name="step-8-create-a-view-to-display-the-users-claims"></a>8. lépés: A felhasználói jogcímek megjeleníthető nézet létrehozása

A Visual Studióban hozzon létre egy új nézetet a felhasználói jogcímek weboldalon való megjelenítéséhez:

1. Kattintson a jobb gombbal a **Views\Claims** mappára, majd válassza az **Add View** (Nézet hozzáadása) lehetőséget.
1. Adja neki az **Index** nevet.
1. Adja hozzá a következő HTML-kódot a fájlhoz:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]

<!--end-use-->

<!--start-configure-->

## <a name="step-9-configure-your-webconfig-and-register-an-application"></a>9. lépés: Konfigurálja a *web.config* és a egy alkalmazás regisztrálása

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

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Regisztrálja az alkalmazását az Azure Portalon, majd adja hozzá az adatait a *web.config* fájlhoz

1. Az alkalmazás regisztrálásához lépjen a [Microsoft Azure Portal – Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) elemre.
2. Válassza az **Új alkalmazás regisztrálása** elemet.
3. Adja meg az alkalmazás nevét.
4. Illessze be a Visual Studio-projekt *SSL URL-címét* a **Bejelentkezési URL** mezőbe. A rendszer a regisztrált alkalmazás válasz URL-címeinek listájához is automatikusan hozzáadja ezt az URL-címet.
5. Válassza a **Létrehozás** lehetőséget az alkalmazás regisztrálásához. Ez a művelet vissza fogja irányítani az alkalmazások listájára.
6. Ezután keresse meg és/vagy válassza ki az imént létrehozott alkalmazást, és nyissa meg a tulajdonságait.
7. Másolja az **alkalmazásazonosító** alatti GUID-azonosítót a vágólapra.
8. Lépjen vissza a Visual Studióba, és a `web.config` fájlban cserélje le az `Enter_the_Application_Id_here` elemet az imént regisztrált alkalmazás alkalmazásazonosítójára.

> [!TIP]
> Ha a fiók több címtárhoz is hozzáfér, győződjön meg arról, hogy a megfelelő címtárat választotta ki ahhoz a szervezethez, amelyhez regisztrálni szeretné az alkalmazást. Ehhez kattintson az Azure Portal jobb felső sarkában található fióknévre, majd ellenőrizze a kiválasztott címtárat az ábrán látható módon:<br/>![A megfelelő címtár kiválasztása](./media/quickstart-v1-aspnet-webapp/tenantselector.png)

## <a name="step-10-configure-sign-in-options"></a>10. lépés: Bejelentkezési beállítások konfigurálása

Az alkalmazást konfigurálhatja úgy, hogy csak egy adott szervezet Azure AD-példányához tartozó felhasználók számára, illetve bármely szervezet felhasználói számára engedélyezze a bejelentkezést. Kövesse az alábbi lehetőségek egyikének utasításait:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Az alkalmazás konfigurálása oly módon, hogy minden vállalat vagy szervezet munkahelyi és iskolai fiókjairól elfogadja a bejelentkezést (több-bérlős)

Kövesse az alábbi lépéseket, ha el szeretné fogadni a bejelentkezést az összes Azure AD-vel integrált vállalat és szervezet munkahelyi és iskolai fiókjairól. Ez egy gyakori forgatókönyv az *SaaS-alkalmazások* esetén:

1. Térjen vissza a [Microsoft Azure Portal – Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) területre, és keresse meg az imént regisztrált alkalmazást.
2. A **Minden beállítás** területen válassza a **Tulajdonságok** lehetőséget.
3. Állítsa a **Több-bérlős** tulajdonságot **Igen** értékre, majd válassza a **Mentés** lehetőséget.

További információ erről a beállításról és a több-bérlős alkalmazások fogalmáról: [Több-bérlős áttekintés](howto-convert-app-to-be-multi-tenant.md).

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Csak egy adott szervezet Active Directory-példányának felhasználói jelentkezhetnek be az alkalmazásba (egybérlős)

Ez a lehetőség gyakori forgatókönyv az üzletági alkalmazások esetén.

Ha azt szeretné, hogy az alkalmazás csak egy adott Azure AD-példányhoz tartozó fiókokról fogadja el a bejelentkezéseket (a példány *vendégfiókjait* is beleértve), kövesse az alábbi lépéseket:

1. Cserélje le a `Common` *web.config* fájljában található `Tenant` paraméter értékét a szervezet bérlőnevére – például *contoso.onmicrosoft.com*.
1. Állítsa az [*OWIN indítási osztályban*](#configure-the-authentication-pipeline) található `ValidateIssuer` argumentumot `true` értékre.

Ha csak a meghatározott szervezetek listáján szereplő szervezetekhez tartozó felhasználókat szeretné engedélyezni, kövesse az alábbi lépéseket:

1. A `ValidateIssuer` paramétert állítsa igaz értékre.
1. Használja a `ValidIssuers` paramétert egy szervezetlista megadásához.

Másik lehetőségként valósítson meg egy egyéni metódust a kiállítók ellenőrzésére az *IssuerValidator* paraméterrel. A `TokenValidationParameters` osztállyal kapcsolatos további információért tekintse meg [ezt az MSDN-cikket](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN-cikk").

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

A bejelentkezés után a rendszer átirányítja a felhasználót a webhely kezdőlapjára. Ez a Microsoft alkalmazásregisztrációs portálján, az alkalmazása regisztrációs információiban megadott HTTPS URL-cím. Ez a lap most az *Üdvözöljük, {Felhasználó}* feliratot mutatja, valamint egy hivatkozást a kijelentkezéshez, és egy hivatkozást a felhasználói jogcímek megtekintéséhez – ez egy hivatkozás, amely a korábban létrehozott Engedélyezés vezérlőre mutat.

### <a name="see-users-claims"></a>A felhasználói jogcímek megtekintése

A felhasználói jogcímek megtekintéséhez kattintson a hivatkozásra. Ez a művelet átirányítja ahhoz a vezérlőhöz és nézethez, amelyeket csak a hitelesített felhasználók érhetnek el.

#### <a name="expected-results"></a>Várt eredmények

 Megjelenik egy táblázat, benne a bejelentkezett felhasználó alapvető tulajdonságaival:

| Tulajdonság | Érték | Leírás |
|---|---|---|
| Name (Név) | {Felhasználó teljes neve} | A felhasználó vezeték- és utóneve |
| Felhasználónév | <span>user@domain.com</span> | A bejelentkezett felhasználó azonosítására használt felhasználónév |
| Tárgy| {Tárgy} |A felhasználói bejelentkezés weben való egyedi azonosításához használt sztring |
| Bérlőazonosító | {Guid} | Egy *guid* azonosító, amely egyedileg jelöli a felhasználó Azure AD szervezetét |

Emellett egy táblázatot is látni fog a hitelesítési kérésben található összes felhasználói jogcímmel. Az azonosító jogkivonatokban található jogcímek listáját és a hozzájuk tartozó magyarázatokat lásd: [Az azonosító jogkivonatban előforduló jogcímek listája](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="optional-access-a-method-that-has-an-authorize-attribute"></a>(Nem kötelező) Hozzáférés az *[Authorize]* attribútummal rendelkező metódusokhoz

Ebben a lépésben névtelen felhasználóként teszteli a Jogcímek vezérlő elérését:<br/>
A felhasználó kijelentkeztetéséhez kattintson a hivatkozásra, és fejezze be a kijelentkezési folyamatot.<br/>
Ezután írja be a böngészőbe a http://localhost:{port}/claims címet az `[Authorize]` attribútummal védett vezérlő eléréséhez.

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
