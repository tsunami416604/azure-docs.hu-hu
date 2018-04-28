---
title: 'Az Azure Active Directory B2C: Áttelepítheti a felhasználókat a közösségi identitások'
description: Alapfogalmak ismertetik a közösségi identitások az Azure AD B2C Graph API-val rendelkező felhasználók áttelepítése
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/03/2018
ms.author: davidmu
ms.openlocfilehash: a150d8255171c83334ac4c1f81cbcee39c1df70a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Az Azure Active Directory B2C: Áttelepítheti a felhasználókat a közösségi identitások
Ha Identitásszolgáltatóként az Azure AD B2C áttelepítését tervezi, is szükség lehet áttelepítheti a felhasználókat a közösségi identitásokat. Ez a cikk azt ismerteti, hogyan telepíthet át meglévő közösségi identitások fiókokat, például: az Azure AD B2C Facebook, a LinkedIn, a Microsoft és a Google fiókokat. Ez a cikk vonatkozik is összevont identitások kialakítása, azonban ezek áttelepítések amelyek kevésbé gyakran.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk a felhasználó áttelepítési cikk folytatása, és közösségi identitás áttelepítési összpontosít. Mielőtt elkezdené, olvassa el a [felhasználóáttelepítés](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Közösségi identitások áttelepítési bemutatása

* Az Azure AD B2C-ben **helyi fiókok** bejelentkezési nevét (felhasználói név vagy e-mail cím) tárolja a `signInNames` gyűjtemény a felhasználói rekordban. A `signInNames` egy vagy több tartalmaz `signInName` azt jelzi, hogy a felhasználó bejelentkezési nevét adja meg. Minden egyes bejelentkezési nevének egyedinek kell lennie a bérlő között.

* **Közösségi fiókok** identitások tárolódnak `userIdentities` gyűjtemény. A bejegyzés határozza meg a `issuer` (identity szolgáltató neve) például Facebook.com weboldalt, és a `issuerUserId`, vagyis a kibocsátó egy egyedi felhasználói azonosító. A `userIdentities` attribútum egy vagy több UserIdentity olyan rekordokat tartalmaz, adja meg a közösségi típusa és a közösségi az identitásszolgáltató által egyedi felhasználói azonosító.

* **Helyi fiók egyesíthető közösségi identitás**. Ahogy azt korábban említettük, a helyi fiók bejelentkezési név és közösségi fiók identitások tárolódnak különböző attribútumokat. `signInNames` van helyi fiókot használja, miközben `userIdentities` közösségi fiók. Egyetlen Azure AD B2C-fiókkal is egy csak a helyi fiók, csak a közösségi fiók, vagy helyi fiók, közösségi ad meg egy felhasználói rekord egyesítése. Ez a viselkedés lehetővé teszi, hogy ugyanazt a fiókot, kezelheti, amíg egy felhasználó bejelentkezhessen a helyi fiók credential(s) be vagy a közösségi identitásokat.

* `UserIdentity` Írja be – az Azure AD B2C-bérlő egy közösségi felhasználó identitásának információt tartalmaz:
    * `issuer` Az identitásszolgáltató felhasználói azonosítóját, például a Facebook.com weboldalt kiadott karakterláncos ábrázolása.
    * `issuerUserId` A base64 formátumban közösségi identitásszolgáltató által használt egyedi felhasználói azonosító.

    ```JSON
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
    ```

* Az identitásszolgáltatótól függően a **közösségi Felhasználóazonosító** egy egyedi érték, egy alkalmazás vagy a fejlesztői fiók az adott felhasználó számára. Az Azure AD B2C házirend konfigurálása a közösségi szolgáltató korábban hozzárendelt azonos alkalmazás-azonosítóval. Vagy a azonos fejlesztői fiókban egy másik alkalmazás.

## <a name="use-graph-api-to-migrate-users"></a>A Graph API segítségével áttelepítheti a felhasználókat
Az Azure AD B2C felhasználói fiók segítségével létrehozhat [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). A Graph API-val folytatott kommunikációhoz, először rendelkeznie kell egy szolgáltatás-rendszergazdai jogosultságokkal rendelkező fiók. Az Azure Active Directoryban regisztrálja a az alkalmazás és az Azure AD hitelesítési. Az alkalmazás hitelesítő adatok Alkalmazásazonosítót és az Alkalmazáskulcsot. Az alkalmazás maga nem felhasználóként, a Graph API hívása funkcionál. Kövesse az utasításokat az 1. lépésben a [felhasználóáttelepítés](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration#step-1-use-graph-api-to-migrate-users) cikk.

## <a name="required-properties"></a>Kötelező tulajdonságok
Az alábbi listában láthatók a tulajdonságokról van szükség, ha a felhasználó létrehozása.
* **accountEnabled** – igaz
* **displayName** – a felhasználó címjegyzékében megjelenítendő nevét.
* **passwordProfile** -a felhasználói jelszó profilját. 

> [!NOTE]
> Csak (helyi fiók hitelesítő adatait) nélkül közösségi fiók továbbra is meg kell a jelszót. Az Azure AD B2C figyelmen kívül hagyja a közösségi fiókok számára megadott jelszót.

* **userPrincipalName** -egyszerű felhasználónév (someuser@contoso.com). A felhasználó egyszerű felhasználóneve tartalmaznia kell egy a ellenőrzött tartományai a bérlő számára. A felhasználónév megadásához létrehozni új GUID azonosítót, a ÖSSZEFŰZ `@` és a bérlő neve.
* **mailNickname** – a levelezési alias a felhasználó számára. Az érték a userPrincipalName tulajdonsághoz használt ugyanezzel az Azonosítóval lehet. 
* **signInNames** -a felhasználói bejelentkezési neveket adjon meg egy vagy több SignInName rögzíti. Minden egyes bejelentkezés nevének egyedinek kell lennie a vállalati/bérlők között. Csak olyan közösségi fiókhoz ezt a tulajdonságot is üresen kell hagyni.
* **userIdentities** -egy vagy több UserIdentity jelzi, hogy adja meg a társadalombiztosítási fiók típusa és a közösségi identitásszolgáltató egyedi felhasználói azonosítóját.
* [választható] **otherMails** - közösségi fiók csak, a felhasználó e-mail címéhez 

További információkért lásd: [Graph API-referencia](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>(Csak) közösségi fiók áttelepítése
Csak, a közösségi fiók létrehozása nélkül helyi fiók hitelesítő adatait. A Graph API HTTPS POST-kérelmet küld. A kérelem törzsében létrehozásához közösségi fiók felhasználó tulajdonságait tartalmazza. Minimális meg kell adnia a szükséges tulajdonságait. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Küldje el a következő képernyő-adatokat: 

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
## <a name="migrate-social-account-with-local-account"></a>Közösségi fiók helyi fiók áttelepítése
Kombinált helyi fiók közösségi azonosítókkal való létrehozásához. A Graph API HTTPS POST-kérelmet küld. A kérelem törzsében létrehozásához közösségi fiók felhasználó tulajdonságait tartalmazza. Minimális meg kell adnia a szükséges tulajdonságait. 

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
### <a name="how-can-i-know-the-issuer-name"></a>Hogyan szerezhetek tudomást arról, a kibocsátó neve?
A kibocsátó neve, vagy az identitás-szolgáltató neve a házirend van beállítva. Ha nem tudja az értéket adja meg a `issuer`, kövesse az alábbi eljárást:
1. Jelentkezzen be az egyik közösségi fiók
2. A JWT jogkivonat másolja a `sub` érték. A `sub` általában az a felhasználó objektum Azonosítóját az Azure AD B2C tartalmazza. Vagy az Azure-portálról, nyissa meg a felhasználó tulajdonságok és másolja az objektum azonosítójával.
3. Nyissa meg [az Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net)
4. Jelentkezzen be a rendszergazdához. N
5. Futtassa a következő GET kérelmet. Cserélje le a userObjectId másolt felhasználói azonosítóval. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Keresse meg a `userIdentities` elem az Azure AD B2C JSON visszatérési belül.
7. [Választható] Is érdemes lehet dekódolni a `issuerUserId` érték.

> [!NOTE]
> A B2C bérlői rendszergazdai fiókot használjon, amely a B2C-bérlő helyi. A fiók nevének szintaxisa admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>Az közösségi-identitás hozzáadása egy meglévő helyi fiókot is?
Igen. A közösségi identitást a helyi fiók létrehozása után is hozzáadhat. Kérelem HTTPS javítás futtatásához. Cserélje le a userObjectId frissíti a felhasználói azonosítóját. 

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

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Az adhat közösségi identitásai?
Igen. Egy olyan Azure AD B2C-fiók közösségi identitásai adhat hozzá. Kérelem HTTPS javítás futtatásához. A userObjectId cserélje le a felhasználói azonosítóját. 

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

## <a name="optional-user-migration-application-sample"></a>[Választható] Felhasználó áttelepítési alkalmazás minta
[Töltse le és futtassa a mintaalkalmazást V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). A mintaalkalmazás V2 használ dummy felhasználói adatokat, beleértve a tartalmazó JSON-fájl: helyi fiók, a közösségi fiók és az egyetlen fiók h & elyi közösségi identitás.  A JSON-fájl szerkesztéséhez nyissa meg a `AADB2C.UserMigration.sln` Visual Studio megoldás. Az a `AADB2C.UserMigration` projektben nyissa meg a `UsersData.json` fájlt. A fájl felhasználói entitások listáját tartalmazza. Minden felhasználó entitás tulajdonságai a következők:
* **signInName** – helyi fiók, bejelentkezés egy e-mail címet
* **displayName** -felhasználó megjelenített neve
* **Keresztnév** -felhasználó utónevét
* **Vezetéknév** -felhasználó vezetékneve
* **jelszó** helyi fiók, jelszó (üres is lehet)
* **kibocsátó** - közösségi fiók, az identitás-szolgáltató neve
* **issuerUserId** - közösségi fiókhoz, a közösségi identitásszolgáltató által használt egyedi felhasználói azonosító. Az érték egyszerű szövegként kell lennie. A mintaalkalmazás kódolja base64 ezt az értéket.
* **e-mailek** a közösségi fiók csak (nem kombinált), felhasználó e-mail címe

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
> Ha nem frissíti a UsersData.json fájlt a mintában az adatait, akkor is jelentkezzen be a minta helyi fiók hitelesítő adatait, de nem a közösségi fiók példák. A közösségi fiókok áttelepítéséhez meg valós.

További információt szeretne használni a mintaalkalmazást, olvassa el [Azure Active Directory B2C: felhasználói áttelepítése](active-directory-b2c-user-migration.md)
