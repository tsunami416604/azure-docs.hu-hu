---
title: Microsoft identity platform által védett ASP.NET webes API hívása
description: Ebből a rövid útmutatóból megtudhatja, hogyan hívhat ja meg a Microsoft identitásplatformja által védett ASP.NET webes API-t egy Windows Asztali (WPF) alkalmazásból. A WPF-ügyfél hitelesíti a felhasználót, hozzáférési jogkivonatot kér, és meghívja a webes API-t.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 084083a704a007e6675234883c62350d1d9a0849
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536148"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Rövid útmutató: Microsoft-identitásplatform által védett ASP.NET webes API hívása

Ebben a rövid útmutatóban elérhetővé teheti a webes API-t, és megvédheti azt, hogy csak a hitelesített felhasználó férhessen hozzá. Ez a minta bemutatja, hogyan teheti elérhetővé a ASP.NET webes API-t, hogy elfogadhassa a személyes fiókok (beleértve a outlook.com, live.com és másokat), valamint a Microsoft identity platformmal integrált vállalatok vagy szervezetek által kibocsátott jogkivonatokat.

A minta tartalmaz egy Windows Asztali alkalmazás (WPF) ügyfelet is, amely bemutatja, hogyan kérhet hozzáférési jogkivonatot egy webes API eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szükség:

* Visual Studio 2017 vagy 2019.  Töltse le [ingyen a Visual Studio alkalmazást.](https://www.visualstudio.com/downloads/)

* [Microsoft-fiók](https://www.outlook.com) vagy [Office 365 fejlesztői program](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>A minta letöltése vagy klónozása

Ezt a mintát klónozhatja a héjból vagy a parancssorból:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Vagy [letöltheti a mintát ZIP-fájlként.](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)

## <a name="register-your-web-api-in-the-application-registration-portal"></a>A webes API regisztrálása az alkalmazásregisztrációs portálon

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Válassza ki azt az Azure AD-bérlőt, ahol létre szeretné hozni az alkalmazásokat

Ha manuálisan szeretné regisztrálni az alkalmazásokat, első lépésként a következőket kell elvégeznie:

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha fiókja több Azure AD-bérlőben is jelen van, válassza ki a profilját a lap tetején található menü jobb felső sarkában, majd **váltson könyvtárat.**
   Módosítsa a portálmunkamenetet a kívánt Azure AD-bérlőre.

### <a name="register-the-service-app-todolistservice"></a>A szolgáltatásalkalmazás regisztrálása (TodoListService)

1. Keresse meg a Microsoft identity platform ot a fejlesztőknek [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
1. Válassza **az Új regisztráció lehetőséget.**
1. Amikor megjelenik a **Jelentkezési lap regisztrálása,** adja meg a jelentkezési lap regisztrációs adatait:
   - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `AppModelv2-NativeClient-DotNet-TodoListService`).
   - Módosítsa **a támogatott fióktípusokat** **bármely szervezeti címtárban lévő fiókok**ra.
   - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.

1. Az alkalmazás **áttekintése** lapon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és rögzítse későbbre. Szüksége lesz rá a Visual Studio konfigurációs fájljának konfigurálásához ehhez a projekthez (`ClientId` in `TodoListService\Web.config`).
1. Jelölje ki az **API-k at elérhetővé téve** szakaszt, és tegye a következőket:
   - Lehetőség **hozzáadása lehetőséget.**
   - fogadja el a javasolt alkalmazásazonosító-URI-t (api://{clientId}) a **Mentés és folytatás** lehetőség kiválasztásával
   - Adja meg a következő paramétereket:
     - **hatókörnév-használathoz**`access_as_user`
     - Annak biztosítása, hogy a **Rendszergazdák és a felhasználók** lehetőség be van jelölve: Ki **járulhat hozzá**
     - **a Rendszergazda hozzájárulása megjelenítendő név** típusában`Access TodoListService as a user`
     - **a Rendszergazdai hozzájárulás leírása** típusban`Accesses the TodoListService web API as a user`
     - **a Felhasználó hozzájárulása megjelenítendő név** típusában`Access TodoListService as a user`
     - a **Felhasználó hozzájárulásának leírása** típusban`Accesses the TodoListService web API as a user`
     - **Állapot** **engedélyezése maradjon**
     - Lehetőség **hozzáadása lehetőséget.**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>A szolgáltatásprojekt konfigurálása a regisztrált webes API-nak megfelelően

1. Nyissa meg a megoldást a Visual Studióban, majd nyissa meg a **Web.config** fájlt a **TodoListService** projekt gyökere alatt.
1. Cserélje le `ida:ClientId` a paraméter értékét az alkalmazásregisztrációs portálon éppen regisztrált alkalmazás **ügyfélazonosítójával (alkalmazásazonosító).**

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Az új hatókör hozzáadása a *TodoListClient*alkalmazáshoz.config

1. Nyissa meg a **TodoListClient** projekt gyökérmappájában található **app.config** fájlt, majd illessze be az **alkalmazásazonosítót** a `{Enter the Application ID of your TodoListService from the app registration portal}` *TodoListService-hez* regisztrált alkalmazásból a paraméter alatt, `TodoListServiceScope` a karakterlánc helyett .

   > Megjegyzés: Győződjön meg arról, hogy a következő formátumot használja:
   >
   > `api://{TodoListService-Application-ID}/access_as_user`
   >
   >(ahol a (TodoListService-Application-ID} azonosító a TodoListService alkalmazásazonosítóját jelölő GUID).

## <a name="register-the-client-app-todolistclient"></a>Az ügyfélalkalmazás regisztrálása (TodoListClient)

Ebben a lépésben konfigurálhatja a *TodoListClient* projektet egy új alkalmazás regisztrálásával az alkalmazásregisztrációs portálon. Azokban az esetekben, amikor az ügyfél és a kiszolgáló *ugyanazt az alkalmazást* tekintik, akkor is csak újra ugyanazt az alkalmazást regisztrált a "2. Ugyanannak az alkalmazásnak a használata szükséges, ha azt szeretné, hogy a felhasználók microsoftos személyes fiókkal jelentkezzenek be

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>A *TodoListClient* alkalmazás regisztrálása az *alkalmazásregisztrációs portálon*

1. Keresse meg a Microsoft identity platform ot a fejlesztőknek [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
1. Válassza **az Új regisztráció lehetőséget.**
1. Amikor megjelenik a **Jelentkezési lap regisztrálása,** adja meg a jelentkezési lap regisztrációs adatait:
   - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `NativeClient-DotNet-TodoListClient`).
   - Módosítsa **a támogatott fióktípusokat** **bármely szervezeti címtárban lévő fiókok**ra.
   - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás Áttekintés lapján válassza a **Hitelesítés** szakaszt.
   - A**nyilvános ügyfelek (mobil, asztali) átirányítása javasolt URI-k átirányítása** szakaszban ellenőrizze a **Redirect URIs** | **https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Kattintson a **Mentés** gombra.
1. Az **API-engedélyek** szakasz kijelölése
   - Kattintson az **Engedély hozzáadása** gombra, majd
   - Válassza a **Saját API-k** lapot.
   - Az API-k listájában `AppModelv2-NativeClient-DotNet-TodoListService API`válassza ki a vagy a webes API-hoz megadott nevet.
   - Ellenőrizze a **access_as_user** engedélyt, ha még nincs bejelölve. Szükség esetén használja a keresőmezőt.
   - Az **Engedélyek hozzáadása** gomb kijelölése

### <a name="configure-your-todolistclient-project"></a>A *TodoListClient* projekt konfigurálása

1. Az *Alkalmazásregisztrációs portálon*az **Áttekintő** lapon másolja az **alkalmazás (ügyfél) azonosítójának** értékét
1. Nyissa meg a **TodoListClient** projekt gyökérmappájában található **app.config** fájlt, `ida:ClientId` majd illessze be az értéket a paraméterértékbe

## <a name="run-your-project"></a>A projekt futtatása

1. Nyomja `<F5>` meg a projekt futtatásához. Meg kell nyílnia *a TodoListClient* programnak.
1. Válassza a Bejelentkezés lehetőséget a jobb felső **sarokban,** és jelentkezzen be ugyanazzal a felhasználóval, akivel az alkalmazás regisztrálásához használt, vagy egy felhasználóugyanabban a könyvtárban.
1. Ezen a ponton, ha első alkalommal jelentkezik be, a rendszer kérheti, hogy járuljon hozzá a *TodoListService* Web Api-hoz.
1. A bejelentkezés a *todolistservice* webapi eléréséhez és a *teendőlista* kezeléséhez a *access_as_user* hatókör hozzáférési jogkivonatát is kéri.

## <a name="pre-authorize-your-client-application"></a>Az ügyfélalkalmazás előzetes engedélyezése

A más könyvtárakból származó felhasználók számára a webes API elérésének engedélyezésének egyik módja, ha *előzetesen engedélyezi* az ügyfélalkalmazások számára a webes API elérését azáltal, hogy az ügyfélalkalmazások alkalmazásazonosítóit hozzáadja a webes *API-hoz előre engedélyezett* alkalmazások listájához. Egy előre engedélyezett ügyfél hozzáadásával nem kell a felhasználónak beleegyezését adnia a webes API használatához. A webalkalmazás előzetes engedélyezéséhez kövesse az alábbi lépéseket::

1. Lépjen vissza az *alkalmazásregisztrációs portálra,* és nyissa meg a **TodoListService**tulajdonságait.
1. Az **API-k nak kitett** területen kattintson **az Ügyfélalkalmazás hozzáadása** elemre a *Jogosult ügyfélalkalmazások* szakaszban.
1. Az *Ügyfélazonosító* mezőben illessze be az `TodoListClient` alkalmazás azonosítóját.
1. Az *Engedélyezett hatókörök* csoportban válassza ki a `api://<Application ID>/access_as_user`webes API hatókörét.
1. Nyomja meg az **Alkalmazás hozzáadása** gombot a lap alján.

## <a name="run-your-project"></a>A projekt futtatása

1. Nyomja `<F5>` meg a projekt futtatásához. Meg kell nyílnia *a TodoListClient* programnak.
1. Válassza a Bejelentkezés lehetőséget a jobb felső **sarokban** (vagy a Gyorsítótár törlése/bejelentkezés) lehetőséget, majd jelentkezzen be személyes Microsoft-fiókkal (live.com vagy hotmail.com), illetve munkahelyi vagy iskolai fiókkal.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Nem kötelező: Korlátozza az alkalmazásba való bejelentkezési hozzáférést

Alapértelmezés szerint, ha letölti ezt a kódmintát, és konfigurálja az alkalmazást, hogy az Azure Active Directory v2-végpont használata az előző lépéseket követve, mind a személyes fiókok - például outlook.com, live.com és mások -, valamint a munkahelyi vagy iskolai fiókok bármely szervezet, amely integrálva van az Azure AD kérhet tokeneket, és hozzáférhet a webes API-t.

Ha korlátozni szeretné, hogy ki jelentkezhet be az alkalmazásba, használja az egyik lehetőséget:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>1. lehetőség: Egyetlen szervezet hozzáférésének korlátozása (egybérlős)

Korlátozhatja az alkalmazás bejelentkezési hozzáférését csak olyan felhasználói fiókokra, amelyek egyetlen Azure AD-bérlőben találhatók , beleértve az adott bérlő *vendégfiókjait* is. Ez a forgatókönyv gyakori az *üzletági alkalmazások esetében:*

1. Nyissa meg a **App_Start\Startup.Auth** fájlt, és módosítsa a `OpenIdConnectSecurityTokenProvider` beadandó `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` metaadat-végpont értékét (használhatja `contoso.onmicrosoft.com`a bérlői nevet is, például ).
2. Ugyanebben a fájlban `ValidIssuer` állítsa `TokenValidationParameters` a `"https://sts.windows.net/{Tenant ID}/"` tulajdonságot a to és az `ValidateIssuer` argumentum ra. `true`

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>2. lehetőség: Egyéni módszer használata a kibocsátók érvényesítéséhez

A **Kiállítóérvényesítő** paraméter használatával egyéni módszert valósíthat meg a kibocsátók érvényesítésére. A paraméter használatáról a [TokenValidationParameters osztályról](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet)olvashat bővebben.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések
További információ a Microsoft identitáskezelési platform által támogatott védett webes API-forgatókönyvről:
> [!div class="nextstepaction"]
> [Védett webes API-forgatókönyv](scenario-protected-web-api-overview.md)
