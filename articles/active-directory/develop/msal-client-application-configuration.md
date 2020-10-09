---
title: Ügyfélalkalmazás konfigurációja (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a nyilvános ügyfél és a bizalmas ügyfélalkalmazások konfigurációs beállításait a Microsoft Authentication Library (MSAL) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: b4595a63613afa3c6fef2fa2a85647d8b70b1388
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81534465"
---
# <a name="application-configuration-options"></a>Alkalmazás konfigurációs beállításai

A kódban egy új nyilvános vagy bizalmas ügyfélalkalmazás (vagy a MSAL.js felhasználói ügynök) inicializálásával hitelesítheti és szerzi be a jogkivonatokat. Az ügyfélalkalmazás a Microsoft Authentication Library (MSAL) szolgáltatásban való inicializálása során több konfigurációs beállítást is beállíthat. Ezek a lehetőségek két csoportba sorolhatók:

- Regisztrációs beállítások, beleértve a következőket:
    - [Authority](#authority) Az alkalmazáshoz tartozó identitás-szolgáltatói [példányból](#cloud-instance) és a bejelentkezési [célközönségből](#application-audience) , és valószínűleg a bérlői azonosítóból áll.
    - [Ügyfél-azonosító](#client-id).
    - [Átirányítási URI](#redirect-uri).
    - [Ügyfél titka](#client-secret) (bizalmas ügyfélalkalmazások esetében).
- [Naplózási beállítások](#logging), beleértve a naplózási szintet, a személyes adatvezérlést és az összetevő nevét a könyvtár használatával.

## <a name="authority"></a>Authority

A szolgáltató egy URL-cím, amely egy olyan könyvtárat jelez, amelyből a MSAL jogkivonatokat kérhet. Gyakori hatóságok:

- https \: //login.microsoftonline.com/ \<tenant\> /, ahol a &lt; bérlő &gt; a Azure Active Directory (Azure ad) bérlő vagy az ehhez az Azure ad-bérlőhöz társított tartomány bérlői azonosítója. Csak egy adott szervezet felhasználói számára való bejelentkezésre használatos.
- https \: //login.microsoftonline.com/common/. Munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal való bejelentkezéshez használatos.
- https \: //login.microsoftonline.com/Organizations/. Munkahelyi és iskolai fiókkal rendelkező felhasználók bejelentkezésére használatos.
- https \: //login.microsoftonline.com/consumers/. Csak személyes Microsoft-fiókkal (korábbi nevén Windows Live ID-fiókokkal) való bejelentkezésre használatos.

A hatóság beállításának konzisztensnek kell lennie az alkalmazás regisztrációs portálján deklarált jelentéssel.

A szolgáltatói URL-cím a példányból és a célközönségből áll.

A szolgáltató a következőket teheti:
- Azure AD-beli felhőalapú szolgáltató.
- Azure AD B2C-szolgáltató. Lásd: [B2C-specifikusak](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Active Directory összevonási szolgáltatások (AD FS) (AD FS) szolgáltató. Lásd: [AD FS támogatás](https://aka.ms/msal-net-adfs-support).

Az Azure AD Cloud hatóságok két részből állnak:
- Az identitás-szolgáltató *példánya*
- Az alkalmazás bejelentkezési *célközönsége*

A példány és a célközönség összefűzve és a szolgáltatói URL-címként adható meg. A MSAL.NET korábbi verzióiban, mint a MSAL 3. *x*-ben saját maga is létre kellett hoznia a hitelesítésszolgáltatót a megcélzott felhő és a bejelentkezési célközönség alapján.  Ez az ábra bemutatja, hogyan áll a szolgáltatói URL-cím:

![A szolgáltatói URL-cím kibontása](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Felhőbeli példány

A *példány* annak megadására szolgál, hogy az alkalmazás a felhasználókat az Azure nyilvános felhőből vagy az országos felhőkből írja-e be. A kódban az MSAL használatával beállíthatja az Azure Cloud-példányt enumerálással, vagy a tag (ha ismeri) URL-címének átadásával a [nemzeti Felhőbeli példányra](authentication-national-cloud.md#azure-ad-authentication-endpoints) `Instance` .

A MSAL.NET explicit kivételt ad, ha mindkettő `Instance` és meg `AzureCloudInstance` van adva.

Ha nem ad meg egy példányt, az alkalmazás az Azure nyilvános Felhőbeli példányát (az URL-példányt) fogja megcélozni `https://login.onmicrosoftonline.com` .

## <a name="application-audience"></a>Alkalmazás célközönsége

A bejelentkezési célközönség az alkalmazás üzleti igényeitől függ:
- Ha Ön üzletági (LOB) fejlesztő, akkor valószínűleg létrehoz egy egybérlős alkalmazást, amelyet csak a szervezete fog használni. Ebben az esetben meg kell adnia a szervezetet, vagy a bérlői AZONOSÍTÓját (az Azure AD-példány AZONOSÍTÓját) vagy az Azure AD-példányhoz társított tartománynevet.
- Ha Ön ISV-t használ, előfordulhat, hogy a felhasználók munkahelyi és iskolai fiókjaikat bármilyen szervezetben vagy szervezeten (több-bérlős alkalmazásban) szeretné bejelentkezni. Előfordulhat azonban, hogy a felhasználók személyes Microsoft-fiókjaikat is be szeretnék jelentkezni.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>A célközönség beállítása a kódban/konfigurációban

A kódban a MSAL használatával a célközönséget a következő értékek egyikével adhatja meg:
- Az Azure AD-szolgáltató célközönségének enumerálása
- A bérlő azonosítója, amely a következőket teheti:
  - Egy egybérlős alkalmazáshoz tartozó GUID (az Azure AD-példány azonosítója)
  - Az Azure AD-példányhoz társított tartománynév (egybérlős alkalmazások esetén is)
- A következő helyőrzők egyike a bérlői AZONOSÍTÓnak az Azure AD-szolgáltató célközönségének enumerálása helyett:
    - `organizations` több-bérlős alkalmazások esetén
    - `consumers` csak a személyes fiókjaik bejelentkezni a felhasználókba
    - `common` Bejelentkezés a felhasználók munkahelyi és iskolai fiókjaival vagy személyes Microsoft-fiókjaival

Ha az Azure AD-szolgáltatót és a bérlői azonosítót is megadja, a MSAL értelmes kivételt fog kiváltani.

Ha nem ad meg célközönséget, az alkalmazás célközönségként fogja megcélozni az Azure AD-t és a személyes Microsoft-fiókokat. (Ez azt eredményezi, hogy a rendszer úgy viselkedik, mintha meg `common` lettek adva.)

### <a name="effective-audience"></a>Hatékony célközönség

Az alkalmazás tényleges célközönsége lesz a minimális (ha van egy metszéspontja) az alkalmazásban beállított célközönségnek és az alkalmazás regisztrációjában megadott célközönségnek. Valójában a [Alkalmazásregisztrációki](https://aka.ms/appregistrations) élmény lehetővé teszi az alkalmazás célközönségének (a támogatott fióktípus) megadását. További információ: gyors útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](quickstart-register-app.md).

Jelenleg csak a személyes Microsoft-fiókkal rendelkező felhasználók jelentkezhetnek be az alkalmazásba, így mindkét beállítást konfigurálhatja:
- Az alkalmazás regisztrációs célközönségének beállítása a következőre: `Work and school accounts and personal accounts` .
- A kód/konfiguráció célközönségének beállítása a `AadAuthorityAudience.PersonalMicrosoftAccount` következőre: (vagy `TenantID` = "consumers").

## <a name="client-id"></a>Ügyfél-azonosító

Az ügyfél-azonosító az alkalmazáshoz az Azure AD által az alkalmazás regisztrálásakor hozzárendelt egyedi alkalmazás-(ügyfél-) azonosító.

## <a name="redirect-uri"></a>Átirányítási URI

Az átirányítási URI az az URI, amelyet az identitás-szolgáltató küld vissza a biztonsági jogkivonatoknak.

### <a name="redirect-uri-for-public-client-apps"></a>Nyilvános ügyfélalkalmazások átirányítási URI-ja

Ha Ön a MSAL-t használó nyilvános ügyfélalkalmazás-fejlesztő:
- `.WithDefaultRedirectUri()`Asztali vagy UWP alkalmazásokban szeretné használni (MSAL.net 4.1 +). Ezzel a módszerrel a nyilvános ügyfélalkalmazás átirányítási URI-tulajdonságát a nyilvános ügyfélalkalmazások alapértelmezett ajánlott átirányítási URI-ja alapján állítja be a rendszer.

  Platform  | Átirányítási URI
  ---------  | --------------
  Asztali alkalmazás (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient`
  UWP | a értéke `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` . Ez lehetővé teszi az egyszeri bejelentkezést a böngészőben úgy, hogy az értéket a WebAuthenticationBroker. GetCurrentApplicationCallbackUri () értékre állítja be, amelyet regisztrálnia kell
  .NET Core | `https://localhost`. Ez lehetővé teszi, hogy a felhasználó a rendszerböngészőt interaktív hitelesítésre használja, mivel a .NET Core jelenleg nem rendelkezik felhasználói felülettel a beágyazott webes nézethez.

- Nem szükséges átirányítási URI-t felvennie, ha olyan Xamarin Android-és iOS-alkalmazást készít, amely nem támogatja a közvetítőt (az átirányítási URI automatikusan a `msal{ClientId}://auth` Xamarin Android és iOS rendszerre van beállítva)

- Konfigurálnia kell az átirányítási URI-t a [Alkalmazásregisztrációkban](https://aka.ms/appregistrations):

   ![Átirányítási URI a Alkalmazásregisztrációkban](media/msal-client-application-configuration/redirect-uri.png)

Felülbírálhatja az átirányítási URI-t a `RedirectUri` tulajdonság használatával (például brókerek használata esetén). Íme néhány példa a forgatókönyv átirányítási URI-azonosítóra:

- `RedirectUriOnAndroid` = "msauth-5a434691-CCB2-4fd1-b97b-b64bcfbc03fc://com.microsoft.Identity.Client.Sample";
- `RedirectUriOnIos` = $ "msauth. {Bundle. ID}://auth ";

További iOS-részletekért lásd: [Microsoft Authenticatort használó iOS-alkalmazások migrálása a ADAL.net-ből a MSAL.net](msal-net-migration-ios-broker.md) és [az iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)-es bróker kihasználása.
További Android-részletekért lásd: felügyelt [hitelesítés az Androidban](brokered-auth.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Átirányítási URI a bizalmas ügyfélalkalmazások számára

A webalkalmazások esetében az átirányítási URI (vagy a válasz URI) az az URI, amelyet az Azure AD fog használni a jogkivonat visszaküldéséhez az alkalmazásnak. Ez az URI lehet a webalkalmazás/webes API URL-címe, ha a bizalmas alkalmazás ezek egyike. Az átirányítási URI-t regisztrálni kell az alkalmazás regisztrálásakor. Ez a regisztráció különösen fontos, ha olyan alkalmazást telepít, amelyet eredetileg helyileg tesztelt. Ezután hozzá kell adnia az üzembe helyezett alkalmazás válasz URL-címét az alkalmazás regisztrációs portálján.

A Daemon-alkalmazások esetében nem kell megadnia az átirányítási URI-t.

## <a name="client-secret"></a>Titkos ügyfélkulcs

Ez a beállítás határozza meg a bizalmas ügyfélalkalmazás ügyfél-titkos kulcsát. Ezt a titkot (az alkalmazás jelszava) a regisztrációs portál vagy az Azure AD számára az alkalmazások regisztrálásakor a PowerShell AzureAD, a PowerShell AzureRM vagy az Azure CLI használatával biztosítjuk.

## <a name="logging"></a>Naplózás

A további konfigurációs beállítások lehetővé teszik a naplózást és a hibaelhárítást. A használatáról a [naplózási](msal-logging.md) cikkben talál további információt.

## <a name="next-steps"></a>További lépések

Ismerje meg [, hogyan hozhatók létre ügyfélalkalmazások a MSAL.NET használatával](msal-net-initializing-client-applications.md).
Ismerje meg [, hogyan hozhatók létre ügyfélalkalmazások MSAL.jshasználatával ](msal-js-initializing-client-applications.md).
