---
title: A Microsoft-hitelesítési tár (MSAL) használja a nemzeti Felhőkben – a Microsoft identity platform
description: A Microsoft-hitelesítési tár (MSAL) lehetővé teszi az alkalmazásfejlesztők annak érdekében, hogy a biztonságos webes API-k hívása jogkivonatok beszerzéséhez. A webes API-k a Microsoft Graph, a más Microsoft APIS, a más gyártók webes API-k vagy a saját webes API-t is lehet. Az MSAL több architektúrák és platformokat támogatja.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18eccd6b6d29a43cc3fa13d133d449bf0b9be657
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075790"
---
# <a name="use-msal-in-national-cloud-environment"></a>Használható az MSAL az országos felhőkörnyezet

[Országos felhők](authentication-national-cloud.md) Azure fizikailag izolált példánya. Győződjön meg arról, hogy adatok fizikai tárolási helye, adatszuverenitási és megfelelőségi követelmények összes régió megfelel a földrajzi határokon belüli Azure ezekben a régiókban lettek kialakítva.

Mellett a Microsoft globális felhőalapú a Microsoft-hitelesítési tár (MSAL) is lehetővé teszi az alkalmazásfejlesztők a nemzeti felhőkben hitelesítéséhez és biztonságos webes API-k hívása a tokenek beszerzésére. A webes API-k a Microsoft Graph vagy más Microsoft APIS lehet.

Globális felhő, beleértve az Azure Active Directory (Azure AD) a következő országos felhőkörnyezetekben üzembe helyezett:  

- Amerikai Egyesült Államok kormánya által használt Azure
- Azure China 21Vianet
- Azure Germany

Ez az útmutató azt ismerteti, hogyan jelentkezzen be a munkahelyi és iskolai fiókok, a hozzáférési jogkivonat beszerzése és a Microsoft Graph API meghívása [Microsoft-felhő az USA kormányzati szerveinek biztosított](https://azure.microsoft.com/global-infrastructure/government/) környezetben.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy az Előfeltételek teljesítése.

### <a name="choose-the-appropriate-identities"></a>Válassza ki a megfelelő identitás

[Az Azure Government](https://docs.microsoft.com/azure/azure-government/) alkalmazások segítségével AD az Azure Government-identitásokat, valamint a nyilvános Azure AD-identitások hitelesítheti a felhasználókat. Mivel ezeket az identitásokat bármelyikét használhatja, és döntse el, melyik szolgáltató végpontot kell kiválasztani a forgatókönyvnek szüksége:

- Azure AD nyilvános: Ha a szervezet már rendelkezik egy nyilvános Azure-AD-bérlőt az Office 365 támogatási (nyilvános vagy GCC) vagy egy másik alkalmazás gyakran használják.
- Az Azure AD kormányzati: Ha a szervezet már tartozik egy (nagyon GCC vagy DoD) az Office 365 támogatási AD az Azure Government-bérlő vagy egy új bérlőt hoz AD az Azure Government gyakran használják.

Ha úgy döntött, a különleges figyelmet igényel, ahol az alkalmazás regisztrációs végre. Ha úgy dönt, az Azure Government-alkalmazás Azure AD nyilvános identitásokat, regisztrálnia kell az alkalmazást a nyilvános Azure-AD-bérlőben.

### <a name="get-an-azure-government-subscription"></a>Az Azure Government-előfizetés beszerzése

- Az Azure Government-előfizetés beszerzése, lásd: [kezelése és hogyan csatlakozhat az előfizetését az Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).
- Ha nem rendelkezik egy Azure Government-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/global-infrastructure/government/request/) megkezdése előtt.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.us/) kell regisztrálni egy alkalmazást.
    1. Azure portal-beli más a nemzeti felhőkben való megkereséséhez lásd: [alkalmazás regisztrációs végpontok](authentication-national-cloud.md#app-registration-endpoints)

1. Ha a fiók lehetőséget biztosít több bérlő hozzáférést, a jobb felső sarokban válassza ki a fiókját, és állítsa be a portál munkamenet a kívánt Azure ad-bérlőben.
1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://aka.ms/ra/ff) lapot.
1. Ha a **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét.
1. Alatt **támogatott fióktípusok**válassza **bármely szervezeti directory fiókok**.
1. Alatt a **átirányítási URI-t** szakaszban jelölje be a **webes** platform és az alkalmazás URL-CÍMÉT az értéket a webkiszolgáló alapján állítja be. Tekintse át az utasításokat az alábbi szakaszokban és az átirányítási URL-cím a Visual Studio és a csomópont.
1. Miután végzett, válassza a **Regisztrálás** lehetőséget.
1. Az alkalmazás **áttekintése** lapon, jegyezze fel a **Alkalmazásazonosítót (ügyfél)** értéket.
1. Ehhez az oktatóanyaghoz a [Implicit folyamat megadása](v2-oauth2-implicit-grant-flow.md) engedélyezni kell. A regisztrált alkalmazás bal oldali navigációs panelén válassza **hitelesítési**.
1. A **speciális beállítások**alatt **típusú Implicit engedélyezés**, mindkettő engedélyezéséhez **azonosító-jogkivonatokat** és **hozzáférési jogkivonatokat** jelölőnégyzeteket. Azonosító-jogkivonatokat és hozzáférési tokenek szükség, mivel ez az alkalmazás a felhasználók és a egy API-t kell.
1. Kattintson a **Mentés** gombra.

### <a name="step-2--set-up-your-web-server-or-project"></a>2. lépés:  A webalkalmazás-kiszolgáló vagy a projekt beállítása

- [Töltse le a projektfájlok](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) egy helyi webkiszolgálót, például a csomópont számára.

  vagy

- [Töltse le a Visual Studio-projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

És folytassa a a ["Konfigurálása a JavaScript SPA"](#step-4-configure-your-javascript-spa) a kódminta konfigurálása előtt futtassa a jelentést.

### <a name="step-3-use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>3. lépés: A Microsoft-hitelesítési tár (MSAL) segítségével a felhasználók bejelentkeztetése

Kövesse lépéseket [Javascript oktatóanyag](tutorial-v2-javascript-spa.md#create-your-project) létrehozása a projekthez, és integrálhatja a Microsoft hitelesítési tár (MSAL), jelentkezzen be a felhasználó.

### <a name="step-4-configure-your-javascript-spa"></a>4. lépés: A JavaScript SPA konfigurálása

Az a `index.html` projekt beállítása során létrehozott fájlt, adja hozzá az alkalmazás regisztrációs adatok. Adja meg a következő kódot a felül található a `<script></script>` címkék törzsében a `index.html` fájlt:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Az elemek magyarázata:

- `Enter_the_Application_Id_here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.
- `Enter_the_Tenant_Info_Here` – az alábbi lehetőségek egyike lesz:
    - Ha az alkalmazás támogatja a **ebben a könyvtárban szervezeti fiókok**, cserélje le ezt az értéket a **Bérlőazonosító** vagy **bérlőnevet** (például contoso.microsoft.com)
    - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
    -  A nemzeti felhőkben való hitelesítés végpontokat megkereséséhez lásd: [az Azure AD hitelesítési végpontjai](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

     > [!NOTE]
     > Személyes Microsoft-fiókok (MSA) forgatókönyvek nem támogatottak a nemzeti felhőkben.
  
-   `graphEndpoint` – a Microsoft Graph-végpont a Microsoft cloud Az Egyesült Államok kormányzata számára van.
    -  A Microsoft Graph-végpont a nemzeti felhőkben való megkereséséhez lásd: [országos felhőben a Microsoft Graph-végpont](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="net"></a>.NET

Az MSAL .NET lehetővé teszi a felhasználók, a token beszerzéséhez és a Microsoft Graph API meghívása a nemzeti Felhőkben.

A következő oktatóanyag bemutatja, hogyan hozhat létre egy .NET Core 2.2 MVC webes alkalmazást, amely OpenID Connect használja a "munkahelyi és iskolai" országos felhőkhöz tartozó szervezeti fiókkal rendelkező felhasználók bejelentkeztetéséhez.

- A felhasználók és a token beszerzéséhez kövesse [ebben az oktatóanyagban](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- A Microsoft Graph API meghívása, hajtsa végre a [ebben az oktatóanyagban](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>További lépések

További információk:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)