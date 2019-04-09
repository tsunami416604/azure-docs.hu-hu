---
title: További tudnivalók az Azure AD v2.0 által támogatott hitelesítési protokollok |} A Microsoft Docs
description: Egy útmutató, amellyel az Azure AD v2.0-végpont által támogatott protokollok.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56970091da74cfc389d60ad91f430fcb64d4bba
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266970"
---
# <a name="v20-protocols---oauth-20-and-openid-connect"></a>v2.0 protokoll – OAuth 2.0 és OpenID Connect

A v2.0-végpont használhatja az Azure Active Directory (Azure AD) identitás--szolgáltatásként az iparági szabványos protokollok, OpenID Connect és az OAuth 2.0. Szabványokkal kompatibilis a szolgáltatás pedig finom eltérések ezeket a protokollokat bármely két megvalósítása között lehet. Az adatok itt lesznek lehet hasznos, ha úgy dönt, hogy a kód írása közvetlenül küldésével és HTTP-kérelmek kezelésére, vagy egy harmadik féltől származó nyílt forráskódú kódtár, ahelyett, hogy az egyik a [nyílt forráskódú könyvtáraink](reference-v2-libraries.md).

> [!NOTE]
> A 2.0-s verziójú végpont nem minden Azure AD-forgatókönyvet és -funkciót támogat. Annak megállapításához, ha a v2.0-végpont használja, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Az alapok

A gyakorlatilag az összes OAuth 2.0 és OpenID Connect folyamatok találhatók négy felek részt az exchange:

![Az OAuth 2.0-szerepkörök](../../media/active-directory-v2-flows/protocols_roles.png)

* A **az engedélyezési kiszolgáló** a v2.0-végpont, és biztosításáért felelős a felhasználó identitását, biztosítása és erőforrásokhoz való hozzáférés visszavonása, és a kiállító tokeneket. Az engedélyezési kiszolgáló más néven az identitásszolgáltató - biztonságosan kezelési semmit sem kell elvégezni a megbízhatósági kapcsolatok egy folyamatot a felek között, a felhasználói adatok és azok elérhető.
* A **erőforrás tulajdonosa** van általában a végfelhasználó számára. A nyilvános, az adatok tulajdonosa, és a teljesítményt, hogy a harmadik felek, adatok vagy az erőforrás eléréséhez.
* A **OAuth-ügyfél** használ az alkalmazás azonosítja az alkalmazás azonosítója. Az OAuth-ügyfél általában a felet, amellyel a felhasználó kommunikál, és tokenek kér az engedélyezési kiszolgálón. Az ügyfél által az erőforrás tulajdonosa erőforrás elérésére engedéllyel kell rendelkezni.
* A **erőforrás-kiszolgáló** van, ahol az erőforrásra vagy található. Biztonságos hitelesítéséhez és engedélyezéséhez az OAuth-ügyfél az engedélyezési kiszolgáló megbízik, és győződjön meg arról, hogy az erőforrásokhoz való hozzáférést is megadható tulajdonosi hozzáférési jogkivonatok használatával.

## <a name="app-registration"></a>Alkalmazásregisztráció

Minden alkalmazás, amely fogadja el a személyes és munkahelyi vagy iskolai fiókkal szeretne regisztrálni kell az új keresztül **alkalmazásregisztrációk (előzetes verzió)** felületét a [az Azure portal](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) előtt jelentkezzen be ezeket a felhasználókat OAuth 2.0 vagy OpenID Connect használatával. Az alkalmazásregisztrációs művelet során összegyűjti, és néhány értéket rendelni az alkalmazást:

* Egy **Alkalmazásazonosító** , amely egyedileg azonosítja az alkalmazást
* A **átirányítási URI-t** vagy **csomagazonosítót** , amely közvetlen válaszokhoz az alkalmazáshoz használható
* Néhány más forgatókönyv-specifikus értékeket.

További részletekért ismerkedjen meg az [alkalmazások regisztrálásának folyamatával](quickstart-v2-register-an-app.md).

## <a name="endpoints"></a>Végpontok

Regisztrálása után az alkalmazás kommunikál az Azure AD v2.0-végpontra való kérések küldésével:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Ha a `{tenant}` négy különböző értékek valamelyikét hajthatja végre:

| Érték | Leírás |
| --- | --- |
| `common` | Lehetővé teszi, hogy a személyes Microsoft-fiókok és a munkahelyi vagy iskolai fiókokkal rendelkező felhasználók jelentkezhetnek be az alkalmazás Azure AD-ből. |
| `organizations` | Lehetővé teszi, hogy csak a munkahelyi vagy iskolai fiókkal rendelkező felhasználók jelentkezhetnek be az alkalmazás Azure AD-ből. |
| `consumers` | Lehetővé teszi, hogy csak a felhasználók a személyes Microsoft-fiókok (MSA) jelentkezzen be az alkalmazást. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` | Lehetővé teszi, hogy csak egy adott Azure AD-ből a munkahelyi vagy iskolai fiókkal rendelkező felhasználók jelentkezhetnek be az alkalmazás-bérlőben. Az Azure AD-bérlő rövid tartományneve, vagy a bérlő GUID-azonosító is használható. |

Megtudhatja, hogyan kezelheti ezeket a végpontokat, az adott alkalmazás típusának kiválasztása a [protokollok](#protocols) szakaszt, és kövesse a hivatkozásokat, további információ.

> [!TIP]
> Bármely olyan alkalmazásban, az Azure AD-ben regisztrált használhatja a v2.0-végpont, még akkor is, ha a személyes fiókokat nem bejelentkeznek.  Ily módon telepíthet át meglévő alkalmazásokat pedig v2.0 és [MSAL](reference-v2-libraries.md) nélkül hozza létre újra az alkalmazást.  

## <a name="tokens"></a>Tokenek

Az OAuth 2.0 és OpenID Connect v2.0 megvalósítását győződjön erősen igénybe veszi a tulajdonosi jogkivonatokat, beleértve a tulajdonosi jogkivonatokat JWTs-kiszolgálókként. A tulajdonosi jogkivonatot, amely hozzáférést biztosít a "tulajdonos" védett erőforrásokhoz való könnyű biztonsági jogkivonat. Ebben az értelemben a "tulajdonos" minden olyan entitás, amely a token is jelenthet. Bár egy entitás először hitelesítenie kell magát az Azure AD-fogadni a tulajdonosi jogkivonattal, ha a szükséges lépéseket a rendszer nem hajtja végre a biztonságos átvitelét és tárolását a jogkivonatot, hozzá, és egy nem kívánt entitás használják. Néhány biztonsági jogkivonatokat rendelkezik egy beépített mechanizmus megakadályozza az illetéktelen fél használja őket, amíg a tulajdonosi jogkivonatokat nem kell ezt a mechanizmust, és kell szállítani, például a transport layer security (HTTPS) biztonságos csatornán. A titkosítatlan továbbított tulajdonosi jogkivonattal, ha egy rosszindulatú fél használatával a man-in-the-middle támadások beszerezni a jogkivonatot, és használja a védett erőforrások jogosulatlan hozzáférést. A biztonsági alapelveket alkalmazható, ha a tárolásával, sem a gyorsítótárazás a tulajdonosi jogkivonatokat későbbi használatra. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságosan tárolja a tulajdonosi jogkivonatokat. A tulajdonosi jogkivonatokat további biztonsági szempontokért lásd: [RFC 6750 5. szakasz](https://tools.ietf.org/html/rfc6750).

További részletekért lásd a v2.0-végpont használt tokenek különböző típusú érhető el a [a v2.0-végpont jogkivonat referenciái](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokollok

Ha szeretné látni, néhány példa kérelmek, az egyik első lépései az alábbi oktatóanyagok. Mindegyik felel meg egy adott hitelesítési forgatókönyv. Ha az Ön számára a megfelelő folyamat megállapításához segítségre van szüksége, tekintse meg az [a típusú alkalmazásokat hozhat létre a v2.0-](v2-app-types.md).

* [Az OAuth 2.0-val a mobil- és natív alkalmazás létrehozása](v2-oauth2-auth-code-flow.md)
* [Web Apps alkalmazások OpenID-kapcsolattal](v2-protocols-oidc.md)
* [Az OAuth 2.0 Implicit folyamat egyoldalas alkalmazások fejlesztése](v2-oauth2-implicit-grant-flow.md)
* [Démonok vagy az OAuth 2.0 ügyfél-hitelesítési folyamata kiszolgálóoldali folyamatok létrehozása](v2-oauth2-client-creds-grant-flow.md)
* [A jogkivonatok lekérésére, webes API-t az OAuth 2.0--meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md)
