---
title: "Az Azure AD v1 ASP.NET webkiszolgáló bevezetés |} Microsoft Docs"
description: "A Microsoft bejelentkezés végrehajtási egy ASP.NET-megoldás a hagyományos böngészőalapú webalkalmazás a szabványos OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/08/2017
ms.author: andret
ms.openlocfilehash: 7708304dd49535d6881641fcafdbfdcb791f9fdf
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Bejelentkezés a Microsoft ASP.NET webalkalmazás hozzáadása

Ez az útmutató bemutatja, hogyan egy ASP.NET MVC-megoldással egy hagyományos webböngésző-alapú webalkalmazás OpenID Connect használatával a Microsoft bejelentkezési megvalósításához. 

Ez az útmutató végén az alkalmazás fogad a szervezeteknek, amelyek az Azure Active Directory integrálva van a munkahelyi és iskolai fiókok indított bejelentkezések.

> [!NOTE]
> Ez az interaktív telepítés segítséget nyújt az ASP.NET-alkalmazás a munkahelyi és iskolai fiókok bejelentkezések engedélyezéséhez. Ha szüksége ahhoz, hogy a munkahelyi és iskolai fiókok mellett személyes fiókok bejelentkezéseket, használhatja a [v2 végpont](../active-directory-v2-compare.md). Lásd: [az ASP.NET interaktív telepítés v2 végpont](./active-directory-aspnetwebapp.md) , valamint [Ez a dokumentum](../active-directory-v2-limitations.md) a jelenlegi korlátozások, a v2 végpont foglalja össze.
<br/><br/>

<!--separator-->

> Ez az útmutató a Visual Studio 2015 Update 3 vagy a Visual Studio 2017 van szükség.  Nem rendelkezik?  [A Visual Studio 2017 ingyenesen letölthető](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Ez az útmutató működése

![Ez az útmutató működése](media/active-directory-aspnetwebapp-v1/aspnet-intro.png)

Ez az útmutató alapján a forgatókönyvet, ahol a böngésző hozzáfér-e ASP.NET-webhely, a felhasználó egy bejelentkezési gomb segítségével kér. Ebben a forgatókönyvben a lehet megjeleníteni a weblapot a munka nagyobb része a kiszolgáló oldalán következik be.

> [!NOTE]
> Az interaktív telepítés jelentkezzen be az ASP.NET webalkalmazások kezdve egy üres sablont a felhasználók, és mint egy bejelentkezési hozzáadása gombra, és minden tartományvezérlő és módszerek, közben is foglalja össze néhány olyan fogalmat lépéseket mutatja be. Alternativelly, is létrehozhat egy projekt bejelentkezés Azure Active Directory felhasználók (munkahelyi és iskolai fiókok) használatával a [Visual Studio web sablon](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) választja *munkahelyi és iskolai fiókok* és majd közül a felhő - ezt a beállítást gazdagabb sablont használ, a további vezérlők, a metódusok és a nézetek.

## <a name="libraries"></a>Szalagtárak

Ez az útmutató a következő csomagok használja:

|Részletes ismertetés|Leírás|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Alkalmazott szoftver, amely lehetővé teszi az alkalmazás OpenIdConnect használja a hitelesítéshez|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Alkalmazott szoftver, amely lehetővé teszi az alkalmazás felhasználói munkamenet cookie-k használata karbantartása|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Lehetővé teszi, hogy a OWIN-alapú alkalmazások futtatásához az IIS használatával az ASP.NET-kérelem folyamata|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz ismerteti a lépéseket, telepítését és konfigurálását a hitelesítési folyamatot OWIN köztes keresztül egy ASP.NET-projekt OpenID Connect használatával. 

> Ez a minta Visual Studio-projekt letöltése helyette inkább? [Töltse le a projekt](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip) és ugorjon a [konfigurációs lépés](#configure-your-webconfig-and-register-an-application) konfigurálása a kódminta végrehajtása előtt.

## <a name="create-your-aspnet-project"></a>Az ASP.NET-projekt létrehozása
1. A Visual Studio:`File` > `New` > `Project`<br/>
2. A *Visual C# \Web*, jelölje be `ASP.NET Web Application (.NET Framework)`.
3. Az alkalmazás neve, és kattintson a *OK*
4. Válassza ki `Empty` , és válassza a jelölőnégyzet bejelölésével vegye fel `MVC` hivatkozások

## <a name="add-authentication-components"></a>Hitelesítés-összetevők hozzáadása

1. A Visual Studio:`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Adja hozzá *OWIN köztes NuGet-csomagok* , a Package Manager Console ablakban írja be a következő:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>Ezek a csomagok
>A fenti könyvtárak engedélyezése az egyszeri bejelentkezés (SSO) használata az OpenID Connect cookie-alapú hitelesítéssel. Miután a hitelesítés végbemegy, és a felhasználó jelképező jogkivonatot kap az alkalmazáshoz, OWIN köztes egy munkamenetcookie-t hoz létre. A böngésző a felhasználónak nem kell újból hitelesítésre, és nincs további ellenőrzés van szükség, majd használja a cookie későbbi kérelmeket.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>A hitelesítési folyamatot konfigurálása
Az alábbi lépések segítségével hozzon létre egy OWIN köztes *indítási osztály* OpenID Connect hitelesítési konfigurálásához. Ez az osztály automatikusan végrehajtása.

> [!TIP]
> Ha a projekt nem rendelkezik egy `Startup.cs` fájl a gyökérmappában található:<br/>
> 1. Kattintson a jobb gombbal a projekt gyökérmappához: >`Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Nevezze el`Startup.cs`<br/>
>
>> Győződjön meg arról, hogy a kiválasztott osztály egy OWIN indítási osztályt és a nem szabványos C#-osztály. Ellenőrizheti, ha ellenőrzésével `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` fent a névteret.


1. Adja hozzá *OWIN* és *Microsoft.IdentityModel* névterek `Startup.cs`:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Indítási osztály cserélje le a következő kódot:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> Megadja a paraméterek *OpenIDConnectAuthenticationOptions* kommunikálni az Azure AD alkalmazás-koordináták szolgálnak. Mivel az OpenID Connect köztes cookie-kat használ, akkor is be kell állítania cookie-hitelesítés az előző kód látható módon. A *ValidateIssuer* értéke közli OpenIdConnect úgy nem korlátozza a hozzáférést egy adott szervezet számára.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Be- és kijelentkezési kérések kezelésére vezérlő hozzáadása

Ez a lépés bemutatja, hogyan hozzon létre egy új tartományvezérlő teszi közzé a bejelentkezési és kijelentkezési metódusokat.

1.  Kattintson a jobb gombbal a `Controllers` mappára, és válassza`Add` > `Controller`
2.  Válassza a(z) `MVC (.NET version) Controller – Empty` lehetőséget.
3.  Kattintson a *hozzáadása*
4.  Nevezze el `HomeController` kattintson *hozzáadása*
5.  Adja hozzá *OWIN* névterek az osztályra:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Adja hozzá a be- és kijelentkezési a vezérlőhöz kezelni egy hitelesítési kérdés kód lekérhetik a következőkkel:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Az alkalmazás kezdőlapját jelentkezzen be a bejelentkezési gomb felhasználókat létrehozása

A Visual Studio vegye fel a Bejelentkezés gombra, és megjeleníti a felhasználói adatok hitelesítés után új nézet létrehozása:

1.  Kattintson a jobb gombbal a `Views\Home` mappára, és válassza`Add View`
2.  Nevezze el a következőképpen: `Index`.
3.  A következő HTML, amely tartalmazza a Bejelentkezés gombra, adja hozzá a fájlhoz:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> Ezen a lapon egy fekete háttérrel SVG-formátumban ad hozzá a Bejelentkezés gombra:<br/>![Jelentkezzen be Microsoft](media/active-directory-aspnetwebapp-v1/aspnetsigninbuttonsample.png)<br/> Több bejelentkezési gomb, látogasson el [ezen a lapon](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "irányelvek Branding").
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>Felhasználói jogcímek egy tartományvezérlőre való hozzáadásával megjelenítése
Ebben a vezérlőben a használatát mutatja be a `[Authorize]` attribútum egy tartományvezérlőre védelméhez. Ez az attribútum korlátozza a hozzáférést a vezérlő csak a hitelesített felhasználók lehetővé. A következő kódot elérhetővé válnak az attribútum használatával megjelenítheti a felhasználói jogcímeket lekérése, a bejelentkezés részeként.

1.  Kattintson a jobb gombbal a `Controllers` mappába:`Add` > `Controller`
2.  Válassza a(z) `MVC {version} Controller – Empty` lehetőséget.
3.  Kattintson a *hozzáadása*
4.  Nevezze el`ClaimsController`
5.  Cserélje le a vezérlő osztály kódját a következő kódra - Ez a gyorsjavítás a `[Authorize]` attribútumot az osztályra:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Használata miatt a `[Authorize]` csak akkor hajtható végre attribútum, a tartományvezérlő minden módszert, ha a felhasználó hitelesítése. Ha a felhasználó nincs hitelesítve, és megpróbál hozzáférni a tartományvezérlő, OWIN indít el egy hitelesítési kérdés, és a felhasználó hitelesítéséhez. A fenti kódot ellenőrzi, hogy az a felhasználó a felhasználói jogkivonat tartalmazza azokat az attribútumokat jogcímek gyűjteménye. Ezek az attribútumok közé tartozik a felhasználó teljes neve és a felhasználónév, valamint a globális felhasználói azonosító tulajdonos. Is tartalmaz, a *Bérlőazonosító*, amely jelenti, hogy a szervezet a felhasználói Azonosítóját. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>A felhasználói jogcímek megjelenítendő nézet létrehozása

A Visual Studio a felhasználói jogcímek megjelenő weblap új nézet létrehozása:

1.  Kattintson a jobb gombbal a `Views\Claims` mappa és:`Add View`
2.  Nevezze el a következőképpen: `Index`.
3.  Adja hozzá a fájlhoz a következő HTML-KÓDBAN:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>Konfigurálja a *web.config* és egy alkalmazás regisztrálása

1. A Visual Studio adja hozzá a következőt `web.config` (a legfelső szintű mappában található) szakaszban `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. A Megoldáskezelőben, válassza ki a projektet, és tekintse meg a <i>tulajdonságok</i> (Ha nem látja a Tulajdonságok ablak az F4) ablak
3. Módosítsa az SSL engedélyezve<code>True</code>
4. Másolja a vágólapra a projekt SSL URL-címe:<br/><br/>![Projekt tulajdonságai](media/active-directory-aspnetwebapp-v1/visual-studio-project-properties.png)<br />
5. A <code>web.config</code>, cserélje le <code>Enter_the_Redirect_URL_here</code> a SSL URL-címet a projekt 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Az alkalmazás regisztrálása az Azure portálon, majd adja meg az információkat a *web.config*

1. Lépjen a [Microsoft Azure Portal - alkalmazás regisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) alkalmazás regisztrálása
2. A következők szerint válasszon: `New application registration`
3. Adja meg az alkalmazás nevét
4. Illessze be a Visual Studio-projekt *SSL URL-cím* a `Sign-on URL` (Ez az URL is automatikusan bekerül a válasz URL-címek listája az alkalmazás regisztrál)
5. Kattintson a `Create` az alkalmazás regisztrálása. Ez a művelet visszatér alkalmazások listája
6. Most keresése és/vagy válassza ki az imént létrehozott tulajdonságainak megnyitása
7. A GUID-azonosítóját `Application ID` a vágólapra
8. Lépjen vissza a Visual Studio és a `web.config`, cserélje le `Enter_the_Application_Id_here` az alkalmazás imént regisztrálta az alkalmazás azonosítójával

> [!TIP]
> Ha a fiók van konfigurálva több könyvtárban való hozzáférést, győződjön meg arról, hogy kiválasztotta a megfelelő címtárat a szervezethez szeretné-e az alkalmazás felső a fiók nevére kattintva regisztrálni kell az Azure-portálon jobb gombbal, és ezután ellenőrzése a a kijelölt könyvtár:<br/>![A jobb oldali címtárat](media/active-directory-aspnetwebapp-v1/tenantselector.png)

## <a name="configure-sign-in-options"></a>Bejelentkezési lehetőségek konfigurálása

Beállíthatja, hogy az alkalmazás engedélyezése csak az egyik szervezet Azure Active Directory példány bejelentkezési tartozó felhasználók számára, vagy fogadja el a szervezethez tartozó felhasználók bejelentkezések. Kérjük, kövesse az utasításokat a következő lehetőségek egyikét:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Állítsa be az alkalmazását, hogy a vállalat vagy szervezet (több-bérlős) a munkahelyi és iskolai fiókok indított bejelentkezések

Kövesse az alábbi lépéseket, ha azt szeretné, hogy fogadja el a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directoryval integrált bejelentkezések. Ez egy általános forgatókönyv esetében az *SaaS-alkalmazásokhoz*:

1. Lépjen vissza a [Microsoft Azure Portal - alkalmazás regisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) , és keresse meg az imént regisztrált alkalmazás
2. A `All Settings` kiválasztása`Properties`
3. Változás `Multi-tenanted` tulajdonságot `Yes` kattintson`Save`

Ez a beállítás és a több-bérlős alkalmazásokhoz fogalmát kapcsolatos további információkért lásd: [Ez a cikk](../active-directory-devhowto-multi-tenant-overview.md "több-bérlős áttekintése").

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Felhasználók korlátozása csak egy szervezet Active Directory példányból bejelentkezni az alkalmazás (single-bérlő)

Ez a beállítás akkor egy általános forgatókönyv a *LOB-alkalmazások*: Ha szeretné-e az alkalmazás csak az Azure Active Directory adott példányához tartozó fiókok bejelentkezések fogadására (beleértve a *vendégfiókok*, hogy a példány), cserélje le a `Tenant` paramétere *web.config* a `Common` a szervezet – a bérlő neve például *contoso.onmicrosoft.com*. Ezt követően módosíthatja a `ValidateIssuer` argumentumának a [ *OWIN indítási osztály* ](#configure-the-authentication-pipeline) való `true`.

Engedélyezi a felhasználók csak bizonyos szervezetekkel listája, állítsa `ValidateIssuer` true, és használja a `ValidIssuers` paraméter adja meg a szervezetek listáját.

Egy másik lehetőség, hogy alkalmazza a kiállítók használatával érvényesítéséhez egyéni módszer *IssuerValidator* paraméter. További információ `TokenValidationParameters`, lásd: [MSDN-cikkben](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN-cikk").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](media/active-directory-aspnetwebapp-v1/vsprojectproperties.png)<br />
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
## <a name="test-your-code"></a>Tesztelheti a kódját

Nyomja le az `F5` a projektet a Visual Studio futtatásához. A böngészőben megnyílik, és arra utasítja, hogy *http://localhost: {port}* válaszoknál láthatja a *jelentkezzen be Microsoft* gombra. Lépjen tovább, és kattintson rá a bejelentkezéshez.

Amikor készen áll a teszt (az Azure Active Directory) munkahelyi fiókkal való bejelentkezéshez használt. 

![Jelentkezzen be Microsoft-böngészőablakot](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin.png)

![Jelentkezzen be Microsoft-böngészőablakot](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
Bejelentkezés, miután a felhasználó a kezdőlapot, a webhely, amely a HTTPS URL-cím, az alkalmazás regisztrációs adatokat a Microsoft alkalmazás regisztrációs portálon megadott átirányítási. Ezen a lapon látható most *Hello {felhasználó}* és kijelentkezés mutató hivatkozást, és egy hivatkozásra kattintva megtekintheti az a felhasználói jogcímek – ami az engedélyezés vezérlő mutató hivatkozást a korábban létrehozott-e.

### <a name="see-users-claims"></a>Tekintse meg a felhasználói jogcímek
Válassza ki a hivatkozásra kattintva tekintse meg a felhasználói jogcímeket. Ez a művelet részletes útmutatást a tartományvezérlő, tekintse meg, hogy csak hitelesített felhasználók számára elérhető.

#### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
 A bejelentkezett felhasználó alaptulajdonságait tartalmazó táblát kell megjelennie:

| Tulajdonság | Érték | Leírás|
|---|---|---|
| Név | {Felhasználó teljes neve} | A felhasználó nagyapja vezeték- és keresztneve
|Felhasználónév | <span>user@domain.com</span>| A felhasználó azonosítására használt felhasználónév
| Tárgy| {Tulajdonos}|A karakterlánc egyedi azonosítására a weben a felhasználói bejelentkezés|
| Bérlőazonosító| {Guid}| A *guid* a felhasználó Azure Active Directory szervezeti egyedileg ábrázolásához.|

Ezenkívül megjelenik egy táblázatot, beleértve az összes felhasználó szerepel a hitelesítési kérelmet. Az azonosító-tokent és a magyarázat minden jogcím listája: ezzel [cikk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "jogcímek listája az Azonosítót jogkivonatban").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Egy metódust, amelynek elérésekor teszt egy *[engedélyezés]* attribútum (nem kötelező)
Ebben a lépésben meg elérésének tesztelése a jogcímek vezérlő névtelen felhasználóként:<br/>
Válassza ki a Kijelentkezés hivatkozásra a felhasználó, és kijelentkezési folyamat befejezéséhez.<br/>
A böngészőben, a parancssorba írja be a http://localhost: {port} / jogcímeket a tartományvezérlő által védett eléréséhez a `[Authorize]` attribútum

#### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
A parancssorba, nincs szükség, hogy a nézet hitelesítést kell kapnia.

## <a name="additional-information"></a>További információ

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>A teljes webhelyet védelme
A teljes webhelyet védelme érdekében vegye fel a `AuthorizeAttribute` való `GlobalFilters` a `Global.asax` `Application_Start` módszert:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->