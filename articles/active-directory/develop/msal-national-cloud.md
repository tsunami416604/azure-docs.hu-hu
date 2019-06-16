---
title: A Microsoft-hitelesítési tár (MSAL) használja a nemzeti felhőkben – a Microsoft identity platform
description: A Microsoft-hitelesítési tár (MSAL) lehetővé teszi az alkalmazásfejlesztők annak érdekében, hogy a biztonságos webes API-k hívása jogkivonatok beszerzéséhez. A webes API-k a Microsoft Graph, más Microsoft APIs, partneri webes API-k vagy saját webes API-t is lehet. Az MSAL több architektúrák és platformokat támogatja.
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
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235266"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Használható az MSAL az országos felhőkörnyezet

[Országos felhők](authentication-national-cloud.md) Azure fizikailag izolált példánya. Ezekben a régiókban, az Azure segítségével, győződjön meg arról, hogy adatok fizikai tárolási helye, adatszuverenitási és megfelelőségi követelmények összes régió megfelel a földrajzi határokon belül.

A Microsoft globális felhő mellett a Microsoft-hitelesítési tár (MSAL) lehetővé teszi az alkalmazásfejlesztők a nemzeti felhőkben hitelesítéséhez és biztonságos webes API-k hívása a tokenek beszerzésére. A webes API-k a Microsoft Graph vagy más Microsoft APIs lehet.

A globális felhő, beleértve az Azure Active Directory (Azure AD) a következő országos felhők telepítve van:  

- Azure Government
- Azure China 21Vianet
- Azure Germany

Ez az útmutató bemutatja, hogyan lehet bejelentkezni a munkahelyi és iskolai fiókok, hozzáférési token beszerzése és a Microsoft Graph API meghívása a [Azure Government-felhőbeli](https://azure.microsoft.com/global-infrastructure/government/) környezetben.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg róla, hogy megfelelnek-e ezeket az előfeltételeket.

### <a name="choose-the-appropriate-identities"></a>Válassza ki a megfelelő identitás

[Az Azure Government](https://docs.microsoft.com/azure/azure-government/) alkalmazások segítségével AD az Azure Government-identitásokat és az Azure AD nyilvános identitások hitelesítheti a felhasználókat. Mivel ezeket az identitásokat bármelyikét használhatja, el kell döntenie, melyik szolgáltató végpontot kell kiválasztani a forgatókönyvhöz:

- Azure AD nyilvános: Ha a szervezet már rendelkezik egy nyilvános Azure-AD-bérlőt az Office 365 támogatási (nyilvános vagy GCC) vagy egy másik alkalmazás gyakran használják.
- Az Azure AD kormányzati: Ha a szervezet már rendelkezik egy Azure AD valamely kormányzati szervének bérlőjén Office 365 támogatási szolgálatának (GCC magas vagy DoD), vagy hoz létre egy új bérlőt az Azure AD Government gyakran használják.

Miután eldöntötte, különös figyelmet, ahol végre az alkalmazás regisztrációját. Ha úgy dönt, az Azure Government-alkalmazás Azure AD nyilvános identitásokat, regisztrálnia kell az alkalmazást a nyilvános Azure-AD-bérlőben.

### <a name="get-an-azure-government-subscription"></a>Az Azure Government-előfizetés beszerzése

Az Azure Government-előfizetés beszerzése, lásd: [kezelése és csatlakozás az előfizetéshez az Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Ha nem rendelkezik egy Azure Government-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/global-infrastructure/government/request/) megkezdése előtt.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.us/).
    
   Azure portal-beli más a nemzeti felhőkben való megkereséséhez lásd: [alkalmazás regisztrációs végpontok](authentication-national-cloud.md#app-registration-endpoints).

1. Ha a fiók lehetőséget biztosít több bérlő hozzáférést, a jobb felső sarokban válassza ki a fiókját, és állítsa be a portál munkamenet a kívánt Azure ad-bérlőben.
1. Nyissa meg a [alkalmazásregisztrációk](https://aka.ms/ra/ff) oldal a Microsoft identity platform fejlesztők számára.
1. Ha a **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét.
1. Alatt **támogatott fióktípusok**válassza **bármely szervezeti directory fiókok**.
1. Az a **átirányítási URI-t** szakaszban jelölje be a **webes** platform és az alkalmazás URL-CÍMÉT az értéket a webkiszolgáló alapján állítja be. Tekintse át a következő szakaszok útmutatást és az átirányítási URL-cím a Visual Studio és a csomópont.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Az alkalmazás **áttekintése** lapon, jegyezze fel a **Alkalmazásazonosítót (ügyfél)** értéket.
1. Ehhez az oktatóanyaghoz, hogy engedélyezze a [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md). A regisztrált alkalmazás bal oldali panelén válassza **hitelesítési**.
1. A **speciális beállítások**alatt **típusú Implicit engedélyezés**, jelölje be a **azonosító-jogkivonatokat** és **hozzáférési jogkivonatokat** jelölőnégyzeteket. Azonosító-jogkivonatokat és hozzáférési tokenek szükség, mert ez az alkalmazás a felhasználók és a egy API-t kell.
1. Kattintson a **Mentés** gombra.

### <a name="step-2--set-up-your-web-server-or-project"></a>2\. lépés:  A webalkalmazás-kiszolgáló vagy a projekt beállítása

- [Töltse le a projektfájlok](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) egy helyi webkiszolgálót, például a csomópont számára.

  vagy

- [Töltse le a Visual Studio-projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Folytassa a [konfigurálása a JavaScript SPA](#step-4-configure-your-javascript-spa) a kódminta konfigurálása, futtatása előtt.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>3\. lépés: A Microsoft-hitelesítési tár segítségével a felhasználók bejelentkeztetése

Kövesse lépéseket a [JavaScript oktatóanyag](tutorial-v2-javascript-spa.md#create-your-project) létrehozása a projekthez, és integrálása az MSAL bejelentkezni a felhasználó.

### <a name="step-4-configure-your-javascript-spa"></a>4\. lépés: A JavaScript SPA konfigurálása

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

A kódban:

- `Enter_the_Application_Id_here` van a **Alkalmazásazonosítót (ügyfél)** érték, amely regisztrálta.
- `Enter_the_Tenant_Info_Here` beállítása a következő lehetőségek közül:
    - Ha az alkalmazás támogatja a **ebben a könyvtárban szervezeti fiókok**, cserélje le a bérlő Azonosítóját vagy a bérlő neve (például contoso.microsoft.com).
    - Ha az alkalmazás támogatja a **bármely szervezeti directory fiókok**, cserélje le ezt az értéket `organizations`.
    
    A nemzeti felhőkben való hitelesítés végpontokat megkereséséhez lásd: [az Azure AD hitelesítési végpontok](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Személyes Microsoft-fiókok nem támogatottak a nemzeti felhőkben.
  
- `graphEndpoint` a Microsoft Graph-végpont esetében a Microsoft cloud Az Egyesült Államok kormányzata számára van.

   A Microsoft Graph-végpont a nemzeti felhőkben való megkereséséhez lásd: [Microsoft Graph-végpont a nemzeti felhőkben](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

MSAL.NET használatával a felhasználók, szerzi be a jogkivonatokat, és a Microsoft Graph API meghívása a nemzeti felhőkben.

Az alábbi oktatóanyagok bemutatják, hogyan hozhat létre egy .NET Core 2.2 MVC webes alkalmazást. Az alkalmazás használja a OpenID Connect egy munkahelyi és iskolai fiókkal, amelyhez tartozik egy országos felhőbeli szervezeten belüli felhasználók bejelentkeztetéséhez.

- A felhasználók és szerzi be a jogkivonatokat, hajtsa végre a [ebben az oktatóanyagban](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- A Microsoft Graph API meghívása, hajtsa végre a [ebben az oktatóanyagban](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>További lépések

További információk:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)