---
title: Engedélyek az Azure Security Centerben |} Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan az Azure Security Center segítségével szerepköralapú hozzáférés-vezérlési engedélyek hozzárendelése a felhasználókhoz, és az egyes szerepkörökhöz engedélyezett műveleteket azonosítja.
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: terrylan
ms.openlocfilehash: f85f49bd54eacbca67143b35eaf555cfb744a41d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="permissions-in-azure-security-center"></a>Engedélyek az Azure Security Centerben

Az Azure Security Center által használt [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md), amely biztosítja [beépített szerepkörök](../role-based-access-control/built-in-roles.md) , hogy a felhasználók, csoportok és az Azure rendelhető.

A Security Center értékeli a konfigurációs az erőforrások biztonsági problémák és biztonsági rések azonosítása. A biztonsági központban csak akkor jelenik meg egy erőforrás tulajdonos, közreműködő vagy olvasó szerepkört az előfizetés vagy az erőforráscsoportot, amelyhez tartozik egy erőforrás hozzárendelése esetén kapcsolatos adatokat.

Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

* **Biztonsági olvasó**: ehhez a szerepkörhöz tartozó felhasználó van jogosultsága ahhoz, hogy a Security Center megtekintése. A felhasználó megtekintheti a javaslatok, a riasztások, a biztonsági házirend és a biztonsági állapotok, de nem módosíthatja.
* **Biztonsági rendszergazda**: ehhez a szerepkörhöz tartozó felhasználó a biztonsági olvasó megegyező jogokkal rendelkezik és is frissíteni a biztonsági házirendet, és hagyja figyelmen kívül a riasztások és javaslatok.

> [!NOTE]
> A biztonsági szerepkörök, biztonsági olvasó és a biztonsági rendszergazda, csak a Security Center rendelkezik hozzáféréssel. A biztonsági szerepkörök az Azure tárolási, webes & mobilalkalmazás vagy az eszközök internetes hálózatát más szolgáltatás területéhez nincs hozzáférése.
>
>

## <a name="roles-and-allowed-actions"></a>Szerepkörök és az engedélyezett műveletek

A következő táblázat megjeleníti a szerepkört, és a Security Center műveletek engedélyezett. Az X jelzi, hogy a művelet engedélyezett-e az adott szerepkörhöz.

| Szerepkör | Biztonsági házirend szerkesztése | Egy erőforrás biztonsági javaslatok alkalmazása | Hagyja figyelmen kívül a riasztások és javaslatok | Riasztások megtekintése és javaslatok |
|:--- |:---:|:---:|:---:|:---:|
| Előfizetés tulajdonosa | X | X | X | X |
| Előfizetés közreműködő | X | X | X | X |
| Erőforráscsoport tulajdonosa | -- | X | -- | X |
| Erőforráscsoport közreműködő | -- | X | -- | X |
| Olvasó | -- | -- | -- | X |
| Biztonsági rendszergazda | X | -- | X | X |
| Biztonsági olvasó | -- | -- | -- | X |

> [!NOTE]
> Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Például az olvasó szerepkört rendelni felhasználók, akik csak egy erőforrás biztonsági állapotával kapcsolatos információk megtekintése, de nem tesznek lépéseket, például a alkalmazhatnak javaslatokat, és módosíthatják a szabályzatokat.
>
>

## <a name="next-steps"></a>További lépések
Ez a cikk alapján hogyan Security Center segítségével RBAC engedélyek hozzárendelése a felhasználókhoz, és azonosítja az egyes szerepkörökhöz engedélyezett műveletek. Most, hogy ismeri az előfizetés biztonsági állapotának figyeléséhez szükséges szerepkör-hozzárendelések, szerkesztheti a biztonsági szabályzatokat, és a javaslatok alkalmazni, megtudhatja, hogyan:

- [A Security Center biztonsági házirendek beállítása](security-center-policies.md)
- [A Security Center biztonsági javaslatok kezelése](security-center-recommendations.md)
- [Az Azure-erőforrások biztonsági állapotának figyelésére](security-center-monitoring.md)
- [A biztonsági riasztások kezelése és a riasztásokra való válaszadás a Security Centerben](security-center-managing-and-responding-alerts.md)
- [Biztonsági partnermegoldások figyelése](security-center-partner-solutions.md)
