---
title: Hozzáférési csomaghoz tartozó hozzárendelések megtekintése, hozzáadása és eltávolítása az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan tekintheti meg, adhatja hozzá és távolíthat el egy hozzáférési csomaghoz tartozó hozzárendeléseket Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 694c8866a69d8488511be1670ba3d3013a4a3423
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392513"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Hozzáférési csomaghoz tartozó hozzárendelések megtekintése, hozzáadása és eltávolítása az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure AD-jogosultságok kezelése szolgáltatásban megtekintheti, hogy kinek van hozzárendelve hozzáférési csomagokhoz, azok házirendjéhez és állapotához. Ha egy hozzáférési csomag megfelelő szabályzattal rendelkezik, közvetlenül is hozzárendelheti a felhasználót egy hozzáférési csomaghoz. Ez a cikk a hozzáférési csomagok hozzárendeléseinek megtekintését, hozzáadását és eltávolítását ismerteti.

## <a name="view-who-has-an-assignment"></a>Hozzárendelést használó nézet megtekintése

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **hozzárendelések** elemre az aktív hozzárendelések listájának megtekintéséhez.

    ![Egy hozzáférési csomag hozzárendeléseinek listája](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. A további részletek megtekintéséhez kattintson egy konkrét hozzárendelésre.

1. Ha meg szeretné tekinteni azoknak a hozzárendeléseknek a listáját, amelyeken nincs megfelelően kiépítve az összes erőforrás-szerepkör, kattintson a szűrő állapotára, és válassza a **kézbesítés**lehetőséget.

    A kézbesítési hibákkal kapcsolatos további részletekért tekintse meg a felhasználó megfelelő kérését a **kérelmek** lapon.

1. A lejárt hozzárendelések megjelenítéséhez kattintson a szűrő állapotára, és válassza a **lejárt**lehetőséget.

1. A szűrt lista CSV-fájljának letöltéséhez kattintson a **Letöltés**gombra.

## <a name="directly-assign-a-user"></a>Felhasználó közvetlen kiosztása

Bizonyos esetekben előfordulhat, hogy közvetlenül egy adott felhasználót szeretne hozzárendelni egy hozzáférési csomaghoz, hogy a felhasználóknak ne kelljen átesniük a hozzáférési csomag igénylésének folyamatán. A felhasználók közvetlen hozzárendeléséhez a hozzáférési csomagnak rendelkeznie kell egy olyan házirenddel, amely lehetővé teszi a közvetlen rendszergazdai hozzárendeléseket.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **hozzárendelések**elemre.

1. Kattintson az **új hozzárendelés** lehetőségre a felhasználó hozzáadása a csomaghoz lehetőség megnyitásához.

    ![Hozzárendelések – felhasználó hozzáadása a csomag eléréséhez](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Kattintson a **felhasználók hozzáadása** elemre azon felhasználók kiválasztásához, akiknek a hozzáférési csomagot hozzá szeretné rendelni.

1. A **házirend kiválasztása** listából válassza ki azt a szabályzatot, amelynek [nincs (csak a rendszergazda közvetlen hozzárendelései)](entitlement-management-access-package-request-policy.md#none-administrator-direct-assignments-only) beállítása.

    Ha ez a hozzáférési csomag nem rendelkezik ilyen típusú házirenddel, kattintson az **új házirend létrehozása** lehetőségre, ha hozzá szeretne adni egyet.

1. Adja meg azt a dátumot és időpontot, ameddig a kiválasztott felhasználók hozzárendelésének kezdési és befejezési időpontja. Ha nincs megadva befejezési dátum, a rendszer a szabályzat életciklus-beállításait fogja használni.

1. Igény szerint megadhatja a közvetlen hozzárendelését a rekordok megőrzése érdekében.

1. A **Hozzáadás** gombra kattintva közvetlenül hozzárendelheti a kiválasztott felhasználókat a hozzáférési csomaghoz.

    Néhány pillanat elteltével kattintson a **frissítés** gombra, hogy megjelenjenek a felhasználók a hozzárendelések listájában.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomag kérésének és beállításainak módosítása](entitlement-management-access-package-request-policy.md)
- [Jelentések és naplók megtekintése](entitlement-management-reports.md)
