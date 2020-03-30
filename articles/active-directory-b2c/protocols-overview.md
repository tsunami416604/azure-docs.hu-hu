---
title: Hitelesítési protokollok az Azure Active Directory B2C könyvtárában | Microsoft dokumentumok
description: Alkalmazások létrehozása közvetlenül az Azure Active Directory B2C által támogatott protokollok használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183905"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Hitelesítési protokollok
Az Azure Active Directory B2C (Azure AD B2C) két iparági szabványnak megfelelő protokoll támogatásával biztosítja az alkalmazások identitását az alkalmazások számára. A szolgáltatás szabványoknak megfelelő, de ezeknek a protokolloknak bármely két implementációja lehet finom különbségek.

Az ebben az útmutatóban található információk akkor hasznosak, ha a kódot közvetlenül http-kérelmek küldésével és kezelésével írja, nem pedig nyílt forráskódú tár használatával. Javasoljuk, hogy olvassa el ezt az oldalt, mielőtt beleveti magát az egyes protokollok részleteibe. Ha azonban már ismeri az Azure AD B2C szolgáltatást, közvetlenül [a protokollreferencia-útmutatókra](#protocols)léphet.

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Az alapok
Az Azure AD B2C-t használó összes alkalmazást regisztrálni kell a B2C-címtárban az [Azure Portalon.](https://portal.azure.com) Az alkalmazásregisztrációs művelet során a rendszer összegyűjt bizonyos adatokat, majd értékeket rendel az alkalmazáshoz:

* **Application ID** (Alkalmazásazonosító), amely egyedileg azonosítja az alkalmazást.
* Átirányítási **URI** vagy **csomagazonosító,** amely segítségével a válaszokat visszairányíthatja az alkalmazásba.
* Néhány más forgatókönyv-specifikus értékeket. További információ: [az alkalmazás regisztrálása.](tutorial-register-applications.md)

Az alkalmazás regisztrálása után kommunikál az Azure Active Directoryval (Azure AD) a végpontra küldött kérelmek kel:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

Szinte minden OAuth és OpenID Connect forgalomban négy fél vesz részt a cserében:

![A négy OAuth 2.0-s szerepkört bemutató diagram](./media/protocols-overview/protocols_roles.png)

* Az **engedélyezési kiszolgáló** az Azure AD-végpont. Biztonságosan kezeli a felhasználói információkkal és hozzáféréssel kapcsolatos információkat. Azt is kezeli a bizalmi kapcsolatok a felek között egy folyamatban. Feladata a felhasználó identitásának ellenőrzése, az erőforrásokhoz való hozzáférés biztosítása és visszavonása, valamint a jogkivonatok kiadása. Identitásszolgáltatóként is ismert.

* Az **erőforrás tulajdonosa** általában a végfelhasználó. Az adatok tulajdonosa a fél, és joga van lehetővé tenni harmadik felek számára az adatokhoz vagy erőforrásokhoz való hozzáférést.

* Az **OAuth-ügyfél** az ön alkalmazása. Az alkalmazásazonosítója azonosítja. Általában ez az a fél, akivel a végfelhasználók interakcióba lépnek. Jogkivonatokat is kér az engedélyezési kiszolgálótól. Az erőforrás tulajdonosának engedélyt kell adnia az ügyfélnek az erőforrás eléréséhez.

* Az **erőforrás-kiszolgáló** az, ahol az erőforrás vagy az adat található. Megbízik abban, hogy az engedélyezési kiszolgáló biztonságosan hitelesítse és engedélyezze az OAuth-ügyfelet. Emellett tulajdonosi hozzáférési jogkivonatokat is használ annak biztosítására, hogy az erőforráshoz való hozzáférés biztosítható legyen.

## <a name="policies-and-user-flows"></a>Házirendek és felhasználói folyamatok
Vitathatatlanul az Azure AD B2C-szabályzatok a szolgáltatás legfontosabb funkciói. Az Azure AD B2C szabályzatok bevezetésével kiterjeszti a szabványos OAuth 2.0 és OpenID Connect protokollokat. Ezek lehetővé teszik az Azure AD B2C számára, hogy az egyszerű hitelesítésnél és engedélyezésnél sokkal több et hajtson végre.

A leggyakoribb identitáskezelési feladatok beállítása érdekében az Azure AD B2C portál előre definiált, konfigurálható szabályzatokat, úgynevezett **felhasználói folyamatokat**tartalmaz. A felhasználói folyamatok teljes mértékben leírják a fogyasztói identitásélményét, beleértve a regisztrációt, a bejelentkezést és a profilszerkesztést. A felhasználói folyamatok egy felügyeleti felhasználói felületen definiálhatók. Ezek a HTTP-hitelesítési kérelmekben speciális lekérdezési paraméterrel hajthatók végre.

A házirendek és a felhasználói folyamatok nem az OAuth 2.0 és az OpenID Connect szabványos szolgáltatásai, ezért időt kell szánnia ezek megértésére. További információ: az [Azure AD B2C felhasználói folyamat referenciaútmutatója.](user-flow-overview.md)

## <a name="tokens"></a>Tokenek
Az OAuth 2.0 és az OpenID Connect Azure AD B2C implementációja széles körben használja a tulajdonosi jogkivonatokat, beleértve a JSON webtokenek (JWT) ként képviselt tulajdonosi jogkivonatokat is. A tulajdonosi jogkivonat egy könnyű biztonsági jogkivonat, amely hozzáférést biztosít a "tulajdonos" egy védett erőforráshoz.

A tulajdonos minden olyan fél, amely be tudja mutatni a tokent. Az Azure AD először hitelesítenie kell egy fél, mielőtt egy tulajdonosi jogkivonatot kap. De ha a szükséges lépéseket nem teszik meg a jogkivonat biztonságossá tétele az átvitel és a tárolás, akkor lehet elfogott és nem kívánt fél által használt.

Egyes biztonsági jogkivonatok beépített mechanizmusokkal rendelkeznek, amelyek megakadályozzák a jogosulatlan felek használatát, de a tulajdonosi jogkivonatok nem rendelkeznek ezzel a mechanizmussal. Ezeket biztonságos csatornán, például átviteli rétegbiztonsággal (HTTPS) kell szállítani.

Ha egy tulajdonosi jogkivonatot egy biztonságos csatornán kívül továbbítanak, a rosszindulatú felek közbeékelődéses támadással szerezhetik be a jogkivonatot, és jogosulatlan hozzáférés megszerzésére használhatják a védett erőforrásokhoz való jogosulatlan hozzáférést. Ugyanezek a biztonsági elvek vonatkoznak a bemutatóra szóló jogkivonatok későbbi használatra történő tárolásakor vagy gyorsítótárazásában. Mindig győződjön meg arról, hogy az alkalmazás biztonságos módon továbbítja és tárolja a tulajdonosi jogkivonatokat.

További tulajdonosi jogkivonat-biztonsági szempontokat lásd az [RFC 6750 5.](https://tools.ietf.org/html/rfc6750)

Az Azure AD B2C-ben használt különböző típusú jogkivonatokról [az Azure AD tokenhivatkozásban talál további információt.](tokens-overview.md)

## <a name="protocols"></a>Protokollok
Ha készen áll néhány példakérés áttekintésére, az alábbi oktatóanyagok egyikével kezdheti. Mindegyik egy adott hitelesítési forgatókönyvnek felel meg. Ha segítségre van szüksége annak meghatározásához, hogy melyik folyamat a megfelelő az Ön számára, tekintse [meg, hogy milyen típusú alkalmazásokat hozhat létre az Azure AD B2C használatával.](application-types.md)

* [Mobil és natív alkalmazások készítése az OAuth 2.0 használatával](authorization-code-flow.md)
* [Webalkalmazások készítése az OpenID Connect használatával](openid-connect.md)
* [Egyoldalas alkalmazások létrehozása az OAuth 2.0 implicit folyamathasználatával](implicit-flow-single-page-application.md)

