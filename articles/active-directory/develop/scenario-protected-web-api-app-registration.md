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
ms.openlocfilehash: a1d8c8fcf84cd008957fcdb7cd14c4a07d9f3643
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074905"
---
# <a name="protected-web-api---app-registration"></a>Védett webes API - alkalmazás regisztrációja

Ez a cikk azt ismerteti, hogy egy védett webes API-alkalmazás regisztrációs adatait.

Lásd: [a rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platform az](quickstart-register-app.md) hogyan kell regisztrálni az alkalmazás általános lépéseit.

## <a name="accepted-token-version"></a>Token elfogadott verzió

A Microsoft identity platform végpont kiadhatnak kétféle jogkivonatokat: 1.0-s verziójú jogkivonatok és 2.0-s verziójú jogkivonatok. Ezek a jogkivonatok többet tudhat [hozzáférési jogkivonatokat](access-tokens.md). Az elfogadott token verzió attól függ, a **támogatott fióktípusok** választotta, hogy az alkalmazás létrehozásakor:

- Ha értékét **támogatott fióktípusok** van **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok (például a Skype, Xbox, Outlook.com)**, az elfogadott token verzió v2.0 lesz.
- Ellenkező esetben az elfogadott jogkivonat-verzió lesz a 2.0-s verzió.

Az alkalmazás létrehozása után módosíthatja az elfogadott token verzió az alábbi lépéseket:

1. Az Azure Portalon válassza ki az alkalmazást, és válassza ki a **Manifest** az alkalmazáshoz.
2. A jegyzékfájlban, keressen **"accessTokenAcceptedVersion"**, és láthatja, hogy a kulcs értéke **2**. Ez a tulajdonság lehetővé teszi, hogy az Azure ad-ben tudja, hogy a webes API 2.0-s verziójú jogkivonatokat fogad. Ha **null**, az elfogadott jogkivonat-verzió lesz 1.0-s verziója.
3. Kattintson a **Mentés** gombra.

> [!NOTE]
> Szolgáltatás a webes API-nak eldönteni, melyik token verziója (1.0 vagy 2.0-s verzió) fogad el. Ügyfelek kérnek egy jogkivonatot a webes API-t a Microsoft identity platform v2.0-végpont használatával, ha azok egy jogkivonatot, amely azt jelzi, hogy melyik verzió van fogadja el a webes API-t fog kapni.

## <a name="no-redirect-uri"></a>Nincs átirányítási URI-ja

Webes API-k nem kell semmilyen felhasználó nem bejelentkezett interaktív módon való regisztrálása átirányítási URI-t.

## <a name="expose-an-api"></a>API közzététele

Egy másik webes API-kat az adott érték a közzétett API-t és a közzétett hatókörök.

### <a name="resource-uri-and-scopes"></a>Erőforrás-URI és hatókörök

Hatókörök: általában az űrlap `resourceURI/scopeName`. A Microsoft Graph, a hatókörök például parancsikonok rendelkezik `User.Read`, de ez a karakterlánc csak a parancsikont `https://graph.microsoft.com/user.read`.

Alkalmazásregisztráció, során kell határozza meg a következő paraméterekkel:

- Egy erőforrás-URI - az alkalmazásregisztrációs portálon alapértelmezés szerint azt javasolja, hogy használhatja `api://{clientId}`. Az erőforrás URI-t az egyedi, de nem emberi olvasható. Módosítsa azt, de győződjön meg arról, hogy annak egyediségét.
- Egy vagy több hatókör

A hatókörök is jelennek meg a beleegyezést kérő oldalon, amelyek bemutatják a végfelhasználók számára, akik használják az alkalmazást. Ezért meg kell adnia a megfelelő karakterláncok, amelyek ismertetik a hatókör:

- A végfelhasználó által látott módon
- A bérlői rendszergazda, aki rendszergazdai jóváhagyás adhat a

### <a name="how-to-expose-the-api"></a>Hogyan teszi közzé az API-hoz

1. Válassza ki a **közzé API-k** szakaszában az alkalmazás regisztrálása és:
   1. Válassza a **Hatókör hozzáadása** lehetőséget.
   1. Fogadja el a javasolt Alkalmazásazonosító URI-ja (api :// {clientId}) kiválasztásával **menti és folytatja azt**.
   1. Adja meg a következő paraméterekkel:
      - A **hatókör neve**, használjon `access_as_user`.
      - A **ki hagyhatja jóvá**, győződjön meg arról, hogy a **rendszergazdák és felhasználók** beállítás.
      - A **rendszergazdai jóváhagyás megjelenítendő neve**, típus `Access TodoListService as a user`.
      - A **rendszergazdai jóváhagyás leírása**, típus `Accesses the TodoListService Web API as a user`.
      - A **felhasználói jóváhagyás megjelenítendő neve**, típus `Access TodoListService as a user`.
      - A **felhasználói jóváhagyás leírása**, típus `Accesses the TodoListService Web API as a user`.
      - Tartsa **állapot** beállítása **engedélyezve**.
      - Válassza ki **hatókör hozzáadása**.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazás kód konfigurációját](scenario-protected-web-api-app-configuration.md)
