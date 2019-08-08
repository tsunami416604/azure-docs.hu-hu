---
title: A felhasználók áttelepíthetők a közösségi identitásokkal a Azure Active Directory B2Cban | Microsoft Docs
description: A közösségi identitásokkal rendelkező felhasználók áttelepítésével kapcsolatos főbb fogalmakat a Graph API használatával tárgyalhatja Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0117a0881422584e3cb949661b1d58cd0257cf67
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853865"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Felhasználók migrálása közösségi identitásokkal
Ha azt tervezi, hogy Azure AD B2Cra telepíti át az identitás-szolgáltatót, akkor a felhasználókat a közösségi identitásokkal is át kell telepítenie. Ez a cikk a meglévő közösségi identitások fiókjainak áttelepítését ismerteti, például: Facebook-, LinkedIn-, Microsoft-és Google-fiókok Azure AD B2C. Ez a cikk az összevont identitásokra is vonatkozik, azonban ezek a Migrálás kevésbé gyakoriak. A cikk további részében vegye figyelembe, hogy a közösségi fiókokra vonatkozó egyéb típusú összevont fiókok esetében is érvényesek.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk a felhasználói áttelepítési cikk folytatása, és a közösségi identitások áttelepítésére koncentrál. Mielőtt elkezdené, olvassa el a [felhasználó](active-directory-b2c-user-migration.md)áttelepítését.

## <a name="social-identities-migration-introduction"></a>A közösségi identitások áttelepítésének bemutatása

* Azure ad B2C a **helyi fiókok** bejelentkezési nevei (Felhasználónév vagy e-mail-cím) a `signInNames` gyűjteményben tárolódnak a felhasználói rekordban. A `signInNames` egy vagy több `signInName` olyan rekordot tartalmaz, amely megadja a felhasználó bejelentkezési nevét. Minden bejelentkezési névnek egyedinek kell lennie a bérlőn belül.

* A **közösségi fiókok** identitásait a `userIdentities` gyűjtemény tárolja. A bejegyzés megadja az `issuer` (identitás-szolgáltató nevét), például a Facebook.com `issuerUserId`és a, amely a kiállító egyedi felhasználói azonosítója. Az `userIdentities` attribútum egy vagy több UserIdentity-rekordot tartalmaz, amelyek meghatározzák a közösségi fiók típusát és a közösségi identitás-szolgáltató egyedi felhasználói azonosítóját.

* **Helyi fiók kombinálása a közösségi identitással**. Ahogy említettük, a helyi fiók bejelentkezési neve és a közösségi fiók identitásai különböző attribútumokban vannak tárolva. `signInNames`helyi fiók esetén használatos, míg `userIdentities` a közösségi fiókok esetében használatos. Egyetlen Azure AD B2C fiók csak helyi fiók lehet, csak közösségi fiók, vagy helyi fiók kombinálása egy vagy több közösségi identitással egy felhasználói rekordban. Ez a viselkedés lehetővé teszi egyetlen fiók kezelését, míg a felhasználó bejelentkezhet a helyi fiók hitelesítő adataival vagy a közösségi identitásokkal.

* `UserIdentity`Type (típus) – a közösségi fiók felhasználójának identitásával kapcsolatos információkat tartalmaz egy Azure AD B2C bérlőben:
  * `issuer`A felhasználói azonosítót kiállító identitás-szolgáltató karakterlánc-ábrázolása, például facebook.com.
  * `issuerUserId`A közösségi identitás-szolgáltató által Base64 kódolású formátumban használt egyedi felhasználói azonosító.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Az identitás-szolgáltatótól függően a **kiállító felhasználói azonosító** egyedi érték egy adott felhasználó számára alkalmazás-vagy fejlesztői fiókban. Konfigurálja a Azure AD B2C szabályzatot ugyanazzal az alkalmazás-AZONOSÍTÓval, amelyet korábban a közösségi szolgáltató vagy egy másik alkalmazás rendelt hozzá ugyanazon a fejlesztési fiókon belül.

## <a name="use-graph-api-to-migrate-users"></a>Felhasználók migrálása Graph API használatával
A Azure AD B2C felhasználói fiókot a [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet)használatával hozza létre. A Graph APIával folytatott kommunikációhoz először rendszergazdai jogosultságokkal rendelkező szolgáltatásfiók szükséges. Az Azure AD-ben regisztrálni kell egy alkalmazást és hitelesítést az Azure AD-ben. Az alkalmazás hitelesítő adatai az alkalmazás azonosítója és az alkalmazás titkos kulcsa. Az alkalmazás önmagát, nem pedig felhasználóként működik a Graph API meghívásához. Kövesse az 1. lépésben szereplő utasításokat a [felhasználói](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) áttelepítési cikkben.

## <a name="required-properties"></a>Kötelező tulajdonságai
A következő lista azokat a tulajdonságokat mutatja be, amelyek a felhasználó létrehozásakor szükségesek.
* **accountEnabled** – igaz
* **DisplayName** – a felhasználó címjegyzékében megjelenítendő név.
* **passwordProfile** – a felhasználó jelszavas profilja. 

> [!NOTE]
> Csak közösségi fiókok esetében (a helyi fiók hitelesítő adatai nélkül) továbbra is meg kell adnia a jelszót. Azure AD B2C figyelmen kívül hagyja a közösségi fiókokhoz megadott jelszót.

* **userPrincipalName** – a felhasználó egyszerű neve (someuser@contoso.com). Az egyszerű felhasználónévnek tartalmaznia kell az egyik ellenőrzött tartományt a bérlő számára. Az egyszerű felhasználónév megadásához adjon meg új GUID-értéket, `@` fűzze össze a és a bérlő nevét.
* **mailNickname** – a felhasználó levelezési aliasa. Ez az érték lehet a userPrincipalName használt azonosító. 
* **signInNames** – egy vagy több SignInName-rekord, amely megadja a felhasználó bejelentkezési nevét. Minden bejelentkezési névnek egyedinek kell lennie a vállalaton/bérlőn belül. Csak közösségi fiók esetén ez a tulajdonság üres maradhat.
* **userIdentities** – egy vagy több olyan UserIdentity-rekord, amely megadja a közösségi fiók típusát és a közösségi identitás-szolgáltató egyedi felhasználói azonosítóját.
* választható **otherMails** – csak közösségi fiók esetén a felhasználó e-mail-címei 

További információkért lásd: [Graph API referenciája](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Közösségi fiók migrálása (csak)
Ha csak közösségi fiókot szeretne létrehozni, helyi fiók hitelesítő adatai nélkül, küldjön egy HTTPS POST-kérelmet Graph API. A kérelem törzse tartalmazza a létrehozandó közösségi fiók felhasználójának tulajdonságait. Legalább a szükséges tulajdonságokat kell megadnia. 


**UTÁNI**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Küldje el a következő űrlapot: 

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
## <a name="migrate-social-account-with-local-account"></a>Közösségi fiók migrálása helyi fiókkal
Ha közösségi identitással rendelkező kombinált helyi fiókot szeretne létrehozni, küldjön egy HTTPS POST-kérelmet a Graph API. A kérelem törzse tartalmazza a közösségi fiókhoz tartozó felhasználó tulajdonságait, beleértve a helyi fiók bejelentkezési nevét is. Legalább a szükséges tulajdonságokat kell megadnia. 

**UTÁNI**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

A következő űrlap beküldése – az adatértékek: 

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
### <a name="how-can-i-know-the-issuer-name"></a>Honnan tudhatom meg a kiállító nevét?
A kiállító neve vagy az identitás-szolgáltató neve konfigurálva van a házirendben. Ha nem tudja `issuer`, hogy melyik értéket kell megadnia, kövesse az alábbi eljárást:
1. Bejelentkezés az egyik közösségi fiókkal
2. Az JWT-tokenből másolja az `sub` értéket. A `sub` általában a felhasználó objektumazonosító-azonosítóját tartalmazza Azure ad B2C. Vagy Azure Portal, nyissa meg a felhasználó tulajdonságait, és másolja az objektumazonosító.
3. Az [Azure ad Graph Explorer](https://graphexplorer.azurewebsites.net) megnyitása
4. Jelentkezzen be a rendszergazdájával.
5. Futtassa a következő GET kérelmet. Cserélje le a userObjectId a másolt felhasználói AZONOSÍTÓra. **GET**https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Keresse meg `userIdentities` a JSON-ban lévő elemet a Azure ad B2Cból.
7. Választható Érdemes lehet dekódolni az `issuerUserId` értéket is.

> [!NOTE]
> Használjon B2C bérlői rendszergazdai fiókot, amely helyi a B2C-bérlőhöz. A fióknév szintaxisa admin@tenant-name.onmicrosoft.com:.

### <a name="is-it-possible-to-add-a-social-identity-to-an-existing-user"></a>Lehetséges a közösségi identitás hozzáadása egy meglévő felhasználóhoz?
Igen. A közösségi identitást a Azure AD B2C fiók létrehozása után is hozzáadhatja (legyen az a helyi vagy közösségi fiók, vagy ezek kombinációja). HTTPS-javítási kérelem futtatása. Cserélje le a userObjectId a frissíteni kívánt felhasználói AZONOSÍTÓra. 

**JAVÍTÁS**https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

A következő űrlap beküldése – az adatértékek: 

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

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Lehet több közösségi identitást is felvenni?
Igen. Egyetlen Azure AD B2C-fiókhoz több közösségi identitást is hozzáadhat. HTTPS-javítási kérelem futtatása. Cserélje le a userObjectId a felhasználói AZONOSÍTÓra. 

**JAVÍTÁS**https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

A következő űrlap beküldése – az adatértékek: 

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

## <a name="optional-user-migration-application-sample"></a>Választható Felhasználói áttelepítési alkalmazás mintája
[Töltse le és futtassa a minta alkalmazás v2-es verzióját](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). A minta alkalmazás v2 egy olyan JSON-fájlt használ, amely a dummy felhasználói adattartalmakat tartalmazza, beleértve a következőket: helyi fiók, közösségi fiók és helyi & közösségi identitások egyetlen fiókban.  A JSON-fájl szerkesztéséhez nyissa `AADB2C.UserMigration.sln` meg a Visual Studio-megoldást. A projektben nyissa meg `UsersData.json` a fájlt. `AADB2C.UserMigration` A fájl tartalmazza a felhasználói entitások listáját. Minden felhasználói entitás a következő tulajdonságokkal rendelkezik:
* **signInName** – helyi fiók, a bejelentkezéshez használt e-mail-cím
* **DisplayName** – a felhasználó megjelenítendő neve
* **firstName** – a felhasználó keresztneve
* **lastName** – a felhasználó vezetékneve
* **jelszó** Helyi fiók esetén a felhasználó jelszava (üres is lehet)
* **kiállító** – közösségi fiók, identitás-szolgáltató neve
* **issuerUserId** – közösségi fiók esetén a közösségi identitás-szolgáltató által használt egyedi felhasználói azonosító. Az értéknek tiszta szövegnek kell lennie. A minta alkalmazás ezt az értéket Base64-re kódolja.
* **e-mail cím** Csak a közösségi fiók (nem együttesen), a felhasználó e-mail-címe

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
> Ha nem frissíti a UsersData. JSON fájlt a mintában az adataival, bejelentkezhet a helyi fiók hitelesítő adataival, de nem a közösségi fiókra vonatkozó példákkal. A közösségi fiókok átadásához valós adatait kell megadnia.

További információt a minta alkalmazás [használatáról a Azure Active Directory B2C: Felhasználói áttelepítés](active-directory-b2c-user-migration.md)
