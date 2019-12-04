---
title: A Microsoft Identity platform rendszergazdai engedélyezési protokolljai | Microsoft Docs
description: Az engedélyezés leírása a Microsoft Identity platform végpontján, beleértve a hatóköröket, az engedélyeket és a jóváhagyást.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e642ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b820be5631d207a32cbf14aa1eec9f3f6de2af52
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766055"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Rendszergazdai engedély a Microsoft Identity platformon

Bizonyos engedélyekhez a rendszergazdának kell beleegyeznie, mielőtt a bérlőn belül is megadhatók.  A rendszergazdai jogosultságok végpontját is használhatja, hogy engedélyeket adjon egy teljes bérlőnek.  

## <a name="recommended-sign-the-user-into-your-app"></a>Ajánlott: a felhasználó aláírása az alkalmazásba

Ha olyan alkalmazást hoz létre, amely a rendszergazdai jogosultságok végpontját használja, akkor az alkalmazásnak szüksége van egy olyan oldalra vagy nézetre, amelyben a rendszergazda jóváhagyhatja az alkalmazás engedélyeit. Ezen a lapon lehet az alkalmazás regisztrációs folyamatának része, az alkalmazás beállításainak egy része, vagy egy dedikált "kapcsolat" folyamat is. Sok esetben érdemes megmutatni, hogy az alkalmazás csak akkor jelenjen meg ez a "kapcsolódás" nézet, ha egy felhasználó munkahelyi vagy iskolai Microsoft-fiók bejelentkezett.

Amikor aláírja a felhasználót az alkalmazásba, azonosíthatja azt a szervezetet, amelyhez a rendszergazda tartozik, mielőtt megkéri őket a szükséges engedélyek jóváhagyására. Bár ez nem feltétlenül szükséges, így könnyebben hozhat létre egy intuitív felhasználói élményt a szervezeti felhasználók számára. A felhasználó aláírásához kövesse a [Microsoft Identity platform protokoll oktatóanyagokat](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>Engedélyek kérése egy címtár-rendszergazdától

Ha készen áll arra, hogy engedélyt kérjen a szervezete rendszergazdájától, átirányíthatja a felhasználót a Microsoft Identity platform *rendszergazdai engedélyezési végpontján*.

```
// Line breaks are for legibility only.
    GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
    &scope=
    https://graph.microsoft.com/calendars.read 
    https://graph.microsoft.com/mail.send
```


| Paraméter     | Állapot     | Leírás                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Szükséges | Az a címtár-bérlő, amelyre engedélyt szeretne kérni. A (z) GUID vagy a felhasználóbarát név formátumban adható meg, vagy általános módon hivatkozik `organizations`re, ahogy az a példában látható. Ne használja a "Common" kulcsszót, mert a személyes fiókok nem biztosíthatnak rendszergazdai jogosultságot, kivéve a bérlő kontextusát. A bérlőket kezelő személyes fiókokkal való legjobb kompatibilitás érdekében használja a bérlői azonosítót, ha lehetséges. |
| `client_id` | Szükséges | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `redirect_uri` | Szükséges |Az az átirányítási URI, ahová az alkalmazásnak el kell juttatnia a választ a kezelésére. Pontosan meg kell egyeznie az alkalmazás regisztrációs portálján regisztrált átirányítási URI-k egyikével. |
| `state` | Ajánlott | A kérelemben szereplő érték, amelyet a rendszer a jogkivonat-válaszban is visszaad. A kívánt tartalom sztringje lehet. Az állapot használatával kódolja a felhasználó állapotára vonatkozó adatokat az alkalmazásban, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
|`scope`        | Szükséges      | Meghatározza az alkalmazás által igényelt engedélyek készletét. Ez lehet statikus (/.default használatával) vagy dinamikus hatókörök használata.  Ilyenek lehetnek a OIDC hatókörök (`openid`, `profile`, `email`). | 


Ezen a ponton az Azure AD-nek a bérlői rendszergazdának kell bejelentkeznie a kérelem teljesítéséhez. A rendszergazda a `scope` paraméterben kért összes engedély jóváhagyását kéri.  Ha statikus (`/.default`) értéket használt, úgy fog működni, mint a v 1.0 rendszergazdai engedélyezési végpont, és az alkalmazáshoz szükséges engedélyekben található összes hatókörre vonatkozó kérelem beleegyezését kéri.

### <a name="successful-response"></a>Sikeres válasz

Ha a rendszergazda jóváhagyja az alkalmazás engedélyeit, a sikeres válasz a következőképpen néz ki:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Paraméter         | Leírás                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| A címtár-bérlő, amely az alkalmazást a kért engedélyekkel rendelkezik, GUID formátumban megadva.|
| `state`           | A kérelemben szereplő érték, amely a jogkivonat-válaszban is szerepelni fog. A kívánt tartalom sztringje lehet. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására szolgál a hitelesítési kérelem végrehajtása előtt, például az oldal vagy a nézet megtekintését.|
| `scope`          | Az alkalmazáshoz való hozzáférést engedélyező engedélyek készlete.|
| `admin_consent`   | `True`értékre lesz állítva.|

### <a name="error-response"></a>Hiba válasza

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

A sikeres válaszban látható paraméterekhez való hozzáadáskor a hiba paramétereit az alábbi módon tekintheti meg.

| Paraméter          | Leírás                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra.|
| `error_description`| Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hibák kiváltó okának azonosításában.|
| `tenant`| A címtár-bérlő, amely az alkalmazást a kért engedélyekkel rendelkezik, GUID formátumban megadva.|
| `state`           | A kérelemben szereplő érték, amely a jogkivonat-válaszban is szerepelni fog. A kívánt tartalom sztringje lehet. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására szolgál a hitelesítési kérelem végrehajtása előtt, például az oldal vagy a nézet megtekintését.|
| `admin_consent`   | A `True` értékre lesz állítva, jelezve, hogy ez a válasz rendszergazdai engedélyezési folyamaton történt.|

## <a name="next-steps"></a>Következő lépések
- Ismerje [meg, hogyan alakíthat át egy alkalmazást több-bérlővé](howto-convert-app-to-be-multi-tenant.md)
- Ismerje meg [, hogyan támogatott a hozzájárulás a OAuth 2,0 protokoll szintjén az engedélyezési kód engedélyezése során](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Ismerje meg, [Hogyan használhatják a több-bérlős alkalmazások a](active-directory-devhowto-multi-tenant-overview.md) "felhasználó" és a "rendszergazda" jogosultságot a "felhasználói" és "rendszergazdai" engedély megvalósításához, ami fejlettebb többrétegű alkalmazási mintákat is támogat.
- Az [Azure ad-alkalmazáshoz való beleegyezési tapasztalatok](application-consent-experience.md) ismertetése
