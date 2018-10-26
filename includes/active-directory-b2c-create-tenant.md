---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 623f95eac029e808746d5d08fa088eba134592dd
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134210"
---
Kattintson a **erőforrás létrehozása** gombra. A **Keresés a piactéren** mezőbe írja be a következőt: `Azure Active Directory B2C`.

![A kiemelt Hozzáadás gomb és az Azure Active Directory B2C szöveg a Keresés a piactéren mezőben](./media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

A találatok listájában válassza ki az **Azure Active Directory B2C** elemet.

![A kiválasztott Azure Active Directory B2C elem a találatok listájában](./media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Ekkor megjelennek az Azure Active Directory B2C-vel kapcsolatos részletek. Az új Azure Active Directory B2C-bérlő konfigurálásának megkezdéséhez kattintson a **Létrehozás** gombra.

Válassza az **Új Azure AD B2C-bérlő létrehozása** lehetőséget. Az alábbi táblázatban megadott beállításokat a vállalat neve Contoso használja példaként. Adja meg a saját szervezet neve és a egy egyedi bérlő neve a bérlő létrehozásakor kell.  

![Azure AD B2C-bérlő, mintaszöveggel a rendelkezésre álló mezőkben](./media/active-directory-b2c-create-tenant/create-new-b2c-tenant.png)

| Beállítás      | Mintaérték  | Leírás                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Szervezet neve** | Contoso | A szervezet neve. | 
| **Kezdeti tartománynév** |  ContosoB2CTenant | A B2C-bérlő tartományneve. Alapértelmezés szerint a kezdeti tartománynév tartalmazza. onmicrosoft.com. Egy tesztelési bérlőn létrehozásakor, válassza ki a nem éles nevet, például ContosoB2CTesting. |
| **Ország vagy régió** | Egyesült Államok | Válassza ki a címtár országát vagy régióját. A címtár ezen a helyen fog létrejönni, és ez később nem változtatható meg.  |

A bérlő létrehozásához kattintson a **Létrehozás** gombra. A bérlő létrehozása eltarthat néhány percig. Amikor a folyamat befejeződött, a rendszer figyelmeztetni fogja az értesítések között.