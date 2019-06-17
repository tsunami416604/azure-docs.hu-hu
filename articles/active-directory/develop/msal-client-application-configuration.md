---
title: Ügyféloldali alkalmazás konfigurációja (Microsoft-hitelesítési tár) |} Az Azure
description: Ismerje meg a nyilvános ügyfél és a bizalmas ügyfél beállítási lehetőségei a Microsoft hitelesítési tár (MSAL) alkalmazások.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
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
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430804"
---
# <a name="application-configuration-options"></a>Alkalmazás-konfigurációs beállításai

A kód egy új nyilvános vagy bizalmas ügyfél (vagy a felhasználói ügynököt az MSAL.js) inicializálása alkalmazás hitelesítéséhez és a jogkivonatok beszerzéséhez. Konfigurációs beállítások több állíthat be, ha a táblafiókhoz az ügyfélalkalmazásnak a Microsoft hitelesítési tár (MSAL). Ezek a beállítások két csoportba sorolhatók:

- Regisztrációs beállítások, például:
    - [Szolgáltató](#authority) (mikroszolgáltatásokból álló, az identitásszolgáltató [példány](#cloud-instance) és bejelentkezési [célközönség](#application-audience) az alkalmazást, és valószínűleg a bérlő azonosítója).
    - [Ügyfél-azonosító](#client-id).
    - [Átirányítási URI](#redirect-uri).
    - [titkos Ügyfélkód](#client-secret) (bizalmas ügyfél alkalmazások esetén).
- [Naplózási beállítások](#logging), beleértve a naplózási szint vezérlési a személyes adatok és neve az összetevőt, a kódtár használatával.

## <a name="authority"></a>szolgáltató
A rendszer nem egy URL-címet, amely azt jelzi, hogy egy könyvtárat, amely az MSAL származó jogkivonatokat kérhetnek. Közös esetében:

- https://login.microsoftonline.com/&lt ; bérlő&gt; /, ahol &lt; bérlői&gt; van az Azure Active Directory (Azure AD) bérlő vagy az Azure AD-bérlőhöz társított tartomány Bérlőazonosítója. Csak használják a felhasználók egy adott szervezet.
- https://login.microsoftonline.com/common/. A felhasználók munkahelyi és iskolai fiókokhoz vagy személyes Microsoft-fiókok segítségével.
- https://login.microsoftonline.com/organizations/. Segítségével a felhasználók munkahelyi és iskolai fiókjába.
- https://login.microsoftonline.com/consumers/. Jelentkezzen be a felhasználók csak személyes Microsoft-fiókok (korábbi nevén Windows Live ID-fiókok)-segítségével.

A szolgáltató beállítás kell lennie az, hogy mi van deklarálva az alkalmazásregisztrációs portálon.

A szolgáltató URL-CÍMÉT a példány és a közönség által feltett tevődik össze.

A szolgáltató is lehet:
- Az Azure AD-felhő szolgáltatót.
- egy Azure AD B2C-szolgáltatót. Lásd: [B2C tulajdonságairól](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Az Active Directory összevonási szolgáltatások (ADFS) szolgáltatóként. Lásd: [támogatja az AD FS](https://aka.ms/msal-net-adfs-support).

Az Azure AD felhőalapú hatóságok van két részből áll:
- az identitásszolgáltató *példány*
- A bejelentkezési *célközönség* az alkalmazás

A példány és a közönség által feltett lehetnek összefűzött és a szolgáltató URL-címeként megadott. Az MSAL.NET régebbi verziói MSAL 3-nál. *x*, akkor saját kezűleg a szolgáltató compose kellett, a felhő alapú szeretne megcélozni, és jelentkezzen be célközönség.  Ez az ábra bemutatja, hogyan épül fel a szolgáltató URL-címe:

![Hogyan épül fel a szolgáltató URL-címe](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Felhő-példány
A *példány* azt adhatja meg, ha az alkalmazás a felhasználók az Azure nyilvános felhő vagy az országos felhők jelentkezik. Az MSAL használatával a kódban, beállíthatja az Azure-felhő-példány egy enumerálás vagy az URL-címet adja át a [országos felhőbeli példány](authentication-national-cloud.md#azure-ad-authentication-endpoints) , a `Instance` tag (ha tudja).

MSAL.NET kivételt ad explicit Ha mindkét `Instance` és `AzureCloudInstance` vannak megadva.

Egy példány nem ad meg, ha az alkalmazás által megcélzott az Azure nyilvános felhő-példány (a példány URL-cím `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Alkalmazás célközönség

A bejelentkezési célközönség az üzleti igényeknek megfelelő, az alkalmazás függ:
- Ha Ön egy üzletági (LOB) fejlesztő, valószínűleg fog készít egy egybérlős alkalmazást, amely használható csak a szervezetben. Ebben az esetben meg kell adnia a szervezetben, vagy a bérlő azonosítója (az Azure AD-példány azonosítója) vagy az Azure AD-példányhoz társított tartománynév.
- Ha Ön független Szoftverszállító, érdemes lehet, hogy a felhasználók a munkahelyi és iskolai fiókjaikat bármely szervezet vagy az egyes szervezetek (több-bérlős alkalmazás). Azonban előfordulhat, hogy szeretné is, vannak olyan felhasználói, jelentkezzen be személyes Microsoft-fiókjukkal.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>A helykódot a célközönség megadása
Az MSAL használatával a kódban, szerint kell megadni a közönség a következő értékek egyikét:
- Az Azure AD authority célközönség enumerálás
- A bérlő azonosítója, amely lehet:
  - Egybérlős alkalmazások egy GUID Azonosítót (az Azure AD-példány azonosítója),
  - Az Azure AD-példányt (is egybérlős alkalmazások esetén) társított tartománynév
- Ezeket a helyőrzőket a helyett az Azure AD authority célközönség enumerálás bérlői azonosító egyike:
    - `organizations` a több-bérlős alkalmazásokban
    - `consumers` a felhasználók csak a személyes
    - `common` a felhasználók a munkahelyi és iskolai fiókokhoz vagy személyes Microsoft-fiókjukkal

Az MSAL váltja egy jelentéssel bíró kivétel, ha megadja az Azure AD authority célközönség és a bérlő azonosítója.

Ha nem ad meg egy adott célközönségnek, az alkalmazás által megcélzott Azure ad-ben és a személyes Microsoft-fiókkal való egy adott célközönségnek. (Azaz úgy működik, mint `common` lett megadva.)

### <a name="effective-audience"></a>Hatékony célközönség
Az alkalmazás hatékony célközönség (ha metszettel) állítsa be az alkalmazás célközönség és a célközönség az alkalmazás regisztrációját a megadott minimális lesz. Valójában a [alkalmazásregisztrációk](https://aka.ms/appregistrations) élmény lehetővé teszi, hogy az alkalmazás a célközönség (a támogatott fióktípus esetében) adható meg. További információkért lásd: [a rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platform az](quickstart-register-app.md).

Csak úgy egy alkalmazást, hogy a felhasználók csak személyes Microsoft-fiókok beolvasása jelenleg ezek a beállítások konfigurálásához:
- Állítsa az alkalmazás regisztrációs célközönség `Work and school accounts and personal accounts`.
- A célközönség állíthat be a kódot/konfigurációs való `AadAuthorityAudience.PersonalMicrosoftAccount` (vagy `TenantID` = "felhasználók").

## <a name="client-id"></a>Ügyfél-azonosító
Az ügyfél-azonosító a az alkalmazás regisztrálásakor az Azure AD által az alkalmazáshoz rendelt egyedi alkalmazás (ügyfél) azonosítója.

## <a name="redirect-uri"></a>Redirect URI
Az átirányítási URI-ja az URI-t az identitásszolgáltató küld vissza a biztonsági jogkivonatokat.

### <a name="redirect-uri-for-public-client-apps"></a>Átirányítási URI-nyilvános ügyfélalkalmazások
Ha egy nyilvános ügyfél alkalmazásfejlesztő, akik az MSAL használatával:
- Nem kell átadni `RedirectUri` mert MSAL szerint a rendszer automatikusan számítja. Az átirányítási URI van megadva, ezeket az értékeket, a platformjától függően:
   - `urn:ietf:wg:oauth:2.0:oob` az összes Windows platformra
   - `msal{ClientId}://auth` Xamarin Android és IOS rendszereken

- Szeretne beállítani az átirányítási URI-t [alkalmazásregisztrációk](https://aka.ms/appregistrations):

   ![Átirányítási URI az alkalmazásregisztrációk](media/msal-client-application-configuration/redirect-uri.png)

Az átirányítási URI-JÁNAK használatával felülírhatja a `RedirectUri` tulajdonságot (például, ha a közvetítők használata). Íme néhány példa az átirányítási URI-nak ehhez a forgatókönyvhöz:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = a $"msauth. {Bundle.ID}://auth";

További információkért lásd: a [dokumentációjában, Android és IOS rendszerű](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>Átirányítási URI-bizalmas ügyfélalkalmazások
A web apps az átirányítási URI-t (vagy a válasz URI) az URI-t használó Azure ad-ben a jogkivonat küldése az alkalmazásnak. Ez lehet a webalkalmazás vagy webes API URL-CÍMÉT, egyet a bizalmas alkalmazások esetén. Az átirányítási URI-t kell regisztrálni az alkalmazás regisztrációját. Ez a regisztráció különösen fontos, ha telepít egy alkalmazást, amely kezdetben helyi tesztelését. Majd hozzá kell az üzembe helyezett alkalmazás válasz URL-címe az az alkalmazásregisztrációs portálon.

Démon alkalmazások esetében nem kell átirányítási URI-t adja meg.

## <a name="client-secret"></a>Titkos ügyfélkulcs
Ezzel a beállítással megadhatja a bizalmas ügyfélalkalmazás az ügyfél titkos kulcsát. A titkos kód (alkalmazásjelszót) biztosítják az alkalmazásregisztrációs portálon vagy az PowerShell AzureAD, az PowerShell AzureRM vagy az Azure CLI az Azure AD-alkalmazás regisztrációja során megadott.

## <a name="logging"></a>Naplózás
Az egyéb konfigurációs lehetőségek engedélyezése a naplózás és hibaelhárítás. Tekintse meg a [naplózás](msal-logging.md) részleteivel kapcsolatban azok használatát.

## <a name="next-steps"></a>További lépések
Ismerje meg [hárítható el az ügyfélalkalmazások által az MSAL.NET használatával](msal-net-initializing-client-applications.md).

Ismerje meg [hárítható el ügyfélalkalmazások MSAL.js használatával](msal-js-initializing-client-applications.md).
