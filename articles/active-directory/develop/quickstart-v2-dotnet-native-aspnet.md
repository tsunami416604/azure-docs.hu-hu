---
title: Az Azure AD által védett ASP.NET webes API meghívása – Microsoft Identity
description: Ebből a rövid útmutatóból megtudhatja, hogyan hívhat meg Azure Active Directory által védett ASP.NET webes API-t egy Windows asztali (WPF-) alkalmazásból. A WPF-ügyfél hitelesíti a felhasználót, hozzáférési jogkivonatot kér, és meghívja a webes API-t.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f1d9e402bff9d333957d51982dd917822d2c24d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920650"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-azure-ad"></a>Gyors útmutató: az Azure AD által védett ASP.NET webes API meghívása

Ebben a rövid útmutatóban egy webes API-t tesz elérhetővé, és gondoskodik arról, hogy csak a hitelesített felhasználók férhessenek hozzá. Ez a minta bemutatja, hogyan tehet közzé egy ASP.NET webes API-t, hogy fogadja el a személyes fiókok (például a outlook.com, a live.com és mások) által kiállított jogkivonatokat, valamint a munkahelyi és iskolai fiókokat bármely olyan vállalattól vagy szervezettől, amely integrálva van a Azure Active Directory.

A minta tartalmaz egy Windows Desktop Application (WPF) ügyfelet is, amely bemutatja, hogyan kérhet hozzáférési jogkivonatot a webes API-k eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szüksége:

* Visual Studio 2017 vagy 2019.  Töltse le [ingyenesen a Visual studiót](https://www.visualstudio.com/downloads/).

* Vagy egy [Microsoft-fiók](https://www.outlook.com) vagy [Office 365 fejlesztői program](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>A minta letöltése vagy klónozása

Ezt a mintát a rendszerhéjból vagy parancssorból is klónozott:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

[A mintát zip-fájlként is letöltheti](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>A webes API regisztrálása az alkalmazás regisztrációs portálján

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Válassza ki azt az Azure AD-bérlőt, amelyben létre szeretné hozni az alkalmazásokat

Ha manuálisan szeretné regisztrálni az alkalmazásokat, első lépésként a következőket kell tennie:

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja egynél több Azure AD-bérlőn található, válassza ki a profilt a lap tetején található menü jobb felső sarkában, majd **váltson át a könyvtárra**.
   Módosítsa a portál munkamenetét a kívánt Azure AD-bérlőre.

### <a name="register-the-service-app-todolistservice"></a>A Service app (TodoListService) regisztrálása

1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
1. Válassza az **új regisztráció**lehetőséget.
1. Amikor megjelenik az **Alkalmazás regisztrálása lap**, adja meg az alkalmazás regisztrációs adatait:
   - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `AppModelv2-NativeClient-DotNet-TodoListService`).
   - A **támogatott fióktípus** módosítása **bármely szervezeti címtárbeli fiókra**.
   - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.

1. Az alkalmazás **– Áttekintés** oldalon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később. Ehhez a projekthez a Visual Studio konfigurációs fájlját (`ClientId` `TodoListService\Web.config`) kell konfigurálnia.
1. Válassza ki az **API közzététele** szakaszt, és:
   - Válassza **a hatókör hozzáadása** elemet.
   - fogadja el a javasolt alkalmazás-azonosító URI-t (API://{clientId}) a **Mentés és folytatás** lehetőség kiválasztásával
   - Adja meg a következő paramétereket:
     - a **hatókör neve** használata `access_as_user`
     - Győződjön meg arról, hogy **ki engedélyezheti** a **rendszergazdák és a felhasználók** beállítást
     - a **rendszergazdai engedély megjelenítendő neve** mezőbe írja be a következőt: `Access TodoListService as a user`
     - a **rendszergazdai engedély leírása** mezőbe írja be a következőt: `Accesses the TodoListService Web API as a user`
     - a **felhasználói beleegyező megjelenítendő név** mezőbe írja be a `Access TodoListService as a user`
     - a **felhasználói beleegyezett Leírás** típusa `Accesses the TodoListService Web API as a user`
     - **Állapot** megtartása **engedélyezettként**
     - **Hatókör hozzáadása** lehetőség kiválasztása

### <a name="configure-the-service-and-client-projects-to-match-the-registered-web-api"></a>A szolgáltatás és az ügyfél projektjeinek konfigurálása a regisztrált webes API-nak megfelelően 

1. Nyissa meg a megoldást a Visual Studióban, majd nyissa meg a **web. config** fájlt a **TodoListService** -projekt gyökerében.
1. Cserélje le a `ida:ClientId` paraméter értékét az **ügyfél-azonosítóra (alkalmazás-azonosító)** az alkalmazás regisztrációs portálján az imént regisztrált alkalmazásból.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Adja hozzá az új hatókört a *TodoListClient*app. config fájlhoz.

1. Nyissa meg a **TodoListClient** -projekt gyökérkönyvtárában található **app. config** fájlt, majd ILLESSZE be az **alkalmazás azonosítóját** a *TodoListService* `TodoListServiceScope` paraméter alatt az imént regisztrált alkalmazásba, és cserélje le a karakterláncot `{Enter the Application ID of your TodoListService from the app registration portal}`.

   > Megjegyzés: Ügyeljen arra, hogy a következő formátumot használja:
   >
   > `api://{TodoListService-Application-ID}/access_as_user` 
   >
   >(ahol a {TodoListService-Application-ID} a TodoListService alkalmazás-AZONOSÍTÓját jelölő GUID azonosító.)

## <a name="register-the-client-app-todolistclient"></a>Az ügyfélalkalmazás regisztrálása (TodoListClient)

Ebben a lépésben a *TodoListClient* -projektet úgy konfigurálja, hogy regisztrálja az új alkalmazást az alkalmazás regisztrációs portálján. Azokban az esetekben, amikor az ügyfél és a kiszolgáló *ugyanazt az alkalmazást* veszi figyelembe, csak a "2. lépésben" regisztrált alkalmazást használhatja fel. Ha azt szeretné, hogy a felhasználók a Microsoft személyes fiókjaival jelentkezzenek be, ugyanazt az alkalmazást kell használnia

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Regisztrálja a *TodoListClient* alkalmazást az *alkalmazás regisztrációs portálján*

1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
1. Válassza az **új regisztráció**lehetőséget.
1. Amikor megjelenik az **Alkalmazás regisztrálása lap**, adja meg az alkalmazás regisztrációs adatait:
   - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `NativeClient-DotNet-TodoListClient`).
   - A **támogatott fióktípus** módosítása **bármely szervezeti címtárbeli fiókra**.
   - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás áttekintés lapján válassza a **hitelesítés** szakaszt.
   - Az **átirányítási URL-címek** | **javasolt átirányítási URL-címek a nyilvános ügyfelekhez (mobil, asztali)** szakaszban, az **urn: IETF: WG: OAuth: 2.0: OOB**
   - Kattintson a **Mentés** gombra.
1. Válassza ki az **API-engedélyek** szakaszt
   - Kattintson az **engedély hozzáadása** gombra, majd
   - Válassza a **saját API** -k fület.
   - Az API-k listájában válassza ki a `AppModelv2-NativeClient-DotNet-TodoListService API`vagy a webes API-hoz megadott nevet.
   - Jelölje be a **access_as_user** engedélyt, ha még nincs bejelölve. Ha szükséges, használja a keresőmezőt.
   - Válassza az **engedélyek hozzáadása** gombot

### <a name="configure-your-todolistclient-project"></a>A *TodoListClient* -projekt konfigurálása

1. Az *alkalmazás-regisztrációs portál* **Áttekintés** lapján másolja az **alkalmazás (ügyfél) azonosítójának értékét.**
1. Nyissa meg az **TodoListClient** projekt gyökérkönyvtárában található **app. config** fájlt, majd illessze be az értéket a `ida:ClientId` paraméter értékbe.

## <a name="run-your-project"></a>A projekt futtatása

1. A projekt futtatásához nyomja meg `<F5>`. A *TodoListClient* nyitva kell lennie.
1. Válassza a **Bejelentkezés** lehetőséget a jobb felső sarokban, és jelentkezzen be ugyanazzal a felhasználóval, aki regisztrálta az alkalmazást, vagy egy felhasználó ugyanabban a címtárban.
1. Ha először jelentkezik be, a rendszer kérheti, hogy *TodoListService* a webes API-t.
1. A bejelentkezés a hozzáférési tokent is kéri a *access_as_user* hatókörre a *TodoListService* web API eléréséhez és a *Tennivalók* listájának kezeléséhez.

## <a name="pre-authorize-your-client-application"></a>Az ügyfélalkalmazás előzetes engedélyezése

A webes API-k más címtárakból való elérésének egyik módja a webes API-hoz való hozzáférés *előzetes engedélyezése* az ügyfélalkalmazások számára a webes API-hoz tartozó, *előre engedélyezett* alkalmazások listájában szereplő ügyfélalkalmazások hozzáadásával. Az előfeltételként szükséges ügyfél hozzáadásával a felhasználónak nem kell beleegyeznie a webes API használatára. A webalkalmazás előzetes engedélyezéséhez kövesse az alábbi lépéseket:

1. Lépjen vissza az *alkalmazás regisztrációs portálján* , és nyissa meg a **TodoListService**tulajdonságait.
1. Az **API közzététele** szakaszban kattintson az **ügyfélalkalmazás hozzáadása** a *jóváhagyott ügyfélalkalmazások* szakaszban elemre.
1. Az *ügyfél-azonosító* mezőben illessze be a `TodoListClient` alkalmazás alkalmazás-azonosítóját.
1. A *jogosultsági hatókörök* szakaszban válassza ki a webes API-`api://<Application ID>/access_as_user`hatókörét.
1. Az oldal alján kattintson az **alkalmazás hozzáadása** gombra.

## <a name="run-your-project"></a>A projekt futtatása

1. A projekt futtatásához nyomja meg `<F5>`. A *TodoListClient* nyitva kell lennie.
1. Válassza a **Bejelentkezés** lehetőséget a jobb felső sarokban (vagy törölje a gyorsítótárat vagy a bejelentkezést), majd jelentkezzen be személyes Microsoft-fiók (live.com vagy hotmail.com) vagy munkahelyi vagy iskolai fiók használatával.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Nem kötelező: az alkalmazáshoz való bejelentkezési hozzáférés korlátozása

Alapértelmezés szerint, amikor letölti ezt a kódot, és úgy konfigurálja az alkalmazást, hogy az előző lépéseket követve a Azure Active Directory v2 végpontot használja, a személyes fiókokat (például outlook.com, live.com és egyebeket), valamint a munkahelyi vagy iskolai fiókokat. Az Azure AD-vel integrált szervezetek jogkivonatokat kérhetnek, és hozzáférhetnek a webes API-hoz. 

Ha szeretné korlátozni, hogy ki jelentkezhet be az alkalmazásba, használja a következő lehetőségek egyikét:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>1\. lehetőség: egyetlen szervezethez való hozzáférés korlátozása (egybérlős)

Az alkalmazás bejelentkezési hozzáférését csak egyetlen Azure AD-bérlőben lévő felhasználói fiókokra korlátozhatja, beleértve a bérlő *vendég fiókjait* is. Ez a forgatókönyv az üzletági *alkalmazások*esetében gyakori.

1. Nyissa meg a **App_Start \startup.auth** fájlt, és módosítsa a `OpenIdConnectSecurityTokenProvider` átadott metaadat-végpont értékét `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"`re (a bérlő nevét is használhatja, például `contoso.onmicrosoft.com`).
2. Ugyanebben a fájlban állítsa a `TokenValidationParameters` `ValidIssuer` tulajdonságát a `"https://sts.windows.net/{Tenant ID}/"`re, a `ValidateIssuer` argumentumot pedig `true`ra.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>2\. lehetőség: egyéni módszer használata a kibocsátók érvényesítéséhez

A **IssuerValidator** paraméter használatával egyéni módszert alkalmazhat a kiállítók érvényesítésére. A paraméter használatáról további információt a [TokenValidationParameters osztályról](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet)szóló cikkben olvashat.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések
További információ a Microsoft Identity platform által támogatott védett webes API-forgatókönyvről:
> [!div class="nextstepaction"]
> [Védett webes API-forgatókönyv](scenario-protected-web-api-overview.md)
