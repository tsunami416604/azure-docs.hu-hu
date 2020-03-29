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
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76308928"
---
### <a name="authenticationresult-properties-in-msalnet"></a>AuthenticationResult tulajdonságai a MSAL.NET

A tokenek beszerzésének `AuthenticationResult`módszerei tadoznak vissza. Az aszinkron `Task<AuthenticationResult>` metódusok esetén a visszatérést adja vissza.

A MSAL.NET, `AuthenticationResult` kiteszi:

- `AccessToken`a webes API-t az erőforrások eléréséhez. Ez a paraméter egy karakterlánc, általában egy Base-64 kódolású JWT. Az ügyfél soha ne nézzen a hozzáférési jogkivonatba. A formátum nem garantált, hogy stabil marad, és az erőforrás titkosítható. A hozzáférési jogkivonat tartalmától függő kód írása az ügyfélegyik legnagyobb hibaforrása és az ügyféllogikai törések. További információt az [Access-jogkivonatok](../articles/active-directory/develop/access-tokens.md)című témakörben talál.
- `IdToken`a felhasználó számára. Ez a paraméter kódolt JWT. További információt az [Azonosító-jogkivonatok című témakörben](../articles/active-directory/develop/id-tokens.md)talál.
- `ExpiresOn`a token lejáratának dátuma és időpontja.
- `TenantId`tartalmazza azt a bérlőt, amelyben a felhasználó tanéven található. Az Azure Active Directory (Azure AD) B2B-forgatókönyvek vendégfelhasználói számára a bérlői azonosító a vendégbérlő, nem pedig az egyedi bérlő.
Amikor a jogkivonat ot `AuthenticationResult` kézbesítik egy felhasználónak, a felhasználóra vonatkozó információkat is tartalmaz. Bizalmas ügyfélfolyamatok esetén, ahol a jogkivonatokat az alkalmazás felhasználója nélkül kérik, ez a felhasználói információ null értékű.
- Az, `Scopes` amelyre a jogkivonatot kiadták.
- A felhasználó egyedi azonosítója.

### <a name="iaccount"></a>IAccount

MSAL.NET határozza meg a fiók `IAccount` fogalmát a felületen keresztül. Ez a törés változás biztosítja a megfelelő szemantika. Ugyanaz a felhasználó több fiókkal is rendelkezhet, különböző Azure AD-könyvtárakban. Emellett MSAL.NET jobb információkat nyújt a vendégesetek esetében, mivel az otthoni fiók adatai meg vannak adva.
Az alábbi ábra a `IAccount` kapcsolat szerkezetét mutatja be.

![IAccount felület szerkezete](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Az `AccountId` osztály egy adott bérlőben lévő fiókot azonosít az alábbi táblázatban látható tulajdonságokkal.

| Tulajdonság | Leírás |
|----------|-------------|
| `TenantId` | A GUID karakterlánc-ábrázolása, amely annak a bérlőnek az azonosítója, ahol a fiók található. |
| `ObjectId` | A GUID karakterlánc-ábrázolása, amely annak a felhasználónak az azonosítója, aki a bérlőben a fiók tulajdonosa. |
| `Identifier` | A fiók egyedi azonosítója `Identifier`a vessző összefűzése `ObjectId` és `TenantId` vesszővel való elválasztása. Nem a 64-es bázis kódolva vannak. |

A `IAccount` felület egyetlen fiók adatait jelöli. Ugyanaz a felhasználó különböző bérlőkben is jelen lehet, ami azt jelenti, hogy a felhasználó több fiókkal is rendelkezhet. Tagjai az alábbi táblázatban láthatók.

| Tulajdonság | Leírás |
|----------|-------------|
| `Username` | Felhasználófelhasználónév (UPN) formátumban megjeleníthető értéket tartalmazó karakterlánc, john.doe@contoso.compéldául . Ez a karakterlánc null értékű lehet, ellentétben a HomeAccountId és a HomeAccountId.Identifier, amely nem lesz null. Ez a tulajdonság `DisplayableId` helyettesíti `IUser` a MSAL.NET korábbi verzióinak tulajdonságát. |
| `Environment` | A fiók identitásszolgáltatóját tartalmazó karakterlánc, `login.microsoftonline.com`például . Ez a tulajdonság `IdentityProvider` helyettesíti `IUser`a `IdentityProvider` tulajdonságát, kivéve, hogy a felhőkörnyezet mellett a bérlőről is rendelkezett információkkal. Itt az érték csak a gazdagép. |
| `HomeAccountId` | A felhasználó otthoni fiókjának azonosítója. Ez a tulajdonság egyedileg azonosítja a felhasználót az Azure AD-bérlők között. |

### <a name="use-the-token-to-call-a-protected-api"></a>Védett API-hívás a jogkivonat használatával

Miután `AuthenticationResult` az MSAL `result`visszaadja a rendszerben, adja hozzá a HTTP-engedélyezési fejléchez, mielőtt a védett webes API eléréséhez hívást kezdeményezne.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```