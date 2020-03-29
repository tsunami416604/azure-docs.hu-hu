---
title: Az MSAL használata nemzeti felhőalkalmazásban | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazásfejlesztők számára, hogy jogkivonatokat szerezzenek be a biztonságos webes API-k hívásához. Ezek a webes API-k lehetnek a Microsoft Graph, más Microsoft API-k, partnerwebes API-k vagy saját webes API-k. Az MSAL több alkalmazásarchitektúrát és platformot támogat.
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
ms.openlocfilehash: dfca2b1311f1b55f19d5709f7c9ca7c3e366769c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695738"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Az MSAL használata nemzeti felhőkörnyezetben

[A nemzeti felhők](authentication-national-cloud.md), más néven szuverén felhők, fizikailag elszigetelt azure-példányok. Az Azure ezen régiói biztosítják, hogy az adatok tárolási, szuverenitási és megfelelőségi követelményeiföldrajzi határokon belül teljesüljenek.

A Microsoft világméretű felhője mellett a Microsoft Authentication Library (MSAL) lehetővé teszi, hogy a nemzeti felhőkben lévő alkalmazásfejlesztők jogkivonatokat szerezzenek be a biztonságos webes API-k hitelesítéséhez és hívásához. Ezek a webes API-k lehetnek Microsoft Graph vagy más Microsoft API-k.

A globális felhővel együtt az Azure Active Directory (Azure AD) a következő nemzeti felhőkben van telepítve:  

- Azure Government
- Azure China 21Vianet
- Azure Germany

Ez az útmutató bemutatja, hogyan jelentkezz be a munkahelyi és iskolai fiókokba, hogyan kaphat egy hozzáférési jogkivonatot, és hogyan hívhatja meg a Microsoft Graph API-t az [Azure Government felhőalapú](https://azure.microsoft.com/global-infrastructure/government/) környezetben.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy megfelel ezeknek az előfeltételeknek.

### <a name="choose-the-appropriate-identities"></a>Válassza ki a megfelelő identitásokat

[Az Azure Government-alkalmazások](https://docs.microsoft.com/azure/azure-government/) azure AD Government-identitások és az Azure AD nyilvános identitások a felhasználók hitelesítéséhez használhatja. Mivel ezek közül bármelyik identitások közül bármelyiket használhatja, el kell döntenie, hogy melyik jogosultsági végpontot válassza ki a forgatókönyvhöz:

- Azure AD Public: Általánosan használt, ha a szervezet már rendelkezik egy Azure AD nyilvános bérlő az Office 365 (nyilvános vagy GCC) vagy egy másik alkalmazás támogatására.
- Azure AD-kormány: Általánosan használt, ha a szervezet már rendelkezik egy Azure AD-kormány bérlő az Office 365 (GCC High vagy DoD) támogatására, vagy létrehoz egy új bérlőt az Azure AD Government.Azure AD Government: Commonly used if your organization already has a AD Government tenant to support Office 365 (GCC High or DoD) or is creating a new tenant in Azure AD Government.

Miután úgy döntött, külön figyelmet kell fordítania arra, hogy hol hajtja végre az alkalmazásregisztrációt. Ha az Azure AD nyilvános identitások az Azure Government-alkalmazás, regisztrálnia kell az alkalmazást az Azure AD nyilvános bérlő.

### <a name="get-an-azure-government-subscription"></a>Azure Government-előfizetés beszereznie

Ha Azure Government-előfizetést szeretne kapni, olvassa [el az előfizetés kezelése és az előfizetéshez való csatlakozás az Azure Government ben.](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)

Ha nem rendelkezik Azure Government-előfizetéssel, a kezdés előtt hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/global-infrastructure/government/request/)

Ha részletesen itt szeretné használni a nemzeti felhőt egy adott programozási nyelvvel, válassza ki a nyelvének megfelelő lapot:

## <a name="net"></a>[.NET](#tab/donet)

A MSAL.NET segítségével bejelentkezhet a felhasználókba, jogkivonatokat szerezhet be, és nemzeti felhőkben meghívhatja a Microsoft Graph API-t.

Az alábbi oktatóanyagok bemutatják, hogyan hozhat létre .NET Core 2.2 MVC webalkalmazást. Az alkalmazás az OpenID Connect segítségével jelentkezik be a nemzeti felhőhöz tartozó szervezet munkahelyi és iskolai fiókjával rendelkező felhasználókba.

- A felhasználók bejelentkezéséhez és a jogkivonatok beszerzéséhez kövesse ezt az oktatóanyagot: [Hozzon létre egy ASP.NET Core webalkalmazás-bejelentkezési felhasználókat szuverén felhőkben a Microsoft identity platformmal.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)
- A Microsoft Graph API hívásához kövesse a következő [oktatóanyagot: A Microsoft Identity platform használatával hívja meg a Microsoft Graph API-t egy ASP.NET Core 2.x webalkalmazásból, egy olyan felhasználó nevében, aki munkahelyi és iskolai fiókját használja a Microsoft National Cloud szolgáltatásban.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)

## <a name="javascript"></a>[Javascript](#tab/javascript)

Az MSAL.js alkalmazás szuverén felhőkre való engedélyezéséhez:

### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.us/)
    
   Az Azure Portal más nemzeti felhők végpontjainak megkereséséhez tekintse meg [az alkalmazásregisztrációs végpontokat.](authentication-national-cloud.md#app-registration-endpoints)

1. Ha a fiók hozzáférést biztosít egynél több bérlőhöz, válassza ki a fiókját a jobb felső sarokban, és állítsa be a portálmunkamenetet a kívánt Azure AD-bérlőre.
1. Nyissa meg az [Alkalmazásregisztrációk](https://aka.ms/ra/ff) lapot a Microsoft identity platformon a fejlesztők számára.
1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
1. A **Támogatott fióktípusok csoportban**válassza **a Fiókok lehetőséget bármely szervezeti címtárban.**
1. Az **ÁTirányítás URI-csoportban** jelölje ki a **webplatformot,** és állítsa be az értéket az alkalmazás URL-címére a webkiszolgáló alapján. Az átirányítási URL-cím beállítására és beszerzésére vonatkozó utasításokat a következő szakaszokban találja a Visual Studio és a Node alkalmazásban.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosító** értékét.
1. Ez az oktatóanyag megköveteli, hogy engedélyezze az [implicit támogatási folyamatot.](v2-oauth2-implicit-grant-flow.md) A regisztrált alkalmazás bal oldali ablaktáblájában válassza a **Hitelesítés**lehetőséget.
1. A **Speciális**beállítások **csoportban**jelölje be az **Azonosító-jogkivonatok és az Access-jogkivonatok** jelölőnégyzetet. **Access tokens** Azonosító jogkivonatok és hozzáférési jogkivonatok szükségesek, mert ez az alkalmazás kell bejelentkeznie a felhasználók és egy API-t kell hívnia.
1. Kattintson a **Mentés** gombra.

### <a name="step-2--set-up-your-web-server-or-project"></a>2. lépés: Webkiszolgáló vagy projekt beállítása

- [Töltse le a projektfájlokat](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) egy helyi webkiszolgálóra, például a Csomópontra.

  vagy

- [Töltse le a Visual Studio projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Ezután ugorjon [a JavaScript SPA konfigurálása](#step-4-configure-your-javascript-spa) a kódminta konfigurálásához a futtatás előtt.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>3. lépés: A felhasználó bejelentkezéséhez a Microsoft hitelesítési tárával

Kövesse a [JavaScript oktatóanyag](tutorial-v2-javascript-spa.md#create-your-project) lépéseit a projekt létrehozásához és az MSAL-lal való integrációhoz a felhasználó bejelentkezéséhez.

### <a name="step-4-configure-your-javascript-spa"></a>4. lépés: A JavaScript SPA konfigurálása

A `index.html` projekt beállítása során létrehozott fájlban adja meg az alkalmazás regisztrációs adatait. Adja hozzá a következő kódot `<script></script>` a `index.html` fájl törzsében lévő címkék tetején:

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

- `Enter_the_Application_Id_here`a **Application (client) ID** regisztrált alkalmazás alkalmazásazonosító-azonosítóértéke.
- `Enter_the_Tenant_Info_Here`az alábbi lehetőségek egyikére van beállítva:
    - Ha az alkalmazás támogatja **a fiókok ebben a szervezeti címtárban,** cserélje le ezt az értéket a bérlői azonosító vagy a bérlő neve (például contoso.microsoft.com).
    - Ha az alkalmazás támogatja a fiókok at bármely `organizations`szervezeti **címtárban,** cserélje le ezt az értéket a gombra.
    
    Az összes nemzeti felhő hitelesítési végpontjainak megkereséséhez tekintse meg az [Azure AD hitelesítési végpontjait.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

    > [!NOTE]
    > A személyes Microsoft-fiókok nem támogatottak a nemzeti felhőkben.
  
- `graphEndpoint`a Microsoft Graph végpontja a Microsoft felhő az egyesült államokbeli kormány.

   Az összes nemzeti felhő Microsoft Graph-végpontjának megkereséséhez tekintse meg a [Microsoft Graph végpontjait a nemzeti felhőkben.](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="python"></a>[Python](#tab/python)

Az MSAL Python-alkalmazás engedélyezése szuverén felhőkhöz:

- Regisztrálja az alkalmazást egy adott portálon, a felhőtől függően. A portál kiválasztásával kapcsolatos további információkért tekintse meg az [alkalmazásregisztrációs végpontokat](authentication-national-cloud.md#app-registration-endpoints)
- Használja a tárműtérből származó [minták bármelyikét](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) a konfiguráció néhány módosításával, a felhőtől függően, amely a következő említésre kerül.
- Használjon egy adott hatóságot, attól függően, hogy a felhőben regisztrálta az alkalmazást. A különböző felhők hatóságairól az [Azure AD-hitelesítés végpontjaiban](authentication-national-cloud.md#azure-ad-authentication-endpoints)talál további információt.

    Íme egy példa hatóság:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```
    
- A Microsoft Graph hívásához egy adott Graph végpontURL-cím szükséges, amely attól függ, hogy melyik felhőt használja. Az összes nemzeti felhő Microsoft Graph-végpontjának megkereséséhez tekintse meg a Microsoft Graph és a [Graph Explorer szolgáltatás gyökérvégpontjait.](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

    Íme egy példa egy grafikonvégpontra, hatókörrel:
    
    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```
    
## <a name="java"></a>[Java](#tab/java)

Az MSAL java-hoz alkalmazás engedélyezése szuverén felhőkhöz:

- Regisztrálja az alkalmazást egy adott portálon, a felhőtől függően. A portál kiválasztásával kapcsolatos további információkért tekintse meg az [alkalmazásregisztrációs végpontokat](authentication-national-cloud.md#app-registration-endpoints)
- Használja a tárműtérből származó [minták bármelyikét](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) a konfiguráció néhány módosításával, a felhőtől függően, amelyek a következők.
- Használjon egy adott hatóságot, attól függően, hogy a felhőben regisztrálta az alkalmazást. A különböző felhők hatóságairól az [Azure AD-hitelesítés végpontjaiban](authentication-national-cloud.md#azure-ad-authentication-endpoints)talál további információt.

Íme egy példa hatóság:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- A Microsoft Graph hívásához egy adott Graph végpontURL-cím szükséges, amely attól függ, hogy melyik felhőt használja. Az összes nemzeti felhő Microsoft Graph-végpontjának megkereséséhez tekintse meg a Microsoft Graph és a [Graph Explorer szolgáltatás gyökérvégpontjait.](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

Íme egy példa egy grafikonvégpontra, hatókörrel:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Célkitűzés-C](#tab/objc)

Az iOS és a macOS msal-ja felhasználható a nemzeti felhőktokenek `MSALPublicClientApplication`beszerzésére, de további konfigurációt igényel a létrehozásakor.

Ha például azt szeretné, hogy az alkalmazás több-bérlős alkalmazás legyen egy nemzeti felhőben (itt az Egyesült Államok kormánya), akkor a következőket írhatja:

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

## <a name="swift"></a>[Swift](#tab/swift)

Az iOS és a macOS msal-ja felhasználható a nemzeti felhőktokenek `MSALPublicClientApplication`beszerzésére, de további konfigurációt igényel a létrehozásakor.

Ha például azt szeretné, hogy az alkalmazás több-bérlős alkalmazás legyen egy nemzeti felhőben (itt az Egyesült Államok kormánya), akkor a következőket írhatja:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>További lépések

További információk:

- [Hitelesítés nemzeti felhőkben](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
