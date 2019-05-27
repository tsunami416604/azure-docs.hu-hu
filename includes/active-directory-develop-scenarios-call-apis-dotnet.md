---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121860"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Az MSAL.NET AuthenticationResult tulajdonságai

A tokenek beszerzésére módszerek adja vissza egy `AuthenticationResult` (vagy az aszinkron metódusok egy `Task<AuthenticationResult>`.

Az MSAL.NET `AuthenticationResult` tesz elérhetővé:

- `AccessToken` a webes API-erőforrások eléréséhez. Ez a paraméter egy karakterláncérték, általában egy base64 kódolású JWT, de az ügyfél soha nem kell kinéznie a hozzáférési jogkivonat belül. A formátum nem garantált, hogy állandó marad, és titkosíthatók az erőforráshoz. Személyek kód írása függően az ügyfél jogkivonat tartalmának eléréséhez a legnagyobb források hibák és az ügyfél logikai oldaltörések egyike. Lásd még: [hozzáférési jogkivonatokat](../articles/active-directory/develop/access-tokens.md)
- `IdToken` a felhasználó (ezt a paramétert a kódolt JWT-). Lásd: [azonosító-jogkivonatokat](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` arra utasítja a dátum/idő, amikor a jogkivonat lejár
- `TenantId` tartalmazza a bérlő, amelyben a felhasználó található. A vendégfelhasználók számára (Azure AD B2B-forgatókönyvekhez) a Bérlőazonosító a Vendég bérlő, nem a egyedi bérlő.
Ha a jogkivonat érkezik egy felhasználó `AuthenticationResult` a felhasználóval kapcsolatos információkat is tartalmaz. Bizalmas ügyfél folyamatokat, ahol nincs (a az alkalmazás) felhasználói jogkivonatok igényelt a felhasználói adatok má hodnotu null.
- A `Scopes` számára a token kiállították.
- A felhasználó egyedi azonosítója.

### <a name="iaccount"></a>IAccount

MSAL.NET meghatározza a fiók (keresztül a `IAccount` interface). Ez használhatatlanná tévő változást biztosítja a megfelelő szemantikát: az a tény, hogy ugyanaz a felhasználó rendelkezhet több fiókot, különböző Azure-ban AD-címtár. Is az MSAL.NET esetén a Vendég-forgatókönyveket, és ez jobb információt nyújt, otthoni fiókadatok biztosított.
Az alábbi ábrán látható szerkezete a `IAccount` felületen:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

A `AccountId` osztály egy fiókot az egyes bérlők azonosítja. A következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|----------|-------------|
| `TenantId` | A GUID, amely a bérlő Azonosítóját a fiókot tartalmazó karakterláncként. |
| `ObjectId` | A GUID, amely a fiók a bérlő felhasználójának azonosítója karakterláncként. |
| `Identifier` | A fiók egyedi azonosítója. `Identifier` van összefűzésén `ObjectId` és `TenantId` egy vesszőt és azok nem base64-kódolású elválasztva. |

A `IAccount` felület egyetlen fiókkal kapcsolatos információk jelöli. Lehet, hogy ugyanaz a felhasználó jelen a különböző bérlők, azt jelenti, egy felhasználó több fiókot lehet. A tagok a következők:

| Tulajdonság | Leírás |
|----------|-------------|
| `Username` | UserPrincipalName (UPN) formátumot, például az eredményben értéket tartalmazó karakterlánc john.doe@contoso.com. Ez a karakterlánc null értékű, lehet, mivel a HomeAccountId és HomeAccountId.Identifier nem lehet null értékű. Ez a tulajdonság felülírja a `DisplayableId` tulajdonsága `IUser` MSAL.NET korábbi verzióiban. |
| `Environment` | Az identitásszolgáltató, például a fiók, amely egy karakterlánc `login.microsoftonline.com`. Ez a tulajdonság felülírja a `IdentityProvider` tulajdonsága `IUser`, azzal a különbséggel, hogy `IdentityProvider` (mellett a felhőalapú környezetben), a bérlővel kapcsolatos információkat is volt, mivel itt értéke csak a gazdagép. |
| `HomeAccountId` | A felhasználó otthoni fiók AccountId. Ezt a tulajdonságot egyedileg azonosítja a felhasználó Azure AD-bérlőn. |

### <a name="using-the-token-to-call-a-protected-api"></a>A jogkivonat használatával védett API hívása

Egyszer a `AuthenticationResult` MSAL adta vissza (a `result`), adja hozzá a HTTP-engedélyezési fejléc hozzáférni a védett webes API-hívás végrehajtása előtt kell.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```