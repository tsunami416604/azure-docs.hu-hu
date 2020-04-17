---
title: Ügyfélalkalmazás-konfiguráció (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Információ a nyilvános ügyfél- és bizalmas ügyfélalkalmazások konfigurációs lehetőségeiről a Microsoft hitelesítési könyvtár (MSAL) használatával.
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534465"
---
# <a name="application-configuration-options"></a>Alkalmazáskonfigurációs beállítások

A kódban inicializálegy új nyilvános vagy bizalmas ügyfélalkalmazást (vagy az MSAL.js felhasználó-ügynökét) a jogkivonatok hitelesítéséhez és beszerzéséhez. Az ügyfélalkalmazás Microsoft Authentication Library (MSAL) inicializálásakor számos konfigurációs beállítást adhat meg. Ezek a lehetőségek két csoportba tartoznak:

- Regisztrációs lehetőségek, többek között:
    - [Hatóság](#authority) (az [identitásszolgáltató-példányból](#cloud-instance) és az alkalmazás bejelentkezési [közönségből,](#application-audience) és esetleg a bérlői azonosítóból áll).
    - [Ügyfélazonosító](#client-id).
    - [Az URI átirányítása](#redirect-uri).
    - [Ügyféltitkos adat](#client-secret) (bizalmas ügyfélalkalmazásokhoz).
- [Naplózási beállítások](#logging), beleértve a naplószintet, a személyes adatok ellenőrzését és az összetevő nevét a tár használatával.

## <a name="authority"></a>Authority

A hatóság egy URL-cím, amely azt jelzi, hogy a könyvtár, amely MSAL kérhet jogkivonatokat. A közös hatóságok a következők:

- https\:\<//login.microsoftonline.com/\>bérlő &lt;/, ahol a bérlő&gt; az Azure Active Directory (Azure AD) bérlői azonosítója vagy az Azure AD-bérlőhöz társított tartomány. Csak egy adott szervezet felhasználóinak bejelentkezésére használható.
- https\://login.microsoftonline.com/common/. A munkahelyi és iskolai fiókkal vagy személyes Microsoft-fiókkal rendelkező felhasználók bejelentkezése.
- https\://login.microsoftonline.com/organizations/. A munkahelyi és iskolai fiókkal rendelkező felhasználók bejelentkezéséhez használható.
- https\://login.microsoftonline.com/consumers/. A felhasználók csak személyes Microsoft-fiókkal (korábbi nevén Windows Live ID-fiókkal) rendelkező felhasználók at regisztrálhatnak.

A jogosultságbeállításnak összhangban kell lennie az alkalmazásregisztrációs portálon megadott adatokkal.

A hatóság URL-címe a példányból és a célközönségből áll.

A hatóság lehet:
- Egy Azure AD felhőalapú jogosultság.
- Egy Azure AD B2C-szolgáltató. Lásd: [B2C-részletek](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Active Directory összevonási szolgáltatások (AD FS) hatóság. Lásd: [AD FS támogatás](https://aka.ms/msal-net-adfs-support).

Az Azure AD felhőhatóságai két részből állnak:
- Az identitásszolgáltató *példánya*
- Az alkalmazás bejelentkezési *közönsége*

A példány és a célközönség összefűzhető, és a hatóság URL-címeként adható meg. Az MSAL 3-nál korábbi MSAL.NET verzióiban. *x*, meg kellett írni a hatóság magad alapján a felhő kívánt cél és a bejelentkezési közönség.  Ez az ábra a hatóság URL-címének összeállását mutatja be:

![A hatóság URL-címének összeállása](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Felhőpéldány

A *példány* segítségével megadhatja, hogy az alkalmazás az Azure nyilvános felhőből vagy a nemzeti felhőkből írja-e alá a felhasználókat. Az MSAL használatával a kódot, beállíthatja az Azure felhőpéldány segítségével egy számbavétel, vagy `Instance` az URL-t a [nemzeti felhőpéldány,](authentication-national-cloud.md#azure-ad-authentication-endpoints) mint a tag (ha tudja).

MSAL.NET kifejezett kivételt okoz, ha mindkettő `Instance` meg `AzureCloudInstance` van adva.

Ha nem ad meg példányt, az alkalmazás az Azure nyilvános `https://login.onmicrosoftonline.com`felhőpéldányát (az URL-példányt) célozza meg.

## <a name="application-audience"></a>Alkalmazás közönsége

A bejelentkezési közönség az alkalmazás üzleti igényeitől függ:
- Ha ön egy üzletági (LOB) fejlesztő, akkor valószínűleg egy egybérlős alkalmazást fog létrehozni, amelyet csak a szervezetben fog használni. Ebben az esetben meg kell adnia a szervezetet, vagy a bérlői azonosító (az Azure AD-példány azonosítója) vagy az Azure AD-példánytársított tartománynév alapján.
- Ha Ön független független ítő, előfordulhat, hogy bármely szervezetben vagy egyes szervezetekben (több-bérlős alkalmazásban) be szeretne jelentkezni a munkahelyi és iskolai fiókjukkal rendelkező felhasználókba. De érdemes lehet a felhasználókat személyes Microsoft-fiókjukkal is bejelentkezni.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>A célközönség megadása a kódban/konfigurációban

Az MSAL használatával a következő értékek egyikével adhatja meg a célközönséget:
- Az Azure AD-szolgáltató közönségszámbavétele
- A bérlőazonosító, amely a következő lehet:
  - Egy GUID azonosító (az Azure AD-példány azonosítója) az egybérlős alkalmazásokhoz
  - Az Azure AD-példányhoz társított tartománynév (egybérlős alkalmazásokhoz is)
- Az egyik helyőrzők bérlői azonosítóként helyett az Azure AD-szolgáltató közönség számbavétele:
    - `organizations`több-bérlős alkalmazásesetén
    - `consumers`a felhasználók bejelentkezése csak a személyes fiókjukkal
    - `common`bejelentkezés a felhasználók munkahelyi és iskolai fiókkal vagy személyes Microsoft-fiókjukkal

Az MSAL jelentős kivételt fog adni, ha megadja az Azure AD-szolgáltató közönségét és a bérlői azonosítót is.

Ha nem ad meg célközönséget, az alkalmazás célközönségként fogja megcélozni az Azure AD-t és a személyes Microsoft-fiókokat. (Ez azt, hogy úgy `common` fog viselkedni, mintha meg lett volna adva.)

### <a name="effective-audience"></a>Hatékony közönség

Az alkalmazás hatékony célközönsége az alkalmazásban beállított célközönség és az alkalmazásregisztrációban megadott célközönség minimális célközönsége (ha van metszéspontja). Valójában az [alkalmazásregisztrációk](https://aka.ms/appregistrations) funkció valamerre lehetővé teszi az alkalmazás célközönségének (a támogatott fióktípusoknak) a megadását. További információt a [Rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platformmal című témakörben talál.](quickstart-register-app.md)

Jelenleg az egyetlen módja annak, hogy egy alkalmazás csak személyes Microsoft-fiókkal rendelkező felhasználókat jelentkezzen be, ha mindkét beállítást konfigurálja:
- Állítsa az alkalmazásregisztrációs `Work and school accounts and personal accounts`közönséget a beállításra.
- Állítsa be a célközönséget `AadAuthorityAudience.PersonalMicrosoftAccount` a `TenantID` kódban/konfigurációban (vagy ="fogyasztók").

## <a name="client-id"></a>Ügyfél-azonosító

Az ügyfélazonosító az az egyedi alkalmazás (ügyfél) azonosító, amelyet az Azure AD rendelt az alkalmazáshoz az alkalmazás regisztrálásakor.

## <a name="redirect-uri"></a>Átirányítási URI

Az átirányítási URI az az URI, amelybe az identitásszolgáltató visszaküldi a biztonsági jogkivonatokat.

### <a name="redirect-uri-for-public-client-apps"></a>Uri átirányítása nyilvános ügyfélalkalmazásokhoz

Ha Ön az MSAL-t használó nyilvános ügyfélalkalmazás-fejlesztő:
- Asztali vagy UWP `.WithDefaultRedirectUri()` alkalmazásokban (MSAL.NET 4.1+) szeretné használni. Ez a módszer a nyilvános ügyfélalkalmazás átirányítási uri tulajdonságát az alapértelmezett ajánlott átirányítási uri-ra állítja a nyilvános ügyfélalkalmazásokhoz.

  Platform  | Átirányítási URI
  ---------  | --------------
  Asztali alkalmazás (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient`
  UWP | értéke. `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` Ez lehetővé teszi az SSO-t a böngészővel a WebAuthenticationBroker.GetCurrentApplicationCallbackUri() eredményének beállításával, amelyet regisztrálnia kell
  .NET Core | `https://localhost`. Ez lehetővé teszi a felhasználó számára, hogy a rendszerböngészőt interaktív hitelesítésre használja, mivel a .NET Core jelenleg nem rendelkezik felhasználói felülettel a beágyazott webes nézethez.

- Nem kell átirányítási URI-t hozzáadnia, ha olyan Xamarin Android és iOS alkalmazást hoz létre, amely nem `msal{ClientId}://auth` támogatja a brókert (az átirányítási URI automatikusan Xamarin Android és iOS rendszerre van beállítva

- Konfigurálnia kell az átirányítási URI-t az [alkalmazásregisztrációkban:](https://aka.ms/appregistrations)

   ![URI átirányítása az alkalmazásregisztrációkban](media/msal-client-application-configuration/redirect-uri.png)

Felülbírálhatja az átirányítási URI-t `RedirectUri` a tulajdonság használatával (például brókerek használata esetén). Íme néhány példa az adott forgatókönyvhöz szükséges átirányítási URI-kra:

- `RedirectUriOnAndroid`= "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos`= $"msauth. {Bundle.ID}://auth";

További iOS-részletek: [A Microsoft Authenticator t használó iOS-alkalmazások áttelepítése ADAL.NET-MSAL.NET](msal-net-migration-ios-broker.md) és [a bróker kihasználása iOS rendszeren](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)című témakörben talál.
További Android-részletek: [Közvetített hitelesítés androidos](brokered-auth.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Uri átirányítása bizalmas ügyfélalkalmazásokhoz

A webalkalmazások, az átirányítási URI (vagy válasz URI) az az URI, amely et az Azure AD fogja használni, hogy küldje vissza a jogkivonatot az alkalmazásnak. Ez az URI lehet a webalkalmazás/web API URL-címe, ha a bizalmas alkalmazás az egyik ilyen. Az átirányítási URI-t regisztrálni kell az alkalmazásregisztrációban. Ez a regisztráció különösen fontos, ha olyan alkalmazást telepít, amelyet eredetileg helyileg tesztelt. Ezután hozzá kell adnia a telepített alkalmazás válasz URL-címét az alkalmazásregisztrációs portálon.

Démonalkalmazások esetén nem kell átirányítási URI-t megadnia.

## <a name="client-secret"></a>Titkos ügyfélkulcs

Ez a beállítás a bizalmas ügyfélalkalmazás ügyféltitkát adja meg. Ezt a titkos kulcsot (alkalmazásjelszót) az alkalmazásregisztrációs portál biztosítja, vagy az Azure AD-nek a PowerShell AzureAD, a PowerShell AzureRM vagy az Azure CLI alkalmazásregisztráció során.

## <a name="logging"></a>Naplózás

A többi konfigurációs beállítás lehetővé teszi a naplózást és a hibaelhárítást. A [naplózási](msal-logging.md) cikkben további információt talál a használatukról.

## <a name="next-steps"></a>További lépések

Ismerje meg [az ügyfélalkalmazások MSAL.NET használatával történő példányosítását.](msal-net-initializing-client-applications.md)
Ismerje meg [az ügyfélalkalmazások példányosítását az MSAL.js használatával.](msal-js-initializing-client-applications.md)
