---
title: Hozzáférés-szabályozás leirányításának delegálása a katalóguskészítők számára az Azure AD jogosultságkezelésben – Azure Active Directory
description: Megtudhatja, hogy miként delegálhatja a hozzáférés-irányítást az informatikai rendszergazdáktól a katalóguskészítők és a projektmenedzserek számára, hogy saját maguk is kezelhessék a hozzáférést.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120198"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Hozzáférés-szabályozás leirányításának delegálása a katalóguskészítők számára az Azure AD-jogosultságkezelésben

A katalógus erőforrások és hozzáférési csomagok tárolója. Katalógust akkor hoz létre, ha a kapcsolódó erőforrásokat és hozzáférési csomagokat szeretné csoportosítani. Alapértelmezés szerint a globális rendszergazda vagy a felhasználó rendszergazda [létrehozhat katalógust,](entitlement-management-catalog-create.md)és további felhasználókat vehet fel katalógustulajdonosként.

Delegálni a felhasználók, akik nem rendszergazdák, hogy azok saját katalógusok létrehozása, hozzáadhatja ezeket a felhasználókat az Azure AD jogosultságkezelés által definiált katalógus készítői szerepkör. Hozzáadhat egyéni felhasználókat, vagy hozzáadhat egy csoportot, amelynek tagjai ezután katalógusokat hozhatnak létre.  A katalógus létrehozása után később hozzáadhatják a saját erőforrásaikat a katalógusukhoz.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Rendszergazdaként delegáljon katalóguskészítővel

Az alábbi lépésekkel rendeljen hozzá egy felhasználót a katalógus létrehozója szerepkörhöz.

**Előfeltételi szerepkör:** Globális rendszergazda vagy felhasználó

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menü **Jogosultságkezelés** szakaszában kattintson a **Beállítások gombra.**

1. Kattintson a **Szerkesztés** gombra.

    ![Katalóguskészítők hozzáadásának beállításai](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. A **Delegált jogosultság kezelésszakaszában** kattintson a **Katalóguskészítők hozzáadása** elemre, és jelölje ki azokat a felhasználókat vagy csoportokat, akiknek delegálni szeretné ezt a jogosultságkezelési szerepkört.

1. Kattintson a **Kiválasztás** gombra.

1. Kattintson a **Mentés** gombra.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Delegált szerepkörök hozzáférésének engedélyezése az Azure Portalhoz

Ha engedélyezni szeretné a delegált szerepkörök, például a katalóguskészítők és a hozzáférés-csomagkezelők számára a hozzáférési csomagok kezeléséhez való hozzáférést, ellenőrizze a felügyeleti portál beállítását.

**Előfeltételi szerepkör:** Globális rendszergazda vagy felhasználó

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd a **Felhasználók**elemre.

1. A bal oldali menüben kattintson a **Felhasználói beállítások parancsra.**

1. Győződjön meg **arról, hogy az Azure AD felügyeleti portálhoz való hozzáférés korlátozása** **nem**.

    ![Az Azure AD felhasználói beállításai – Felügyeleti portál](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>További lépések

- [Erőforrások katalógusának létrehozása és kezelése](entitlement-management-catalog-create.md)
- [Hozzáférés-szabályozás delegálása a csomagkezelőkszámára](entitlement-management-delegate-managers.md)

