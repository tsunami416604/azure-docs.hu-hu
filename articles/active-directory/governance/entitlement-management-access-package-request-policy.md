---
title: Hozzáférési csomag kérés- és jóváhagyási beállításainak módosítása az Azure AD jogosultságkezelésében – Azure Active Directory
description: Megtudhatja, hogyan módosíthatja egy hozzáférési csomag kérési és jóváhagyási beállításait az Azure Active Directory jogosultságkezelésében.
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
ms.date: 03/30/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0eae4f53283c33f3d7372a606f8c0a3ac27d079
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655931"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag kérés- és jóváhagyási beállításainak módosítása az Azure AD-jogosultságkezelésben

Hozzáférési csomagkezelőként bármikor módosíthatja azon felhasználókat, akik hozzáférési csomagot kérhetnek a házirend szerkesztésével vagy új házirend hozzáadásával. A jóváhagyási beállításokat is módosíthatja.

Ez a cikk azt ismerteti, hogyan módosíthatja egy meglévő hozzáférési csomag kérési és jóváhagyási beállításait.

## <a name="choose-between-one-or-multiple-polices"></a>Válasszon egy vagy több rendőr közül

A hozzáférési csomag igénylésének módja egy házirend. Hozzáférési csomag létrehozásakor megadhatja azt a kérelem- és jóváhagyási beállítást, amely házirendet hoz létre. A legtöbb hozzáférési csomag egyetlen szabályzattal rendelkezik, de egyetlen hozzáférési csomag több szabályzattal is rendelkezhet. Ha azt szeretné, hogy a felhasználók különböző csoportjai különböző kérési és jóváhagyási beállításokkal kapjanak hozzárendeléseket, akkor több házirendet is létrehozhat egy hozzáférési csomaghoz. Például egyetlen házirend nem használható belső és külső felhasználók hozzárendelésére ugyanahhoz a hozzáférési csomaghoz. Azonban létrehozhat két szabályzatot ugyanabban a hozzáférési csomagban – egyet belső, egyet pedig külső felhasználók számára. Ha több szabályzat vonatkozik egy felhasználóra, a rendszer a kérés időpontjában kéri, hogy válassza ki azt a házirendet, amelyhez hozzá szeretné rendelni őket. Az alábbi ábrán egy két házirendet rendelkező hozzáférési csomag látható.

![Több házirend egy hozzáférési csomagban](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Forgatókönyv | Házirendek száma |
| --- | --- |
| Azt szeretném, hogy a címtárban lévő összes felhasználó ugyanazt a kérési és jóváhagyási beállításokat kívánja megegyeznie egy hozzáférési csomaghoz | Eggyel |
| Azt szeretném, hogy bizonyos csatlakoztatott szervezetek összes felhasználója hozzáférést kérjen | Eggyel |
| Azt akarom, hogy a felhasználók a könyvtárban, valamint a felhasználók kívül a könyvtárban, hogy kérjen egy hozzáférési csomag | Többszörös |
| Egyes felhasználók számára különböző jóváhagyási beállításokat szeretnék megadni | Többszörös |
| Azt akarom, hogy egyes felhasználók hozzáférjenek a csomag-hozzárendelésekhez, míg más felhasználók kiterjeszthetik hozzáférésüket | Többszörös |

A több házirend alkalmazásakor használt prioritási logikáról a [Több házirend](entitlement-management-troubleshoot.md#multiple-policies
)című témakörben talál további információt.

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Meglévő kérelem- és jóváhagyási házirend megnyitása

A hozzáférési csomag kérési és jóváhagyási beállításainak módosításához meg kell nyitnia a megfelelő házirendet. Az alábbi lépésekkel nyissa meg a hozzáférési csomag kérési és jóváhagyási beállításait.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **Házirendek,** majd a szerkesztni kívánt házirend elemre.

    A Házirend részletei ablaktábla a lap alján nyílik meg.

    ![Access csomag – Házirend részletei ablaktábla](./media/entitlement-management-shared/policy-details.png)

1. A házirend szerkesztéséhez kattintson a **Szerkesztés** gombra.

    ![Access csomag – Házirend szerkesztése](./media/entitlement-management-shared/policy-edit.png)

1. A kérelmek és **jóváhagyásbeállításainak** megnyitásához kattintson a Kérések fülre.

1. Hajtsa végre a következő kérelemszakaszok egyikében végrehajtott lépéseket.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Új kérelem- és jóváhagyási házirend hozzáadása

Ha olyan felhasználókat hoz létre, amelyeknek eltérő kérési és jóváhagyási beállításokkal kell rendelkezniük, akkor valószínűleg új szabályzatot kell létrehoznia. Az alábbi lépésekkel új házirendet vehet fel egy meglévő hozzáférési csomagba.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson **a Házirendek,** majd **a Házirend hozzáadása gombra.**

1. Írja be a házirend nevét és leírását.

    ![Házirend létrehozása névvel és leírással](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. A **Tovább** gombra kattintva nyissa meg a **Kérések** lapot.

1. Hajtsa végre a következő kérelemszakaszok egyikében végrehajtott lépéseket.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Ha házirendet szerkeszt, kattintson a **Frissítés gombra.** Ha új házirendet ad hozzá, kattintson a **Létrehozás gombra.**

## <a name="next-steps"></a>További lépések

- [Hozzáférési csomag életciklus-beállításainak módosítása](entitlement-management-access-package-lifecycle-policy.md)
- [Hozzáférési csomagra vonatkozó kérelmek megtekintése](entitlement-management-access-package-requests.md)