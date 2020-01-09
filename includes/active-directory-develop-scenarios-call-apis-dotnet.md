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
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423759"
---
### <a name="authenticationresult-properties-in-msalnet"></a>AuthenticationResult tulajdonságai a MSAL.NET

A jogkivonatok beszerzésének módszerei `AuthenticationResult` (vagy az aszinkron metódusok esetében egy `Task<AuthenticationResult>`adnak vissza.

A MSAL.NET `AuthenticationResult` teszi a következőket:

- `AccessToken` a webes API számára az erőforrások eléréséhez. Ez a paraméter egy karakterlánc, általában Base64 kódolású JWT, de az ügyfélnek soha nem kell megkeresnie a hozzáférési jogkivonatban. A formátum nem garantált, hogy stabil maradjon, és titkosítva lehet az erőforrás számára. Az ügyfél hozzáférési jogkivonatának tartalmától függően a kód írása az egyik legnagyobb forrása a hibáknak és az ügyfél-logikai szüneteknek. Lásd még: [hozzáférési tokenek](../articles/active-directory/develop/access-tokens.md)
- `IdToken` a felhasználó számára (ez a paraméter egy kódolt JWT). Lásd: [azonosító jogkivonatok](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` megadja a jogkivonat lejáratának dátumát és időpontját
- `TenantId` tartalmazza azt a bérlőt, amelyben a felhasználó található. A vendég felhasználók (Azure AD B2B forgatókönyvek) esetében a bérlői azonosító a vendég bérlő, nem az egyedi bérlő.
Ha a tokent egy felhasználó számára továbbítják, `AuthenticationResult` a felhasználóval kapcsolatos információkat is tartalmaz. A bizalmas ügyfél-folyamatok esetében, amelyekben a rendszer a jogkivonatokat nem felhasználó (az alkalmazás esetében) kéri, ez a felhasználói információ null értékű.
- Az `Scopes`, amelyhez a tokent kiállították.
- A felhasználó egyedi azonosítója.

### <a name="iaccount"></a>IAccount

A MSAL.NET határozza meg a fiók fogalmát (a `IAccount` felületen keresztül). Ez a feltörési változás biztosítja a megfelelő szemantikai feltételt: az a tény, hogy ugyanaz a felhasználó több fiókkal is rendelkezhet különböző Azure AD-címtárakban. Emellett a MSAL.NET jobb információkat nyújt a vendég forgatókönyvek esetében, mivel az otthoni fiókadatok megadására szolgál.
Az alábbi ábrán az `IAccount` felület szerkezete látható:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

A `AccountId` osztály egy adott bérlő fiókját azonosítja. A következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|----------|-------------|
| `TenantId` | Egy GUID-azonosító karakterlánc-ábrázolása, amely annak a bérlőnek az azonosítója, amelyben a fiók található. |
| `ObjectId` | Egy GUID-azonosító karakterlánc-ábrázolása, amely a bérlőben a fiókot birtokló felhasználó azonosítója. |
| `Identifier` | A fiók egyedi azonosítója. a `Identifier` a `ObjectId` és a `TenantId` vesszővel elválasztott összefűzése, és nem Base64 kódolású. |

Az `IAccount` felület egyetlen fiókra vonatkozó információkat jelenít meg. Ugyanaz a felhasználó különböző bérlők között lehet, azaz egy felhasználó több fiókkal is rendelkezhet. Tagjai a következők:

| Tulajdonság | Leírás |
|----------|-------------|
| `Username` | A UserPrincipalName (UPN) formátumú, nem játszható értéket tartalmazó karakterlánc, például john.doe@contoso.com. Ez a karakterlánc lehet null, míg a HomeAccountId és az HomeAccountId. Identifier értéke nem lehet null. Ez a tulajdonság a MSAL.NET korábbi verzióiban lévő `IUser` `DisplayableId` tulajdonságát váltja fel. |
| `Environment` | A fiók identitás-szolgáltatóját tartalmazó karakterlánc, például `login.microsoftonline.com`. Ez a tulajdonság a `IUser``IdentityProvider` tulajdonságát váltja fel, azzal a különbséggel, hogy a `IdentityProvider` a bérlőre vonatkozó információkkal is rendelkezett (a felhőalapú környezeten kívül), míg itt az érték csak a gazdagép. |
| `HomeAccountId` | A felhasználó AccountId. Ez a tulajdonság egyedileg azonosítja a felhasználót az Azure AD-bérlők között. |

### <a name="using-the-token-to-call-a-protected-api"></a>Védett API meghívása a token használatával

Miután a MSAL visszaadta a `AuthenticationResult` (`result`), hozzá kell adnia a HTTP-engedélyezési fejléchez, mielőtt a meghívást a védett webes API-hoz való hozzáféréshez.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```