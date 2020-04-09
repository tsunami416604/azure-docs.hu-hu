---
title: Microsoft identity platform felügyeleti hozzájárulási protokolljai
description: A Microsoft identity platform végpontján az engedélyezés leírása, beleértve a hatóköröket, az engedélyeket és a hozzájárulást.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c9f633e0d205adaf5cefb2e3c036ce7f48253651
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886381"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Rendszergazdai hozzájárulás a Microsoft identitásplatformján

Egyes engedélyek hez a rendszergazda hozzájárulása szükséges ahhoz, hogy a bérlőn belül megadhatók legyenek.  A rendszergazdai hozzájárulási végpont használatával engedélyeket adhat a teljes bérlőnek.  

## <a name="recommended-sign-the-user-into-your-app"></a>Ajánlott: Írja alá a felhasználót az alkalmazásba

Általában, ha olyan alkalmazást hoz létre, amely a rendszergazdai hozzájárulási végpontot használja, az alkalmazásnak szüksége van egy lapra vagy nézetre, amelyben a rendszergazda jóváhagyhatja az alkalmazás engedélyeit. Ez az oldal része lehet az alkalmazás regisztrációs folyamatának, az alkalmazás beállításainak része, vagy lehet egy dedikált "connect" folyamat. Sok esetben célszerű, hogy az alkalmazás csak akkor jelenítse meg ezt a "connect" nézetet, ha a felhasználó bejelentkezett egy munkahelyi vagy iskolai Microsoft-fiókkal.

Amikor bejelentkezik a felhasználóaz alkalmazásba, azonosíthatja azt a szervezetet, amelyhez a rendszergazda tartozik, mielőtt megkérné őket a szükséges engedélyek jóváhagyására. Bár nem feltétlenül szükséges, segíthet egy intuitívabb felhasználói élmény létrehozásában. A felhasználó bejelentkezéséhez kövesse a [Microsoft identity platform protokoll oktatóanyagait.](active-directory-v2-protocols.md)

## <a name="request-the-permissions-from-a-directory-admin"></a>Engedélyek kérése címtáradminisztrátortól

Ha készen áll arra, hogy engedélyeket kérjen a szervezet rendszergazdájától, átirányíthatja a felhasználót a Microsoft identity platform *rendszergazdájának hozzájárulási végpontjára.*

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
| `tenant` | Kötelező | Az a címtár-bérlő, amelytől engedélyt szeretne kérni. Guid vagy rövid névformátumban vagy általánosan `organizations` hivatkozott, amint az a példában látható, megadható. Ne használja a "közös", a személyes fiókok nem adhat rendszergazdai jóváhagyást, kivéve a bérlő ikörnyezetében. A bérlőket kezelő személyes fiókokkal való legjobb kompatibilitás biztosítása érdekében, ha lehetséges, használja a bérlőazonosítót. |
| `client_id` | Kötelező | Az **Alkalmazás (ügyfél) azonosító,** amely az [Azure Portal – Alkalmazás regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) az alkalmazáshoz rendelt élmény. |
| `redirect_uri` | Kötelező |Az átirányítási URI, ahol azt szeretné, hogy a választ kell küldeni az alkalmazás kezelni. Pontosan meg kell egyeznie az alkalmazásregisztrációs portálon regisztrált átirányítási URI-k egyikével. |
| `state` | Ajánlott | A kérelemben szereplő érték, amely a jogkivonat-válaszban is vissza kerül. Ez lehet egy sor bármilyen tartalmat akarsz. Az állapot segítségével kódolhat információkat a felhasználó állapotáról az alkalmazásban a hitelesítési kérelem bekövetkezése előtt, például azt az oldalt vagy nézetet, amelyen voltak. |
|`scope`        | Kötelező      | Az alkalmazás által kért engedélyek készletét határozza meg. Ez lehet statikus (a /.default használatával) vagy dinamikus hatókörök.  Ez magában foglalhatja az`openid`OIDC hatóköröket ( , `profile`, `email`. ). | 


Ezen a ponton az Azure AD megköveteli a bérlői rendszergazda a kérelem teljesítéséhez. A rendszergazdának jóvá kell hagynia a `scope` paraméterben kért összes engedélyt.  Ha statikus (`/.default`) értéket használt, az a v1.0-s rendszergazdai hozzájárulási végponthoz hasonlóan fog működni, és az alkalmazáshoz szükséges engedélyekben található összes hatókörhöz beleegyezést kér.

### <a name="successful-response"></a>Sikeres válasz

Ha a rendszergazda jóváhagyja az alkalmazás engedélyeit, a sikeres válasz a következőképpen néz ki:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Paraméter         | Leírás                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| Az a címtár-bérlő, amely megadta az alkalmazásnak a kért engedélyeket GUID formátumban.|
| `state`           | A kérelemben szereplő érték, amely a jogkivonat-válaszban is visszakerül. Ez lehet egy sor bármilyen tartalmat akarsz. Az állapot a felhasználó állapotára vonatkozó információk kódolására szolgál az alkalmazásban a hitelesítési kérelem bekövetkezése előtt, például az oldal vagy a nézet, amelyen voltak.|
| `scope`          | Az alkalmazáshoz hozzáférést kapott engedélyek készlete.|
| `admin_consent`   | Lesz beállítva, hogy `True`.|

### <a name="error-response"></a>Hibaválasz

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

A sikeres válaszban látható paraméterekhez hozzáadva a hibaparaméterek az alábbiak szerint jelennek meg.

| Paraméter          | Leírás                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható.|
| `error_description`| Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hiba kiváltó okának azonosításában.|
| `tenant`| Az a címtár-bérlő, amely megadta az alkalmazásnak a kért engedélyeket GUID formátumban.|
| `state`           | A kérelemben szereplő érték, amely a jogkivonat-válaszban is visszakerül. Ez lehet egy sor bármilyen tartalmat akarsz. Az állapot a felhasználó állapotára vonatkozó információk kódolására szolgál az alkalmazásban a hitelesítési kérelem bekövetkezése előtt, például az oldal vagy a nézet, amelyen voltak.|
| `admin_consent`   | Úgy lesz `True` beállítva, hogy jelezze, hogy ez a válasz egy rendszergazdai hozzájárulási folyamaton történt.|

## <a name="next-steps"></a>További lépések
- Tekintse [meg, hogyan alakíthat át egy alkalmazást több-bérlőssé](howto-convert-app-to-be-multi-tenant.md)
- Ismerje meg, hogyan [támogatja a jóváhagyást az OAuth 2.0 protokollrétegen az engedélyezési kód engedélyezési folyamata során.](v2-oauth2-auth-code-flow.md#request-an-authorization-code)
- Ismerje [meg, hogyan egy több-bérlős alkalmazás használhatja a jóváhagyási keretet](active-directory-devhowto-multi-tenant-overview.md) a "felhasználó" és a "rendszergazda" hozzájárulás megvalósításához, támogatva a fejlettebb többrétegű alkalmazásmintákat.
- Az [Azure AD-alkalmazások hozzájárulási élményei](application-consent-experience.md)
