---
title: Az Azure Active Directory B2C a hitelesítési protokollok |} A Microsoft Docs
description: Hogyan hozhat létre alkalmazásokat közvetlenül az Azure Active Directory B2C által támogatott protokollok használatával.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: de7c40bcf93eae357ad99613caa2274f9e77d884
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853068"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Hitelesítési protokollok
Az Azure Active Directory B2C (Azure AD B2C-vel) identitást biztosít az alkalmazások szolgáltatás két, az iparági szabványos protokollok támogatása révén: OpenID Connect és az OAuth 2.0-s. A szolgáltatás szabványoknak megfelelő, de bármilyen két implementációiban ezeket a protokollokat is finom eltérések vannak. 

Ez az útmutató információkat akkor hasznos, ha a kód írása közvetlenül küldésével és HTTP-kérelmek kezelésére, nem pedig egy nyílt forráskódú kódtár használatával. Azt javasoljuk, hogy ezen a lapon olvassa el, mielőtt alaposabban elmerülne egyes adott protokoll részleteit. Ha már ismeri az Azure AD B2C-vel, megnyithatja közvetlenül, de [a protokoll referencia útmutatók](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Az alapok
Az Azure AD B2C-t használó alkalmazásokat a B2C-címtárban szerepelnie kell a [az Azure portal](https://portal.azure.com). Az alkalmazásregisztrációs művelet során a rendszer összegyűjt bizonyos adatokat, majd értékeket rendel az alkalmazáshoz:

* **Application ID** (Alkalmazásazonosító), amely egyedileg azonosítja az alkalmazást.
* A **átirányítási URI-t** vagy **csomag azonosítója** , amely közvetlen válaszokhoz az alkalmazáshoz használható.
* Néhány más forgatókönyv-specifikus értékeket. További információkért tekintse meg [hogyan regisztrálhatja alkalmazását](active-directory-b2c-app-registration.md).

Miután regisztrálta az alkalmazást, akkor kommunikál az Azure Active Directory (Azure AD) a végponthoz való kérések küldésével:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

Szinte minden OAuth és OpenID Connect folyamatokban négy felek vesz részt, az exchange:

![Az OAuth 2.0-szerepkörök](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* A **az engedélyezési kiszolgáló** van az Azure AD-végpont. Ez biztonságos felhasználói és hozzáférési kapcsolódó egyik elemet sem kezeli. A megbízhatósági kapcsolatok egy flow-ban a felek között is kezeli. Ez felelős a felhasználó identitásának igazolására, biztosítása és erőforrásokhoz való hozzáférés visszavonása és a jogkivonatok kiállítása. Más néven az identitásszolgáltató.

* A **erőforrás tulajdonosa** van általában a végfelhasználó számára. Az entitás, amely az adatok tulajdonosa, és rendelkezik a teljesítmény, hogy a harmadik felek, adatok vagy az erőforrás eléréséhez.

* A **OAuth-ügyfél** használ az alkalmazás. Ez azonosítja az alkalmazás azonosítóját. Ez általában az, hogy a végfelhasználók együttműködő fél. Az engedélyezési kiszolgáló a tokeneket is kéri. Az erőforrás tulajdonosa engedélyt kell adni az ügyfél az erőforrás eléréséhez.

* A **erőforrás-kiszolgáló** van, ahol az erőforrásra vagy található. Az engedélyezési kiszolgáló biztonságos hitelesítéséhez és engedélyezéséhez az OAuth-ügyfél megbízik. Győződjön meg arról, hogy az erőforrásokhoz való hozzáférést is megadható a tulajdonosi jogkivonatot is használ.

## <a name="policies-and-user-flows"></a>Szabályzatok és a felhasználói folyamatok
Késései az Azure AD B2C-szabályzatok a szolgáltatás legfontosabb funkcióit. Az Azure AD B2C a szabványos OAuth 2.0 és OpenID Connect protokollok szabályzatok bevezetésével kiterjeszti. Ezek lehetővé teszik az Azure AD B2C-vel sokkal jobban, mint az egyszerű hitelesítés és engedélyezés. 

Segítséget a leggyakoribb feladatok identitás beállítása, az Azure AD B2C-portálon előre meghatározott, a konfigurálható szabályzatok nevű tartalmaz **felhasználókövetési adatai**. Felhasználói folyamatok teljes körű ismertetik a fogyasztói identitások jellemzőit, beleértve a regisztrációs, bejelentkezési, és profil szerkesztése. Felhasználói folyamatok egy rendszergazdai felhasználói felületen lehet definiálni. Ezek a HTTP-hitelesítési kéréseket egy speciális lekérdezési paraméter használatával hajthatók végre. 

Szabályzatok és felhasználókövetési adatai nem el szabványos OAuth 2.0 és OpenID Connect, szolgáltatásait, értelmezését időt kell tennie. További információkért lásd: a [Azure AD B2C felhasználói folyamat referencia-útmutató](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokenek
Az OAuth 2.0 és OpenID Connect Azure AD B2C-vel megvalósítását teszi erősen igénybe veszi a tulajdonosi jogkivonatokat, beleértve a tulajdonosi jogkivonatokat, amelyek JSON webes jogkivonatainak (JWTs) helyettesítik. A tulajdonosi jogkivonatot, amely hozzáférést biztosít a "tulajdonos" védett erőforrásokhoz való könnyű biztonsági jogkivonat.

A tulajdonosi minden olyan entitás, amely a token is jelenthet. Azure ad-ben először hitelesítenie kell magát egy entitás előtt megkaphatja a tulajdonosi jogkivonattal. De ha a rendszer nem hajtja végre a szükséges lépéseket, biztonságos átvitelét és tárolását a jogkivonatot, azt is lehessen elfogni és egy nem kívánt entitás használja.

Néhány biztonsági jogkivonatokat, amelyek meggátolják, hogy a nem hitelesített felek használja őket a beépített mechanizmusok rendelkezik, de ez a mechanizmus nem rendelkezik tulajdonosi jogkivonatokat. Egy biztonságos csatornát, például a transport layer biztonsági (HTTPS) kell szállítani. 

Tulajdonosi jogkivonattal továbbított kívül egy biztonságos csatornán, ha egy rosszindulatú fél használhatja a man-in-the-middle támadások beszerezni a jogkivonatot, és a védett erőforrások jogosulatlan elérésére. Biztonsági alapelveket alkalmazható, ha a tulajdonosi jogkivonatokat tárolt vagy későbbi használatra a gyorsítótárba. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságosan tárolja a tulajdonosi jogkivonatokat.

További tulajdonosi jogkivonat biztonsági szempontokért lásd: [RFC 6750 5. szakasz](https://tools.ietf.org/html/rfc6750).

A különböző típusú jogkivonatokat, amelyek az Azure AD B2C-vel kapcsolatos további információk érhetők el a [az Azure AD-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protokollok
Amikor elkészült, tekintse át az egyes például kérések, elkezdheti a következő oktatóanyagok egyikével. Minden egyes felel meg egy adott hitelesítési forgatókönyv. Ha, amely meghatározza, hogy mely folyamat az Önnek való segítségre van szüksége, tekintse meg az [a típusú alkalmazásokat hozhat létre Azure AD B2C használatával](active-directory-b2c-apps.md).

* [OAuth 2.0-val a mobil- és natív alkalmazások készítését](active-directory-b2c-reference-oauth-code.md)
* [OpenID Connect használatával hozhat létre webalkalmazásokat](active-directory-b2c-reference-oidc.md)
* [Az OAuth 2.0 implicit folyamat használatával egyoldalas alkalmazások készítése](active-directory-b2c-reference-spa.md)

