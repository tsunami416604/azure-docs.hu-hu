---
title: További tudnivalók az Azure AD v2.0 által támogatott hitelesítési protokollok |} Microsoft Docs
description: Az Azure AD v2.0-végponttól által támogatott protokollok útmutatóját.
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 29d9e2d9ee05b755ef40179e0e75fb0c8a6b010b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="v20-protocols---oauth-20--openid-connect"></a>v2.0 protokoll - OAuth 2.0-s & OpenID Connect
A v2.0-végpontra használhatja az Azure AD identity,--szolgáltatás az iparági szabványos protokollok, OpenID Connectet és az OAuth 2.0-s.  Szabványokkal kompatibilis a szolgáltatás pedig finom eltérések vannak ezek a protokollok minden két közötti lehet.  Az adatok itt akkor lehet hasznos, ha úgy dönt, hogy a kód írása útján tájékoztatjuk & kezelése HTTP-kérelmek vagy a 3. fél nyílt forráskódú szalagtár helyett egyikével a [nyissa meg a forrás szalagtárak](active-directory-v2-libraries.md).

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók támogatják a v2.0-végponttól.  Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>
>

## <a name="the-basics"></a>Az alapok
Szinte minden OAuth & OpenID Connect adatfolyamok nincsenek négy felek az exchange részt:

![OAuth 2.0 szerepkörök](../../media/active-directory-v2-flows/protocols_roles.png)

* A **engedélyezési Server** van a v2.0-végponttól.  Ez felelős biztosítása a felhasználói azonosító megadása és erőforrásokhoz való hozzáférés visszavonása és jogkivonatok kiállítása.  Más néven az identitásszolgáltató - biztonságosan kezelési semmit, a felhasználó adatait, a hozzáférés és a megbízhatósági kapcsolatok egy folyamat felek között.
* A **erőforrás tulajdonosa** általában a végfelhasználó van.  Az entitás, amely az adatok tulajdonosa, és lehetővé teszik a harmadik felek adott adatok, vagy az erőforrás elérésére jogosult.
* A **OAuth ügyfél** az alkalmazás azonosítja az alkalmazás azonosítóját.  Ez általában a felet, a végfelhasználó kommunikál, és jogkivonatok kér a hitelesítési kiszolgáló.  Az ügyfél által az erőforrás tulajdonosa erőforrás eléréséhez engedéllyel kell rendelkezni.
* A **erőforrás-kiszolgáló** van, amelyben az erőforrás vagy adatok található.  A hitelesítési kiszolgáló biztonságosan helyszerepkörre, és az OAuth-ügyfél megbízik, és a tulajdonosi access_tokens segítségével győződjön meg arról, hogy az erőforráshoz való hozzáférés is adható.

## <a name="app-registration"></a>Alkalmazás regisztrálása
Minden alkalmazást, amely használja a v2.0-végponttal kell regisztrálható [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) előtt OAuth vagy az OpenID Connect használhatják.  Az alkalmazásregisztrációs művelet során fog gyűjteni & néhány értéket hozzárendelni az alkalmazást:

* Egy **alkalmazásazonosító** , amely egyedileg azonosítja az alkalmazást
* A **átirányítási URI-** vagy **csomagazonosítót** , amely közvetlen válaszokhoz az alkalmazáshoz használható
* Néhány más forgatókönyvekre jellemző értékeket.

További részletekért ismerkedjen meg az [alkalmazások regisztrálásának folyamatával](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Végpontok
Regisztrálás után az alkalmazás kommunikál az Azure AD által küldött kérésekkel a v2.0-végponttal:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Ha a `{tenant}` négy különböző értékek valamelyikét hajthatja végre:

| Érték | Leírás |
| --- | --- |
| `common` |Lehetővé teszi a felhasználók személyes Microsoft-fiókok és a munkahelyi vagy iskolai fiókokhoz az Azure Active Directory, az alkalmazás aláírásához. |
| `organizations` |Az Azure Active Directory jelentkezzen be az alkalmazás lehetővé teszi, hogy csak a munkahelyi vagy iskolai fiókkal rendelkező felhasználók. |
| `consumers` |Lehetővé teszi, hogy csak a felhasználók a személyes Microsoft-fiókokkal (msa-t) az alkalmazás aláírásához. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` |Lehetővé teszi, hogy csak a munkahelyi vagy iskolai fiókkal rendelkező felhasználók egy adott Azure Active Directory-bérlőhöz bejelentkezni az alkalmazásba.  Az Azure AD-bérlő rövid tartomány nevét vagy guid-azonosító a bérlő is használható. |

További információ a fenti végpontokkal kezelésének módját válasszon az alábbi adott alkalmazás típust.

## <a name="tokens"></a>Tokenek
OAuth 2.0 és az OpenID Connect v2.0 végrehajtásának kihasználása kiterjedt tulajdonosi jogkivonatok, beleértve a tulajdonosi jogkivonatok JWTs ábrázolva. Egy tulajdonosi jogkivonatot egy egyszerűsített biztonsági jogkivonatot, amely védett erőforrásokhoz való hozzáférést a "tulajdonos". Abban az értelemben a "tulajdonos", amely a token is jelenthet félre. Egy entitás először hitelesítenie kell magát a tulajdonosi jogkivonattal, fogadni az Azure AD, ha a szükséges lépéseket a rendszer nem hajtja végre a lexikális elem szerepel az átvitel, illetve tárolás biztosításához, ha hozzá, és egy nem kívánt félnek használják. Míg néhány biztonsági jogkivonatokat egy beépített mechanizmust meggátolja, hogy a nem hitelesített felek használja őket, a tulajdonosi jogkivonatok nem rendelkezik a mechanizmus, és kell szállítani, például a transport layer security (HTTPS) biztonságos csatorna. Egy tulajdonosi jogkivonatot továbbított szövegként, ha egy man-a a középső támadás segítségével egy rosszindulatú fél jogkivonat és a védett erőforrásokhoz való illetéktelen hozzáférés használni. Az azonos biztonsági elveket alkalmazza, ha a tárolást, vagy a gyorsítótárazás tulajdonosi jogkivonatok későbbi használatra. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságos módon tárolja a tulajdonosi jogkivonatokhoz. További biztonsági szempontok a tulajdonosi jogkivonatok, lásd: [RFC 6750 szakasz 5](http://tools.ietf.org/html/rfc6750).

További részletek a jogkivonatok a v2.0-végpontra használt különböző típusú érhető el [a v2.0 jogkivonat referenciái végpont](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protokollok
Ha készen áll a talál néhány példa kérelmeket, az egyik első lépései az alábbi oktatóanyagok.  Mindegyik megfelel egy adott hitelesítési forgatókönyv.  Ha a jobb oldali folyamata megállapításához segítségre van szüksége, tekintse meg [milyen típusú alkalmazásokat hozhat létre a v2.0 rendelkező](active-directory-v2-flows.md).

* [Mobileszköz- és natív alkalmazás OAuth 2.0-val hozhat létre.](active-directory-v2-protocols-oauth-code.md)
* [Build webes alkalmazások nyitott azonosítójú kapcsolódhatnak](active-directory-v2-protocols-oidc.md)
* [Az OAuth 2.0 implicit engedélyezési folyamat egyoldalas alkalmazások létrehozása](active-directory-v2-protocols-implicit.md)
* [Build démonok vagy a kiszolgáló oldalán folyamatok az OAuth 2.0 ügyfél hitelesítő adatait Flow](active-directory-v2-protocols-oauth-client-creds.md)
* [A Web API-t az OAuth 2.0 más nevében Flow a jogkivonatok lekérésére](active-directory-v2-protocols-oauth-on-behalf-of.md)
