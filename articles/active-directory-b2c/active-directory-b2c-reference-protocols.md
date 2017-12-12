---
title: "Az Azure Active Directory B2C: Hitelesítési protokollok |} Microsoft Docs"
description: "Olyan alkalmazások fordítása közvetlenül az Azure Active Directory B2C által támogatott protokollok használatával"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 5e407d0a-73a2-4d74-ac81-3aa6c31ddcee
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.openlocfilehash: eb5d0da5b723c5fef81f7d74936ae30d4366385c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Az Azure AD B2C: Hitelesítési protokollok
Az Azure Active Directory B2C két iparági szabványos protokollok támogatása (az Azure AD B2C) biztosít az az alkalmazások szolgáltatásként identitás: OpenID Connectet és az OAuth 2.0-s. A szolgáltatás szabványoknak megfelelő, de ezeket a protokollokat bármely két implementációja rendelkezhet finom eltérések vannak. 

Ez az útmutató az információk akkor hasznos, ha közvetlenül küldésével és HTTP-kérelmek kezelése, nem pedig egy nyílt forráskódú könyvtár használatával írhatja a kódot. Azt javasoljuk, hogy olvassa el ezen a lapon az ahhoz, hogy mélyedjen el az adott protokollokat részleteit. De ha már ismeri az Azure AD B2C, nyissa meg rögtön [a protokoll hivatkozási útmutatók](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Az alapok
Minden Azure AD B2C alkalmazó alkalmazásban regisztrálva kell lennie a B2C-címtárban lévő a [Azure-portálon](https://portal.azure.com). Az alkalmazásregisztrációs művelet során a rendszer összegyűjt bizonyos adatokat, majd értékeket rendel az alkalmazáshoz:

* **Application ID** (Alkalmazásazonosító), amely egyedileg azonosítja az alkalmazást.
* A **átirányítási URI-** vagy **csomag azonosítója** , amely közvetlen válaszokhoz az alkalmazáshoz használható.
* Néhány más forgatókönyvekre jellemző értékeket. További információt a további [az alkalmazás regisztrálása](active-directory-b2c-app-registration.md).

Az alkalmazás regisztrálása után kommunikál Azure Active Directory (Azure AD) a végpontnak küldött kérésekkel:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Szinte minden OAuth és az OpenID Connect adatfolyamok, a négy felek használnak az exchange:

![OAuth 2.0 szerepkörök](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* A **engedélyezési server** az Azure AD-végpont. Biztonságosan kezelési semmit a felhasználói adatok és való hozzáféréssel kapcsolatos. A folyamat a felek közötti megbízhatósági kapcsolatokat is kezeli. Ez felelős a felhasználói identitás ellenőrzése, megadásának és erőforrásokhoz való hozzáférés visszavonása és a jogkivonatok kiállítása. Akkor is az identitásszolgáltató.

* A **erőforrás tulajdonosa** általában a végfelhasználó van. Azt a felet, birtokolja az adatokat, és azt, hogy a harmadik felek adott adatok vagy az erőforrás elérésére jogosult.

* A **OAuth ügyfél** az alkalmazás. Ez azonosítja az alkalmazás azonosítóját. Azt az általában a felet, a végfelhasználók interakciót. A hitelesítési kiszolgáló jogkivonatokat is kéri. Az erőforrás tulajdonosa engedélyt kell az ügyfél az erőforrás elérésére.

* A **erőforrás-kiszolgáló** van, amelyben az erőforrás vagy adatok található. A hitelesítési kiszolgáló biztonságosan helyszerepkörre, és az OAuth-ügyfél megbízhatónak fogja tekinteni. Annak érdekében, hogy az erőforráshoz való hozzáférés is adható tulajdonosi jogkivonatot is használ.

## <a name="policies"></a>Szabályzatok
Az Azure AD B2C-házirendek késései, a legfontosabb funkciókról a szolgáltatás olyan. Az Azure AD B2C a szabványos OAuth 2.0 és az OpenID Connect protokollok házirendek bevezetésével kiterjeszti. Ezek teszik lehetővé az Azure AD B2C sokkal több, mint az egyszerű hitelesítés és engedélyezés végrehajtásához. 

Házirendek teljes leírása fogyasztói identitással kapcsolatos műveletet, beleértve a regisztráció, bejelentkezés, profil és szerkesztését. Házirendek meghatározása egy rendszergazda felhasználói felületén. A HTTP-hitelesítési kérelmek egy speciális lekérdezési paraméter segítségével végrehajthatók. 

Házirendek olyan nem szabványos OAuth 2.0 és az OpenID Connect, szolgáltatásait, ezek megértéséhez időt kell végrehajtani. További információkért lásd: a [házirend referencia-útmutató az Azure AD B2C](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokenek
OAuth 2.0 és az OpenID Connect Azure AD B2C végrehajtását teszi tulajdonosi jogkivonatok, beleértve a tulajdonosi jogkivonatok JSON webes jogkivonatok (JWTs) helyettesítik használatának lehetőségét. Egy tulajdonosi jogkivonatot egy egyszerűsített biztonsági jogkivonatot, amely védett erőforrásokhoz való hozzáférést a "tulajdonos".

A tulajdonosi, amely a token is jelenthet félre. Az Azure AD először hitelesíteniük kell egy entitás előtt megkaphatja a tulajdonosi jogkivonattal. De ha a szükséges lépéseket a rendszer nem hajtja végre a lexikális elem szerepel az átvitel, illetve tárolás biztosításához, azt is hozzá és egy nem kívánt fél által használt.

Néhány biztonsági jogkivonatokat rendelkezik beépített mechanizmust, amely megakadályozhatja, hogy a nem hitelesített felek a őket, de tulajdonosi jogkivonatok nem rendelkezik a mechanizmus. Azok a biztonságos csatornákat, például egy a transport layer security (HTTPS) kell szállítani. 

Ha egy tulajdonosi jogkivonatot kívül egy biztonságos csatornán kerül továbbításra, egy rosszindulatú entitás segítségével a-átjárójának támadás jogkivonat és annak segítségével védett erőforrásokhoz való jogosulatlan hozzáférést. Az azonos biztonsági elveket alkalmazza, ha a tulajdonosi jogkivonatok tárolt és gyorsítótárba helyezni későbbi használat. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságos módon tárolja a tulajdonosi jogkivonatokhoz.

További tulajdonosi jogkivonat biztonsági szempontjait, lásd: [RFC 6750 szakasz 5](http://tools.ietf.org/html/rfc6750).

További információ a különböző típusú jogkivonatokat, amelyek az Azure AD B2C találhatók [az Azure AD-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protokollok
Amikor készen áll a tekintse át az egyes példa kérések, megkezdheti az alábbi oktatóanyagok egyike. Mindegyike megfelel egy adott hitelesítési forgatókönyv. Ha annak meghatározásakor, amelyben az Ön számára legmegfelelőbb segítségre van szüksége, tekintse meg [milyen típusú alkalmazásokat hozhat létre az Azure AD B2C segítségével](active-directory-b2c-apps.md).

* [Mobil- és natív alkalmazások létrehozását OAuth 2.0 használatával](active-directory-b2c-reference-oauth-code.md)
* [Webalkalmazások OpenID Connect használatával összeállítása](active-directory-b2c-reference-oidc.md)
* [Egyoldalas alkalmazások használata az OAuth 2.0 típusú implicit engedélyezési folyamat](active-directory-b2c-reference-spa.md)

