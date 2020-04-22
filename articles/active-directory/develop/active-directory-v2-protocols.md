---
title: OAuth 2.0 és OpenID Connect protokollok – Microsoft identity platform | Azure
description: Útmutató a Microsoft identity platform végpontja által támogatott OAuth 2.0 és OpenID Connect protokollokhoz.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: e94bdd6e79ff13d8ba736e140538bae74091f727
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680091"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>OAuth 2.0 és OpenID Connect protokollok a Microsoft identitásplatformon

A Microsoft identity platform végpont identitás-szolgáltatásként az iparági szabványnak megfelelő protokollok, OpenID Connect és OAuth 2.0. Bár a szolgáltatás szabványoknak megfelelő, a protokollok két implementációja között finom különbségek lehetnek. Az itt található információk akkor lesznek hasznosak, ha úgy dönt, hogy a kódot közvetlenül http-kérések küldésével és kezelésével írja meg, vagy egy harmadik fél nyílt forráskódú könyvtárát használja, ahelyett, hogy az egyik [nyílt forráskódú könyvtárunkat](reference-v2-libraries.md)használná.

## <a name="the-basics"></a>Az alapok

Szinte minden OAuth 2.0 és OpenID Connect forgalomban négy fél vesz részt a cserében:

![Az OAuth 2.0 szerepköröket bemutató diagram](./media/active-directory-v2-flows/protocols-roles.svg)

* Az **engedélyezési kiszolgáló** a Microsoft identitásplatform-végpontja, amely felelős a felhasználó identitásának biztosításáért, az erőforrásokhoz való hozzáférés biztosításáért és visszavonásáért, valamint a jogkivonatok kiadásáért. Az engedélyezési kiszolgáló is ismert, mint az identitás-szolgáltató - biztonságosan kezeli a felhasználó adatait, azok hozzáférését, és a bizalmi kapcsolatok felek között egy folyamatban.
* Az **erőforrás tulajdonosa** általában a végfelhasználó. Ez az a fél, amely birtokolja az adatokat, és rendelkezik azzal a hatáskörrel, hogy harmadik felek számára lehetővé tegye az adatokhoz vagy erőforrásokhoz való hozzáférést.
* Az **OAuth-ügyfél** az ön alkalmazása, amelyet az alkalmazásazonosítója azonosít. Az OAuth-ügyfél általában az a fél, akivel a végfelhasználó interakcióba lép, és jogkivonatokat kér az engedélyezési kiszolgálótól. Az ügyfélnek engedélyt kell adni az erőforrás eléréséhez az erőforrás tulajdonosa által.
* Az **erőforrás-kiszolgáló az,** ahol az erőforrás vagy az adatok találhatók. Megbízik abban, hogy az engedélyezési kiszolgáló biztonságosan hitelesítse és engedélyezze az OAuth-ügyfelet, és tulajdonosi hozzáférési jogkivonatokat használ annak biztosítására, hogy az erőforrásokhoz való hozzáférés megadható legyen.

## <a name="app-registration"></a>Alkalmazásregisztráció

Minden olyan alkalmazást, amely személyes és munkahelyi vagy iskolai fiókokat is el akar fogadni, regisztrálni kell az [Azure Portalon](https://aka.ms/appregistrations) az **alkalmazásregisztrációk** felületén keresztül, mielőtt aláírhatná ezeket a felhasználókat az OAuth 2.0 vagy az OpenID Connect használatával. Az alkalmazásregisztrációs folyamat néhány értéket gyűjt össze, és hozzárendel néhány értéket az alkalmazáshoz:

* **Alkalmazásazonosító,** amely egyedileg azonosítja az alkalmazást
* **Átirányítási URI** (nem kötelező), amely a válaszokat az alkalmazásra irányíthatja
* Néhány más forgatókönyv-specifikus értékeket.

További részletekért ismerkedjen meg az [alkalmazások regisztrálásának folyamatával](quickstart-register-app.md).

## <a name="endpoints"></a>Végpontok

A regisztrációt követően az alkalmazás a végfelhasználói kérelmek küldésével kommunikál a Microsoft identity platformmal:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Ahol `{tenant}` a lehet venni egy négy különböző értékek:

| Érték | Leírás |
| --- | --- |
| `common` | Lehetővé teszi, hogy a személyes Microsoft-fiókkal és az Azure AD-ből származó munkahelyi/iskolai fiókokkal rendelkező felhasználók jelentkezzenek be az alkalmazásba. |
| `organizations` | Csak az Azure AD-ből származó munkahelyi/iskolai fiókkal rendelkező felhasználók jelentkeztetnek be az alkalmazásba. |
| `consumers` | Csak a személyes Microsoft-fiókkal (MSA) rendelkező felhasználók jelentkezhöttbe az alkalmazásba. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` | Csak egy adott Azure AD-bérlőből származó munkahelyi/iskolai fiókkal rendelkező felhasználók jelentkeztetnek be az alkalmazásba. Az Azure AD-bérlő rövid tartományneve vagy a bérlő GUID-azonosítója használható. |

Ha meg szeretné tudni, hogyan használhatja ezeket a [végpontokat,](#protocols) válasszon ki egy adott alkalmazástípust a Protokollok szakaszban, és kövesse a hivatkozásokat további információkért.

> [!TIP]
> Az Azure AD-ben regisztrált bármely alkalmazás használhatja a Microsoft identity platform végpontját, még akkor is, ha nem jelentkeznek be a személyes fiókokba.  Így a meglévő alkalmazásokat áttelepítheti a Microsoft identity platformra és az [MSAL-ra](reference-v2-libraries.md) anélkül, hogy újra létre tudná hozna az alkalmazást.

## <a name="tokens"></a>Tokenek

Az OAuth 2.0 és az OpenID Connect Microsoft identity platform implementációja széles körben használja a tulajdonosi jogkivonatokat, beleértve a JWT-ként ábrázolt tulajdonosi jogkivonatokat is. A tulajdonosi jogkivonat egy könnyű biztonsági jogkivonat, amely hozzáférést biztosít a "tulajdonos" egy védett erőforráshoz. Ebben az értelemben a "tulajdonos" minden olyan fél, amely be tudja mutatni a tokent. Bár az egyik fél először hitelesíti a Microsoft-identitás platform a tulajdonosi jogkivonat fogadásához, ha a szükséges lépéseket nem teszik meg a jogkivonat biztonságossá tétele az átvitel és a tárolás, akkor lehet elfogott és nem kívánt fél által használt. Míg egyes biztonsági jogkivonatok beépített mechanizmussal rendelkeznek, amely megakadályozza, hogy illetéktelenek jogosulatlan feleket használjanak, a tulajdonosi jogkivonatok nem rendelkeznek ezzel a mechanizmussal, és biztonságos csatornán, például a szállítási réteg biztonságán (HTTPS) kell szállítani őket. Ha egy tulajdonosi jogkivonat ot továbbít a tiszta, egy rosszindulatú fél használhatja a man-in-the-middle támadás a jogkivonat megszerzéséhez, és használja a védett erőforráshoz való jogosulatlan hozzáférés. Ugyanezek a biztonsági elvek vonatkoznak a bemutatóra szóló jogkivonatok későbbi használatra történő tárolására vagy gyorsítótárazására. Mindig győződjön meg arról, hogy az alkalmazás biztonságos módon továbbítja és tárolja a tulajdonosi jogkivonatokat. A bemutatóra szóló tokenekkel kapcsolatos további biztonsági szempontokat lásd az [RFC 6750 5.](https://tools.ietf.org/html/rfc6750)

A Microsoft identity platform végpontjában használt különböző típusú jogkivonatok további részletei [a Microsoft identity platform végponttoken-hivatkozásában](v2-id-and-access-tokens.md)érhetők el.

## <a name="protocols"></a>Protokollok

Ha készen áll néhány példakérés megtekintésére, ismerkedjen meg az alábbi oktatóanyagok egyikével. Mindegyik egy adott hitelesítési forgatókönyvnek felel meg. Ha segítségre van szüksége annak meghatározásához, hogy melyik a megfelelő folyamat az Ön számára, tekintse meg [a Microsoft identity platformmal létrehozható alkalmazások típusait.](v2-app-types.md)

* [Hozzon létre mobil és natív alkalmazást az OAuth 2.0-val](v2-oauth2-auth-code-flow.md)
* [Webalkalmazások készítése az OpenID Connect segítségével](v2-protocols-oidc.md)
* [Egyoldalas alkalmazások készítése az OAuth 2.0 Implicit folyamattal](v2-oauth2-implicit-grant-flow.md)
* [Démonok vagy kiszolgálóoldali folyamatok létrehozása az OAuth 2.0 ügyfél hitelesítő adatainak folyamatával](v2-oauth2-client-creds-grant-flow.md)
* [Tokenek beszereznie egy webes API-ban az OAuth 2.0-t a Flow nevében](v2-oauth2-on-behalf-of-flow.md)
