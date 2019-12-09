---
title: MSAL használata a nemzeti Felhőbeli alkalmazásokban | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazások fejlesztői számára a jogkivonatok beszerzését a biztonságos webes API-k meghívásához. Ezek a webes API-k lehetnek Microsoft Graph, más Microsoft API-k, partner webes API-k vagy a saját webes API-k. A MSAL több alkalmazás-architektúrát és platformot is támogat.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1d0d4511b95d56ae41bf9fbb1118318d8374bde
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916038"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>A MSAL használata nemzeti Felhőbeli környezetben

Az [országos felhők](authentication-national-cloud.md), más néven szuverén felhők, fizikailag elkülönített Azure-példányok. Az Azure ezen régiói segítenek biztosítani, hogy az adattárolási, a szuverenitási és a megfelelőségi követelmények a földrajzi határokon belül legyenek tiszteletben.

A Microsoft globális felhőn kívül a Microsoft Authentication Library (MSAL) lehetővé teszi, hogy az alkalmazások fejlesztői az országos felhőkben jogkivonatokat szerezzenek be a biztonságos webes API-k hitelesítéséhez és meghívásához. Ezek a webes API-k Microsoft Graph vagy más Microsoft API-k lehetnek.

A globális felhővel (Azure Active Directory (Azure AD) együtt a következő nemzeti felhőkben is üzembe helyezhetők:  

- Azure Government
- Azure China 21Vianet
- Azure Germany

Ez az útmutató bemutatja, hogyan jelentkezhet be a munkahelyi és iskolai fiókba, hogyan szerezhet be hozzáférési jogkivonatot, és hogyan hívhatja meg a Microsoft Graph API-t a [Azure Government felhőalapú](https://azure.microsoft.com/global-infrastructure/government/) környezetben.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy megfelel az előfeltételeknek.

### <a name="choose-the-appropriate-identities"></a>A megfelelő identitások kiválasztása

[Azure Government](https://docs.microsoft.com/azure/azure-government/) alkalmazások használhatják az Azure ad Government-identitásokat és az Azure ad nyilvános identitásait a felhasználók hitelesítéséhez. Mivel ezen identitások bármelyikét felhasználhatja, el kell döntenie, hogy melyik szolgáltatói végpontot válassza ki a forgatókönyvhöz:

- Nyilvános Azure AD: gyakran használt, ha a szervezete már rendelkezik Azure AD nyilvános Bérlővel az Office 365 (Public vagy GCC) vagy más alkalmazás támogatásához.
- Azure AD Government: gyakran használt, ha a szervezete már rendelkezik Azure AD Government-Bérlővel az Office 365 (GCC High vagy DoD) támogatásához, vagy egy új bérlőt hoz létre az Azure AD Governmentben.

Miután eldöntötte, hogy elvégezte az alkalmazás regisztrálását, külön figyelmet igényel. Ha az Azure AD nyilvános identitásait választja a Azure Government alkalmazáshoz, regisztrálnia kell az alkalmazást az Azure AD nyilvános bérlőben.

### <a name="get-an-azure-government-subscription"></a>Azure Government előfizetés beszerzése

Azure Government előfizetés beszerzéséhez tekintse meg az [előfizetésének kezelése és csatlakoztatása Azure Government-ban](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)című témakört.

Ha nem rendelkezik Azure Government-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/global-infrastructure/government/request/) a Kezdés előtt.

A nemzeti felhő adott programozási nyelvvel való használatával kapcsolatos részletekért válassza a nyelvének megfelelő fület:

## <a name="nettabdonet"></a>[.NET](#tab/donet)

A MSAL.NET segítségével bejelentkezhet a felhasználókba, megvásárolhatja a jogkivonatokat, és meghívhatja a Microsoft Graph API-t az országos felhőkben.

Az alábbi oktatóanyagok bemutatják, hogyan hozhat létre .NET Core 2,2 MVC-webalkalmazást. Az alkalmazás az OpenID Connect használatával írja be a felhasználókat munkahelyi és iskolai fiókkal egy olyan szervezetbe, amely egy nemzeti felhőhöz tartozik.

- A felhasználók beléptetéséhez és a jogkivonatok beszerzéséhez kövesse az alábbi oktatóanyagot: [hozzon létre egy ASP.net Core webalkalmazás-bejelentkezési felhasználókat a szuverén felhőkben a Microsoft Identity platformmal](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- A Microsoft Graph API meghívásához kövesse az alábbi oktatóanyagot: a [Microsoft Identity platform használatával hívja meg a Microsoft Graph API-t egy ASP.net Core 2. x webalkalmazásból a Microsoft National Cloud szolgáltatásban a munkahelyi és iskolai fiókkal való bejelentkezéshez](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A MSAL. js-alkalmazás szuverén felhőkhöz való engedélyezése:

### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.us/).
    
   Más nemzeti felhők Azure Portal végpontjának megkereséséhez lásd: [alkalmazás-regisztrációs végpontok](authentication-national-cloud.md#app-registration-endpoints).

1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban, és állítsa be a portál munkamenetét a kívánt Azure AD-bérlőre.
1. Nyissa meg a Microsoft Identity platform [Alkalmazásregisztrációk](https://aka.ms/ra/ff) lapját a fejlesztők számára.
1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
1. A **támogatott fiókok típusai**területen válassza **a fiókok lehetőséget bármely szervezeti címtárban**.
1. Az **átirányítási URI** szakaszban válassza ki a **webplatformot** , és állítsa az értéket az alkalmazás URL-címére a webkiszolgáló alapján. A következő részekben megtudhatja, hogyan állíthatja be és kérheti le az átirányítási URL-címet a Visual Studióban és a csomópontban.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét.
1. Ez az oktatóanyag megköveteli az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md)engedélyezését. A regisztrált alkalmazás bal oldali ablaktábláján válassza a **hitelesítés**lehetőséget.
1. A **Speciális beállítások**területén az **implicit engedélyezés**területen jelölje be az **azonosító tokenek** és a **hozzáférési tokenek** jelölőnégyzetet. Az azonosító jogkivonatok és hozzáférési tokenek megadása kötelező, mert az alkalmazásnak be kell jelentkeznie a felhasználókba, és hívnia kell egy API-t.
1. Kattintson a **Mentés** gombra.

### <a name="step-2--set-up-your-web-server-or-project"></a>2\. lépés: a webkiszolgáló vagy projekt beállítása

- [Töltse le a](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) helyi webkiszolgáló (például a csomópont) projektfájl-fájljait.

  vagy

- [Töltse le a Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Ezután ugorjon a [JavaScript-Spa konfigurálásához](#step-4-configure-your-javascript-spa) a kód minta konfigurálásához a futtatása előtt.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>3\. lépés: a felhasználóhoz való bejelentkezéshez használja a Microsoft hitelesítési függvénytárát

Kövesse a [JavaScript oktatóanyag](tutorial-v2-javascript-spa.md#create-your-project) lépéseit a projekt létrehozásához és a MSAL-mel való integrálásához a felhasználónak való bejelentkezéshez.

### <a name="step-4-configure-your-javascript-spa"></a>4\. lépés: a JavaScript SPA konfigurálása

A Project beállítása során létrehozott `index.html` fájlban adja meg az alkalmazás regisztrációs adatait. Adja hozzá az alábbi kódot a `<script></script>` címkén belül a `index.html` fájl törzsében:

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

Ebben a kódban:

- `Enter_the_Application_Id_here` az alkalmazás **(ügyfél) azonosítójának** értéke a regisztrált alkalmazáshoz.
- `Enter_the_Tenant_Info_Here` a következő lehetőségek egyikére van beállítva:
    - Ha az alkalmazás támogatja a **szervezeti címtárban lévő fiókokat**, cserélje le ezt az értéket a BÉRLŐi azonosítóra vagy a bérlő nevére (például contoso.microsoft.com).
    - Ha az alkalmazás **minden szervezeti címtárban támogatja a fiókokat**, cserélje le ezt az értéket `organizations`.
    
    Az összes országos felhőhöz tartozó hitelesítési végpontok megkereséséhez tekintse meg az [Azure ad-hitelesítési végpontokat](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > A személyes Microsoft-fiókok nem támogatottak az országos felhőkben.
  
- a `graphEndpoint` a Microsoft Cloud az Egyesült Államok kormányának Microsoft Graph végpontja.

   Az összes országos felhők Microsoft Graph végpontjának megkereséséhez lásd: [Microsoft Graph végpontok az országos felhőkben](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="pythontabpython"></a>[Python](#tab/python)

A MSAL Python-alkalmazás szuverén felhőkhöz való engedélyezése:

- Az alkalmazás regisztrálása egy adott portálon, a felhőtől függően. A portál kiválasztásával kapcsolatos további információkért tekintse meg az [alkalmazás regisztrációs végpontját](authentication-national-cloud.md#app-registration-endpoints)
- A tárházban található [mintákat](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) a felhőtől függően a konfiguráció néhány változásával, amely a következőben is szerepel.
- Egy adott szolgáltató használata attól függően, hogy milyen felhőben regisztrálta az alkalmazást az alkalmazásban. A különböző Felhőkkel rendelkező hatóságokkal kapcsolatos további információkért tekintse meg az [Azure ad-hitelesítési végpontokat](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Példa a szolgáltatóra:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```
    
- A Microsoft Graph meghívásához egy adott gráf-végpont URL-címe szükséges, amely attól függ, hogy melyik felhőt használja. Az összes országos felhők Microsoft Graph végpontjának megkereséséhez tekintse meg a [Microsoft Graph és a Graph Explorer szolgáltatás gyökérszintű végpontját](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Íme egy példa egy gráf-végpontra, hatókörrel:
    
    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```
    
## <a name="javatabjava"></a>[Java](#tab/java)

A Java-alkalmazás MSAL engedélyezése a szuverén felhők esetében:

- Az alkalmazás regisztrálása egy adott portálon, a felhőtől függően. A portál kiválasztásával kapcsolatos további információkért tekintse meg az [alkalmazás regisztrációs végpontját](authentication-national-cloud.md#app-registration-endpoints)
- A tárházban található [mintákat](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) a felhőtől függően a konfiguráció néhány módosításával, a következővel megemlítve használhatja.
- Egy adott szolgáltató használata attól függően, hogy milyen felhőben regisztrálta az alkalmazást az alkalmazásban. A különböző Felhőkkel rendelkező hatóságokkal kapcsolatos további információkért tekintse meg az [Azure ad-hitelesítési végpontokat](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Példa a szolgáltatóra:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- A Microsoft Graph meghívásához egy adott gráf-végpont URL-címe szükséges, amely attól függ, hogy melyik felhőt használja. Az összes országos felhők Microsoft Graph végpontjának megkereséséhez tekintse meg a [Microsoft Graph és a Graph Explorer szolgáltatás gyökérszintű végpontját](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Íme egy példa egy gráf-végpontra, hatókörrel:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

Az iOS-és macOS-MSAL a jogkivonatok nemzeti felhőkben való beszerzésére használhatók, de a `MSALPublicClientApplication`létrehozásakor további konfigurálásra is szükség van.

Ha például azt szeretné, hogy az alkalmazása több-bérlős alkalmazás legyen egy nemzeti felhőben (itt az USA kormánya), akkor a következőket írhatja:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swifttabswift"></a>[Swift](#tab/swift)

Az iOS-és macOS-MSAL a jogkivonatok nemzeti felhőkben való beszerzésére használhatók, de a `MSALPublicClientApplication`létrehozásakor további konfigurálásra is szükség van.

Ha például azt szeretné, hogy az alkalmazása több-bérlős alkalmazás legyen egy nemzeti felhőben (itt az USA kormánya), akkor a következőket írhatja:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Következő lépések

További információk:

- [Hitelesítés az országos felhőkben](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
