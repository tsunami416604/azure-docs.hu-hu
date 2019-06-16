---
title: Védett webes API - alkalmazás regisztrációja |} Az Azure
description: Ismerje meg, hogyan hozhat létre egy védett webes API-t és az alkalmazás regisztrálásához szükséges információkat.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fe71c38678ae789a93ecbc956f24f0b0ebeb01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111129"
---
# <a name="protected-web-api---app-registration"></a>Védett webes API - alkalmazás regisztrációja

Ez a cikk azt ismerteti, hogy egy védett webes API-alkalmazás regisztrációs adatait.

Lásd: [a rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platform az](quickstart-register-app.md) hogyan kell regisztrálni az alkalmazás általános lépéseit.

## <a name="accepted-token-version"></a>Token elfogadott verzió

A Microsoft identity platform végpont kiadhatnak kétféle jogkivonatokat: 1.0-s verziójú jogkivonatok és 2.0-s verziójú jogkivonatok. Ezek a jogkivonatok többet tudhat [hozzáférési jogkivonatokat](access-tokens.md). Az elfogadott token verzió attól függ, a **támogatott fióktípusok** választotta, hogy az alkalmazás létrehozásakor:

- Ha értékét **támogatott fióktípusok** van **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok (például a Skype, Xbox, Outlook.com)** , az elfogadott token verzió v2.0 lesz.
- Ellenkező esetben az elfogadott jogkivonat-verzió lesz az 1.0-s verziója.

Az alkalmazás létrehozása után módosíthatja az elfogadott token verzió az alábbi lépéseket:

1. Az Azure Portalon válassza ki az alkalmazást, és válassza ki a **Manifest** az alkalmazáshoz.
2. A jegyzékfájlban, keressen **"accessTokenAcceptedVersion"** , és láthatja, hogy a kulcs értéke **2**. Ez a tulajdonság lehetővé teszi, hogy az Azure ad-ben tudja, hogy a webes API 2.0-s verziójú jogkivonatokat fogad. Ha **null**, az elfogadott jogkivonat-verzió lesz 1.0-s verziója.
3. Kattintson a **Mentés** gombra.

> [!NOTE]
> Szolgáltatás a webes API-nak eldönteni, melyik token verziója (1.0 vagy 2.0-s verzió) fogad el. Ügyfelek kérnek egy jogkivonatot a webes API-t a Microsoft identity platform v2.0-végpont használatával, ha azok egy jogkivonatot, amely azt jelzi, hogy melyik verzió van fogadja el a webes API-t fog kapni.

## <a name="no-redirect-uri"></a>Nincs átirányítási URI-ja

Webes API-k nem kell semmilyen felhasználó nem bejelentkezett interaktív módon való regisztrálása átirányítási URI-t.

## <a name="expose-an-api"></a>Tegye elérhetővé az API-k

Egy másik webes API-kat az adott érték a közzétett API-t és a közzétett hatókörök.

### <a name="resource-uri-and-scopes"></a>Erőforrás-URI és hatókörök

Hatókörök: általában az űrlap `resourceURI/scopeName`. A Microsoft Graph, a hatókörök például parancsikonok rendelkezik `User.Read`, de ez a karakterlánc csak a parancsikont `https://graph.microsoft.com/user.read`.

Alkalmazásregisztráció, során kell határozza meg a következő paraméterekkel:

- Az erőforrás-URI - az alkalmazásregisztrációs portálon alapértelmezés szerint azt javasolja, hogy használhatja `api://{clientId}`. Az erőforrás URI-t az egyedi, de nem emberi olvasható. Módosítsa azt, de győződjön meg arról, hogy annak egyediségét.
- Egy vagy több **hatókörök** (az ügyfélalkalmazások számára, akkor jelenik meg **delegált engedélyek** a webes API-hoz)
- Egy vagy több **alkalmazás-szerepkörök** (az ügyfélalkalmazások számára, akkor jelenik meg **Alkalmazásengedélyek** a webes API-hoz)

A hatókörök is jelennek meg a beleegyezést kérő oldalon, a végfelhasználók számára, akik használják az alkalmazást. Ezért meg kell adnia a megfelelő karakterláncok, amelyek ismertetik a hatókör:

- A végfelhasználó által látott módon
- A bérlői rendszergazda, aki rendszergazdai jóváhagyás adhat a

### <a name="how-to-expose-delegated-permissions-scopes"></a>Hogyan lehet elérhetővé tenni a delegált engedélyeket (hatóköröket)

1. Válassza ki a **közzé API-k** szakaszában az alkalmazás regisztrálása és:
   1. Válassza a **Hatókör hozzáadása** lehetőséget.
   1. Ha a rendszer kéri, fogadja el a javasolt Alkalmazásazonosító URI-ja (api :// {clientId}) kiválasztásával **menti és folytatja azt**.
   1. Adja meg a következő paraméterekkel:
      - A **hatókör neve**, használjon `access_as_user`.
      - A **ki hagyhatja jóvá**, győződjön meg arról, hogy a **rendszergazdák és felhasználók** beállítás.
      - A **rendszergazdai jóváhagyás megjelenítendő neve**, típus `Access TodoListService as a user`.
      - A **rendszergazdai jóváhagyás leírása**, típus `Accesses the TodoListService Web API as a user`.
      - A **felhasználói jóváhagyás megjelenítendő neve**, típus `Access TodoListService as a user`.
      - A **felhasználói jóváhagyás leírása**, típus `Accesses the TodoListService Web API as a user`.
      - Tartsa **állapot** beállítása **engedélyezve**.
      - Válassza ki **hatókör hozzáadása**.

### <a name="case-where-your-web-api-is-called-by-daemon-application"></a>Eset, ahol hívja meg a webes API démon alkalmazás

A bekezdés elsajátíthatja a védett webes API regisztrálása, hogy a démon alkalmazások biztonságosan hívható lesz:

- tegye elérhetővé kell **Alkalmazásengedélyek**. Alkalmazásengedélyek csak deklarálja, mivel démon alkalmazások nem használhatnak felhasználókat, és ezért delegált engedélyeket értelme.
- bérlői rendszergazdák csak olyan alkalmazások, amelyek már regisztrált, hogy szeretné-e hozzáférni a webes API-alkalmazások engedélyek egyikét a webalkalmazás az Azure AD-probléma jogkivonatok szükség lehet.

#### <a name="how-to-expose-application-permissions-app-roles"></a>Hogyan lehet elérhetővé tenni az Alkalmazásengedélyek (alkalmazás-szerepkörök)

Alkalmazásengedélyek elérhetővé, szüksége lesz a jegyzékfájl szerkesztése.

1. Kattintson az alkalmazás alkalmazásregisztráció, **Manifest**.
1. A jegyzékfájl szerkesztése megkeresésével a `appRoles` beállítást, és a egy vagy több alkalmazás-szerepkörök hozzáadása. A szerepkör-definíció az alábbi példa JSON-kódblokkot van megadva.  Hagyja a `allowedMemberTypes` "Alkalmazás" csak. Győződjön meg arról, hogy a **azonosító** egy egyedi GUID és **displayName** és **érték** nem tartalmazhat szóközt.
1. A jegyzékfájl mentése.

Tartalmának `appRoles` kell lennie a következő (a `id` lehet bármilyen egyedi GUID azonosítója)

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="how-to-ensure-that-azure-ad-issues-tokens-for-your-web-api-only-to-allowed-clients"></a>Hogyan ellenőrizhető, hogy az Azure AD jogkivonatokat a webes API-hoz csak engedélyezett ügyfelek

Az alkalmazás-szerepkör (Ez a fejlesztői módja nagyon) ellenőrzi a webes API-t. De jogkivonatok kiállítása a webes API-hoz csak az API eléréséhez a bérlői rendszergazda által jóváhagyott alkalmazások az Azure Active Directory is konfigurálhatja. A további biztonsági hozzáadása:

1. Az alkalmazás **áttekintése** az alkalmazás regisztrációs lapra, jelölje be az alkalmazás nevére a hivatkozás **felügyelt alkalmazás a helyi címtárban**. Ez a mező címét levágása. Megteheti például, olvassa el: `Managed application in ...`

   > [!NOTE]
   >
   > Ha bejelöli ezt a hivatkozást fog váltani a **vállalati alkalmazás áttekintése** a szolgáltatásnevet, a bérlőben, amelyben létrehozta az alkalmazáshoz társított oldal. A böngésző Vissza gomb használatával az alkalmazás regisztrációs oldalra navigálhat.

1. Válassza ki a **tulajdonságok** lapját a **kezelés** szakasz a vállalati alkalmazás lapok
1. Ha azt szeretné, hogy aad-ben való hozzáférés kikényszerítéséhez a webes API-nak csak egyes ügyfelek, **kell felhasználó-hozzárendelés?** való **Igen**.

   > [!IMPORTANT]
   >
   > Beállításával **kell felhasználó-hozzárendelés?** való **Igen**, AAD ellenőrizni fogja az ügyfelek az alkalmazás szerepkör-hozzárendeléseit, amikor azok a hozzáférési jogkivonatot a webes API-hoz. Ha az ügyfél nem volt rendelve bármely AppRoles, aad-ben csak a következő hibát adna vissza: `invalid_client: AADSTS501051: Application xxxx is not assigned to a role for the xxxx`
   >
   > Ha a **kell felhasználó-hozzárendelés?** való **nem**, <span style='background-color:yellow; display:inline'>Azure ad-ben nem ellenőrizze a szerepkör-hozzárendeléseit, amikor egy ügyfél hozzáférési jogkivonatot kér a webes API</span>. Ezért (az ügyfélhitelesítő adatok folyamata segítségével bármely ügyfél) démon ügyfelek továbbra is tudná hozzáférési jogkivonat beszerzése az API-hoz csak a célközönség megadásával. Bármely alkalmazás tudná az API eléréséhez a hozzá tartozó engedélyek kérése nélkül. Most, ez a lehetőség nem majd végén, a webes API mindig, a következő szakaszban leírtak szerint ellenőrizheti, hogy rendelkezik-e az alkalmazás a megfelelő szerepkör (amely a bérlői rendszergazda által engedélyezett volt), ellenőrzi, hogy rendelkezik-e a hozzáférési jogkivonat egy `roles` jogcímet, és a megfelelő érték t kérelmét (esetünkben megan `access_as_application`).

1. Válassza ki **mentése**

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazás kód konfigurációját](scenario-protected-web-api-app-configuration.md)
