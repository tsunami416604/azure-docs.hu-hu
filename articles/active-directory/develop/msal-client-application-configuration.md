---
title: Ügyfélalkalmazás konfigurációja (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a nyilvános ügyfél és a bizalmas ügyfélalkalmazások konfigurációs beállításait a Microsoft Authentication Library (MSAL) használatával.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5388dc101dbc4e06ce26f12bfd40645455e8dae8
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696482"
---
# <a name="application-configuration-options"></a>Alkalmazás konfigurációs beállításai

A kódban egy új nyilvános vagy bizalmas ügyfélalkalmazás (vagy a MSAL. js-hez tartozó felhasználói ügynök) inicializálásával hitelesítheti és beszerezheti a jogkivonatokat. Az ügyfélalkalmazás a Microsoft Authentication Library (MSAL) szolgáltatásban való inicializálása során több konfigurációs beállítást is beállíthat. Ezek a lehetőségek két csoportba sorolhatók:

- Regisztrációs beállítások, beleértve a következőket:
    - [](#authority) Az alkalmazáshoz tartozó identitás-szolgáltatói [példányból](#cloud-instance) és a bejelentkezési [célközönségből](#application-audience) , és valószínűleg a bérlői azonosítóból áll.
    - [Ügyfél-azonosító](#client-id).
    - [Átirányítási URI](#redirect-uri).
    - [Ügyfél titka](#client-secret) (bizalmas ügyfélalkalmazások esetében).
- [Naplózási beállítások](#logging), beleértve a naplózási szintet, a személyes adatvezérlést és az összetevő nevét a könyvtár használatával.

## <a name="authority"></a>Authority

A szolgáltató egy URL-cím, amely egy olyan könyvtárat jelez, amelyből a MSAL jogkivonatokat kérhet. Gyakori hatóságok:

- https\://login.microsoftonline.com/\<bérlő\>/, ahol &lt;bérlő&gt; az Azure Active Directory (Azure AD) bérlő vagy az Azure AD-bérlőhöz társított tartomány bérlői azonosítója. Csak egy adott szervezet felhasználói számára való bejelentkezésre használatos.
- https\://login.microsoftonline.com/common/. Munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal való bejelentkezéshez használatos.
- https\://login.microsoftonline.com/organizations/. Munkahelyi és iskolai fiókkal rendelkező felhasználók bejelentkezésére használatos.
- https\://login.microsoftonline.com/consumers/. Csak személyes Microsoft-fiókkal (korábbi nevén Windows Live ID-fiókokkal) való bejelentkezésre használatos.

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

A *példány* annak megadására szolgál, hogy az alkalmazás a felhasználókat az Azure nyilvános felhőből vagy az országos felhőkből írja-e be. A kódban a MSAL használatával beállíthatja az Azure Cloud-példányt egy enumerálással, vagy átadhatja az URL-címet a [nemzeti Felhőbeli példánynak](authentication-national-cloud.md#azure-ad-authentication-endpoints) `Instance` tagként (ha ismeri).

A MSAL.NET explicit kivételt ad, ha a `Instance` és a `AzureCloudInstance` is meg van adva.

Ha nem ad meg egy példányt, az alkalmazás az Azure nyilvános felhő példányát fogja használni (az URL-cím `https://login.onmicrosoftonline.com`).

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
    - `organizations` több-bérlős alkalmazáshoz
    - `consumers` csak a személyes fiókkal rendelkező felhasználók jelentkezhetnek be
    - a felhasználók munkahelyi és iskolai fiókjaikkal vagy személyes Microsoft-fiókjaikkal való bejelentkezéshez `common`

Ha az Azure AD-szolgáltatót és a bérlői azonosítót is megadja, a MSAL értelmes kivételt fog kiváltani.

Ha nem ad meg célközönséget, az alkalmazás célközönségként fogja megcélozni az Azure AD-t és a személyes Microsoft-fiókokat. (Vagyis úgy viselkedik, mintha `common` voltak megadva.)

### <a name="effective-audience"></a>Hatékony célközönség

Az alkalmazás tényleges célközönsége lesz a minimális (ha van egy metszéspontja) az alkalmazásban beállított célközönségnek és az alkalmazás regisztrációjában megadott célközönségnek. Valójában a [Alkalmazásregisztrációki](https://aka.ms/appregistrations) élmény lehetővé teszi az alkalmazás célközönségének (a támogatott fióktípus) megadását. További információ: gyors útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](quickstart-register-app.md).

Jelenleg csak a személyes Microsoft-fiókkal rendelkező felhasználók jelentkezhetnek be az alkalmazásba, így mindkét beállítást konfigurálhatja:
- Az alkalmazás regisztrációs célközönségének beállítása `Work and school accounts and personal accounts`.
- A kód/konfiguráció célközönségének beállítása `AadAuthorityAudience.PersonalMicrosoftAccount` (vagy `TenantID` = "fogyasztók") értékre.

## <a name="client-id"></a>Ügyfél-azonosító

Az ügyfél-azonosító az alkalmazáshoz az Azure AD által az alkalmazás regisztrálásakor hozzárendelt egyedi alkalmazás-(ügyfél-) azonosító.

## <a name="redirect-uri"></a>Átirányítási URI

Az átirányítási URI az az URI, amelyet az identitás-szolgáltató küld vissza a biztonsági jogkivonatoknak.

### <a name="redirect-uri-for-public-client-apps"></a>Nyilvános ügyfélalkalmazások átirányítási URI-ja

Ha Ön a MSAL-t használó nyilvános ügyfélalkalmazás-fejlesztő:
- Asztali vagy UWP alkalmazásokban szeretné használni a `.WithDefaultRedirectUri()`t (MSAL.NET 4.1 +). Ezzel a módszerrel a nyilvános ügyfélalkalmazás átirányítási URI-tulajdonságát a nyilvános ügyfélalkalmazások alapértelmezett ajánlott átirányítási URI-ja alapján állítja be a rendszer. 

  Platform  | Átirányítási URI  
  ---------  | --------------
  Asztali alkalmazás (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`értéke. Ez lehetővé teszi az egyszeri bejelentkezést a böngészőben úgy, hogy az értéket a WebAuthenticationBroker. GetCurrentApplicationCallbackUri () értékre állítja be, amelyet regisztrálnia kell
  .NET Core | `https://localhost` kérdésre adott válaszban foglalt lépéseket. Ez lehetővé teszi, hogy a felhasználó a rendszerböngészőt interaktív hitelesítésre használja, mivel a .NET Core jelenleg nem rendelkezik felhasználói felülettel a beágyazott webes nézethez.

- Nem kell hozzáadnia átirányítási URI-t, ha olyan Android-és iOS-alkalmazást épít, amely nem támogatja a közvetítőt (az átirányítási URI-t a rendszer automatikusan `msal{ClientId}://auth`ra állítja a Xamarin Android és iOS rendszerű Xamarin.

- Konfigurálnia kell az átirányítási URI-t a [Alkalmazásregisztrációkban](https://aka.ms/appregistrations):

   ![Átirányítási URI a Alkalmazásregisztrációkban](media/msal-client-application-configuration/redirect-uri.png)

Az átirányítási URI-t felülbírálhatja a `RedirectUri` tulajdonság használatával (például a brókerek használata esetén). Íme néhány példa a forgatókönyv átirányítási URI-azonosítóra:

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

## <a name="next-steps"></a>Következő lépések

Ismerje meg [, hogyan hozhatók létre ügyfélalkalmazások a MSAL.NET használatával](msal-net-initializing-client-applications.md).
Ismerje meg [, hogyan hozhatók létre ügyfélalkalmazások a MSAL. js használatával](msal-js-initializing-client-applications.md).
