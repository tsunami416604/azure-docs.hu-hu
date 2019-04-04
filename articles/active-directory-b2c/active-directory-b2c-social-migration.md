---
title: Az Azure Active Directory B2C közösségi identitású felhasználók áttelepítése |} A Microsoft Docs
description: Alapfogalmakat tárgyalja a közösségi identitású felhasználók Azure AD B2C-be Graph API-val, az áttelepítés.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 12b464d9b6bd09acb9c93ab1de0ba178f28a778a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894901"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Felhasználók migrálása közösségi identitásokkal
Ha azt tervezi, az identitásszolgáltató migrálása az Azure AD B2C-vel, szükség lehet a közösségi identitású felhasználók áttelepítése. Ez a cikk azt ismerteti, hogyan telepítheti át meglévő közösségi identitások fiókokat, például: Facebook, a LinkedIn, a Microsoft és a Google fiókok Azure AD B2C-t. Ez a cikk az összevont identitások kialakítása, is vonatkozik, azonban ezek áttelepítések kevésbé gyakoriak.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk a felhasználó áttelepítési cikk folytatása, és közösségi áttelepítési összpontosít. Mielőtt elkezdené, olvassa el a [felhasználóáttelepítés](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Közösségi identitások áttelepítési bemutatása

* Az Azure AD B2C- **helyi fiókok** bejelentkezési nevét (felhasználói név vagy e-mail cím) vannak tárolva a `signInNames` a felhasználói rekord gyűjteményhez. A `signInNames` tartalmaz egy vagy több `signInName` rekordokat, amelyek adja meg a felhasználó bejelentkezési nevét. Minden bejelentkezési nevének egyedinek kell lennie a bérlőket.

* **Közösségi fiókok** identitások tárolt `userIdentities` gyűjtemény. A bejegyzés határozza meg a `issuer` (identitásszolgáltató neve) például Facebook.com weboldalt, és a `issuerUserId`, amely az a kibocsátó egyedi felhasználói azonosító. A `userIdentities` attribútum egy vagy több UserIdentity olyan rekordokat tartalmaz, adja meg a közösségi fiók típusát és a közösségi identitásszolgáltatótól egyedi felhasználói azonosítót.

* **Helyi fiók kombinálva közösségi**. Ahogy említettük, a helyi fiók bejelentkezési nevét és a közösségi fiók identitások tárolódnak különböző attribútumokat. `signInNames` a helyi fiókot használt, miközben `userIdentities` közösségi fiók. Egyetlen Azure AD B2C-fiókot, csak a helyi fiók, a közösségi fiók csak, vagy helyi fiók kombinálhatja a közösségi identitású egy felhasználórekordban tárolja. Ez a viselkedés lehetővé teszi, hogy egyetlen fiókban, kezelheti, amíg egy felhasználó jelentkezhet be a helyi fiók credential(s) vagy a közösségi identitású.

* `UserIdentity` Írja be – a közösségi fiók felhasználó az Azure AD B2C-bérlő identitását információt tartalmaz:
  * `issuer` Az identitásszolgáltató a felhasználói azonosító, például Facebook.com weboldalt kiadó karakteres formáját.
  * `issuerUserId` Base64 formátumban a közösségi identitásszolgáltató által használt egyedi felhasználói azonosító.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Az identitásszolgáltató függően a **közösségi Felhasználóazonosító** egyedi érték alkalmazást vagy fejlesztői fiókonként az adott felhasználó számára. A közösségi hálózati szolgáltató által korábban hozzárendelt ugyanazon Alkalmazásazonosítóval konfigurálhatja az Azure AD B2C-szabályzathoz. Vagy egy adott fejlesztői fiókban egy másik alkalmazás.

## <a name="use-graph-api-to-migrate-users"></a>Felhasználók migrálása a Graph API használatával
Az Azure AD B2C felhasználói fiók használatával létrehozott [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). A Graph API-val folytatott kommunikációhoz, először kell rendelkeznie a szolgáltatás-rendszergazdai jogosultsággal rendelkező fiókot. Az Azure AD-ben regisztrál egy alkalmazás és az Azure AD-hitelesítés. Az alkalmazás hitelesítő adatok olyan Alkalmazásazonosítót és az alkalmazás titkos. Az alkalmazás maga nem felhasználóként, a Graph API meghívása funkcionál. Kövesse az utasításokat az 1. lépésben [felhasználóáttelepítés](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) cikk.

## <a name="required-properties"></a>Kötelező tulajdonságai
Az alábbi lista tartalmazza a tulajdonságokat, amelyeket van szükség, amikor egy felhasználó létrehoz.
* **accountEnabled** – igaz
* **displayName** – a felhasználó címjegyzékben megjelenítendő nevét.
* **passwordProfile** – a jelszót a felhasználó profilját. 

> [!NOTE]
> Közösségi fiók csak (nélküli helyi fiók hitelesítő adatait) továbbra is kell adnia a jelszót. Az Azure AD B2C figyelmen kívül hagyja a jelszót, adja meg a közösségi fiókok esetében.

* **userPrincipalName** – egyszerű felhasználónév (someuser@contoso.com). Egyszerű felhasználónév tartalmaznia kell egy ellenőrzött tartománynak a bérlő számára. Annak megadásához, az egyszerű Felhasználónevet, hozzon létre új GUID azonosítót, a ÖSSZEFŰZ `@` és a bérlő neve.
* **mailNickname** – a felhasználó e-mail-alias. Ez az érték a userPrincipalName tulajdonsághoz használt ugyanazzal az Azonosítóval lehet. 
* **signInNames** – adja meg a bejelentkezési nevét, a felhasználó egy vagy több SignInName rekordot. Minden bejelentkezési nevének egyedinek kell lennie a vállalat vagy a bérlőjéhez között. Csak közösségi fiók ezt a tulajdonságot is üresen kell hagyni.
* **userIdentities** -közösségi adjon meg egy vagy több UserIdentity rekordot fiók típusát és a közösségi identitásszolgáltatótól egyedi felhasználói azonosítót.
* [opcionális] **otherMails** – közösségi fiók csak a felhasználó e-mail-címei 

További információkért lásd: [Graph API-referencia](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Közösségi fiók (csak) áttelepítése
Csak a közösségi fiók létrehozása nélkül helyi fiók hitelesítő adatait. HTTPS POST kérés küldése a Graph API-t. A kérés törzse tartalmazza a felhasználó tulajdonságait a a közösségi fiók létrehozásához. Minimális meg kell adnia a szükséges tulajdonságokat. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

A következő űrlapadatok elküldése: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Közösségi fiók helyi fiókkal áttelepítése
A közösségi identitásuk hozzon létre egy kombinált helyi fiókot. HTTPS POST kérés küldése a Graph API-t. A kérés törzse tartalmazza a felhasználó tulajdonságait a a közösségi fiók létrehozásához. Minimális meg kell adnia a szükséges tulajdonságokat. 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Küldje el a következő képernyő-adatok: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="how-can-i-know-the-issuer-name"></a>Hogyan lehet, hogy a kibocsátó neve?
A kibocsátó neve vagy az identitásszolgáltató nevét, a szabályzat van konfigurálva. Ha nem ismeri az értéket adja meg a `issuer`, kövesse az alábbi lépéseket:
1. Jelentkezzen be a közösségi fiókok
2. A JWT jogkivonat másolja a `sub` értéket. A `sub` általában tartalmazza az Azure AD B2C-ben a felhasználó Objektumazonosítója. Vagy az Azure Portalon nyissa meg a felhasználó tulajdonságait és másolja az objektumazonosítót.
3. Nyissa meg [az Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net)
4. Jelentkezzen be a rendszergazdához.
5. Futtassa a következő GET kérelmet. Cserélje le a userObjectId kimásolt felhasználói azonosító. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Keresse meg a `userIdentities` elem található a JSON-ban lépjen vissza az Azure AD B2C-ből.
7. [Opcionális] Dekódolandó is érdemes a `issuerUserId` értéket.

> [!NOTE]
> A B2C-Bérlői rendszergazda fiókjával, helyi a B2C-bérlő használatához. A fiók nevének szintaxisa admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>A lehetséges közösségi hozzáadása egy meglévő helyi fiókot?
Igen. A közösségi identitását a helyi fiók létrehozása után is hozzáadhat. Futtassa a HTTPS-PATCH kérés. Cserélje le a userObjectId a frissíteni kívánt felhasználói azonosító. 

**JAVÍTÁS** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Küldje el a következő képernyő-adatok: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Az hozzáadhat több közösségi identitások?
Igen. Egyetlen Azure AD B2C fiókkal közösségi dentitásai adhat hozzá. Futtassa a HTTPS-PATCH kérés. A userObjectId cserélje le a felhasználói azonosítóját. 

**JAVÍTÁS** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Küldje el a következő képernyő-adatok: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>[Opcionális] Példa a migrálás felhasználói alkalmazás
[Töltse le és futtassa a mintaalkalmazást V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). A mintaalkalmazás V2, amely tartalmazza a próbafelhasználó adatok, beleértve a JSON-fájlt használ: helyi fiók, a közösségi fiók és a helyi és közösségi identitások egyetlen fiókban.  A JSON-fájl szerkesztéséhez nyissa meg a `AADB2C.UserMigration.sln` Visual Studio-megoldás. Az a `AADB2C.UserMigration` projektben nyissa meg a `UsersData.json` fájlt. A fájl felhasználói entitások listáját tartalmazza. Minden egyes felhasználó típusú entitás a következő tulajdonságokkal rendelkezik:
* **signInName** – helyi fiók, jelentkezzen be az e-mail-cím
* **displayName** -felhasználó megjelenített neve
* **firstName** -felhasználó utóneve
* **Vezetéknév** -felhasználó vezetékneve
* **jelszó** helyi fiók, jelszó a felhasználónak (üres is lehet)
* **kiállító** – közösségi fiók, az identitásszolgáltató neve
* **issuerUserId** – közösségi fiók, a közösségi identitásszolgáltató által használt egyedi felhasználói azonosító. Az érték lehet szövegként. A mintaalkalmazás kódolása base64 ezt az értéket.
* **e-mailek** a közösségi fiók csak (nem kombinált) felhasználó e-mail-cím

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Ha nem frissíti a UsersData.json fájlt a mintában az adatokkal, is bejelentkezik a minta helyi fiók hitelesítő adatait, de nem a közösségi fiók példák. A közösségi fiókok át, adja meg a tényleges adatok.

További információ a mintaalkalmazás használja, olvassa el [Azure Active Directory B2C: Felhasználói áttelepítése](active-directory-b2c-user-migration.md)
