---
title: Ügyféloldali alkalmazás konfigurációja (Microsoft-hitelesítési tár) |} Az Azure
description: Ismerje meg a nyilvános ügyfél és a bizalmas ügyfél beállítási lehetőségei a Microsoft hitelesítési tár (MSAL) alkalmazások.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a48eea9fedd2d82f44693d58b31ee0d5c8c288d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138536"
---
# <a name="application-configuration-options"></a>Alkalmazás-konfigurációs beállításai

A kód egy új nyilvános vagy bizalmas ügyfél (vagy a felhasználói ügynököt az MSAL.js) inicializálása alkalmazás hitelesítéséhez és a jogkivonatok beszerzéséhez.  Számos különböző konfigurációs lehetőségeket, az ügyfélalkalmazás az MSAL inicializálásakor állítható be. Ezek a beállítások két csoportra oszthatók:

- Regisztrációs beállítások, például:
    - [Szolgáltató](#authority) (mikroszolgáltatásokból álló, az identitásszolgáltató [példány](#cloud-instance) és bejelentkezési [célközönség](#application-audience) az alkalmazást, és valószínűleg a bérlő azonosítója).
    - [client ID](#client-id)
    - [Átirányítási URI](#redirect-uri)
    - [titkos Ügyfélkód](#client-secret) (bizalmas ügyfél alkalmazások esetén).
- [Naplózási beállítások](#logging), beleértve a naplózási szint vezérlési a személyes adatok és neve az összetevőt, a kódtár használatával.

## <a name="authority"></a>szolgáltató
A rendszer nem egy könyvtár, amely az MSAL származó jogkivonatokat kérhetnek utaló URL-címet. Szokásos esetében:

- https://login.microsoftonline.com/&lt; bérlő&gt;/, ahol &lt;bérlői&gt; van az Azure AD-bérlő vagy az Azure AD-bérlőhöz társított tartomány Bérlőazonosítója.  Csak használják a felhasználók egy adott szervezet.
- https://login.microsoftonline.com/common/. Segítségével a felhasználók munkahelyi és iskolai fiókokhoz vagy személyes Microsoft-fiókkal.
- https://login.microsoftonline.com/organizations/. Segítségével a felhasználók munkahelyi és iskolai fiókjába.
- https://login.microsoftonline.com/consumers/. Segítségével a felhasználók csak személyes Microsoft-fiókkal (live).

A szolgáltató beállítás mi van deklarálva az alkalmazásregisztrációs portálon összhangban kell lennie.

A szolgáltató URL-CÍMÉT a példány és a közönség által feltett tevődik össze.

A szolgáltató is lehet:
- az Azure Active directory felhőalapú hatóság
- egy Azure AD B2C-szolgáltatót. Lásd: [B2C tulajdonságairól](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- egy AD FS-szolgáltatót. Lásd: [támogatja az AD FS](https://aka.ms/msal-net-adfs-support).

Az Azure AD felhőalapú hatóságok van két részből áll:
- az identitásszolgáltató **példány**
- a bejelentkezési **célközönség** az alkalmazáshoz

A példány és a közönség által feltett lehetnek összefűzött és a szolgáltató URL-címeként megadott. Az MSAL.NET MSAL előtti verzióiban 3.x, kellett compose a szolgáltatót, magát a felhőben szeretne cél és a bejelentkezési célközönség attól függően.  Az alábbi ábrán látható, hogyan épül fel a szolgáltató URL-CÍMÉT.

![szolgáltató](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Felhő-példány
A **példány** azt adhatja meg, ha az alkalmazás a felhasználók a Microsoft Azure nyilvános felhőjében vagy a nemzeti felhőkben jelentkezik. A kód az MSAL használatával, az Azure-felhő-példány beállítható enumerálás vagy az URL-címet adja át a [országos felhőbeli példány](authentication-national-cloud.md#azure-ad-authentication-endpoints) , a `Instance` tag (ha tudja).

MSAL.NET kivételt ad explicit Ha mindkét `Instance` és `AzureCloudInstance` vannak megadva. 

Egy példány nem ad meg, ha az alkalmazás által megcélzott az Azure nyilvános felhő-példány (a példány URL-cím `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Alkalmazás célközönség

A bejelentkezési célközönség az üzleti igényeknek megfelelő, az alkalmazás függ:
- Ha Ön egy üzletági (LOB) fejlesztő, valószínűleg az egy egybérlős alkalmazást, csak a szervezetben használt elő lesz. Ebben az esetben kell adja meg, hogy milyen a szervezetben, a bérlő azonosítója (az Azure Active Directory-azonosító), vagy az Azure Active Directory társított tartománynév.
- Ha egy független, érdemes a felhasználók munkahelyi és iskolai fiókok minden olyan szervezet, vagy egyes szervezetek (több-bérlős alkalmazás), de Ön is szüksége lehet a felhasználók saját személyes Microsoft-fiókjával jelentkezzen.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>A helykódot a célközönség megadása
Az MSAL használatával a kódban, szerint kell megadni a célközönség:
- vagy az Azure AD authority célközönség enumerálás. 
- vagy a bérlő azonosítója, amely lehet:
  - egy GUID Azonosítót, (az Azure Active Directory-azonosító), egybérlős alkalmazások
  - az Azure Active Directoryban (is egybérlős alkalmazások esetén) társított tartománynév
- vagy az egyik ezeket a helyőrzőket a helyett az Azure AD authority célközönség enumerálás bérlői azonosító:
    - `organizations` egy több-bérlős alkalmazás esetében
    - `consumers` a felhasználók csak a személyes
    - `common` a munkahelyi és iskolai fiókot, vagy személyes Microsoft-fiókkal rendelkező felhasználók

Az MSAL váltja egy jelentéssel bíró kivétel, ha megadja az Azure AD-szolgáltató célközönség és a bérlő azonosítója. 

Ha egy adott célközönségnek nem adja meg az alkalmazás által megcélzott Azure ad-ben és a egy adott célközönségnek, személyes Microsoft-fiókok (azaz `common`).

### <a name="effective-audience"></a>Hatékony célközönség
Az alkalmazás hatékony célközönség (ha metszettel) állítsa be alkalmazását a célközönség és a célközönség az alkalmazás regisztrálása a megadott minimális lesz. Sőt, az alkalmazásregisztrációs folyamatot ([Alkalmazásregisztráció](https://aka.ms/appregistrations)) lehetővé teszi az alkalmazás a célközönség (támogatott fióktípus esetében) megadását. Lásd: [a rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platform az](quickstart-register-app.md) további információt.

Az egyetlen módszer a felhasználók csak személyes Microsoft-fiókokba való az alkalmazás jelenleg beállításához:
- Állítsa be az alkalmazás regisztrációs célközönség "Munkahelyi és iskolai fiókokhoz és személyes fiókok", és
- és állítsa be a közönség a kódban / konfiguráció `AadAuthorityAudience.PersonalMicrosoftAccount` (vagy `TenantID `= "felhasználók")

## <a name="client-id"></a>Ügyfél-azonosító
Ha az alkalmazás regisztrálva lett az Azure AD által az alkalmazáshoz rendelt egyedi alkalmazás (ügyfél) azonosítója.

## <a name="redirect-uri"></a>Átirányítási URI
Az átirányítási URI az az URI-t, amelyben az identitásszolgáltató küld vissza a biztonsági jogkivonatokat. 

### <a name="redirect-uri-for-public-client-applications"></a>Átirányítási URI-nyilvános ügyfélalkalmazások
Nyilvános ügyfél alkalmazásfejlesztőkre MSAL használatával:
- Nem kell átadni a ``RedirectUri`` módon MSAL szerint a rendszer automatikusan számítja. Az átirányítási URI-t beállítása a platformtól függően a következő értékeket:

- ``urn:ietf:wg:oauth:2.0:oob`` az összes Windows platformra
- ``msal{ClientId}://auth`` Xamarin Android és IOS rendszereken

Azonban az átirányítási URI-t kell konfigurálni a [Alkalmazásregisztráció](https://aka.ms/appregistrations).

![Átirányítási URI-portálon](media/msal-client-application-configuration/redirect-uri.png)

Felülbírálhatja az átirányítási URI használatával lehetséges a `RedirectUri` tulajdonság, például ha közvetítők. Az alábbiakban néhány példa az átirányítási URI-k ebben az esetben:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = a $"msauth. {Bundle.ID}://auth";

További részletekért lásd: Android tulajdonságairól és [iOS tulajdonságairól](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)

### <a name="redirect-uri-for-confidential-client-applications"></a>Átirányítási URI-bizalmas ügyfélalkalmazások
A web apps az átirányítási URI-t (vagy a válasz URI), akkor az URI-t, amellyel az Azure AD kapcsolatba lép vissza az alkalmazásnak a jogkivonat. Ez lehet az URL-címet a webalkalmazás / webes API-t, ha a bizalmas egyet ezek közül.  Az átirányítási URI-t kell regisztrálni kell az alkalmazás regisztrációját. Ez különösen fontos, amelyeket kezdetben tesztelt helyileg egy alkalmazás központi telepítésekor. Majd hozzá kell az üzembe helyezett alkalmazás válasz URL-címe az az alkalmazásregisztrációs portálon.

Démon alkalmazások esetében nem kell átirányítási URI-t adja meg.

## <a name="client-secret"></a>Titkos ügyfélkulcs
A titkos ügyfélkulcsot a bizalmas ügyfél alkalmazáshoz. A titkos kód (alkalmazás jelszó) az alkalmazásregisztrációs portálon által biztosított, vagy az PowerShell AzureAD, az PowerShell AzureRM vagy az Azure CLI az Azure AD az alkalmazás regisztrációja során megadott.

## <a name="logging"></a>Naplózás
A többi beállítást engedélyezi, naplózás és hibaelhárítás. További információkért lásd: a [naplózás](msal-logging.md) témánál talál további információkat a használatukat.

## <a name="next-steps"></a>További lépések
Ismerje meg [hárítható el az MSAL.NET használatával ügyfélalkalmazások](msal-net-initializing-client-applications.md).

Ismerje meg [hárítható el MSAL.js használó ügyfélalkalmazások](msal-js-initializing-client-applications.md).
