---
title: Engedélyek az Azure Security Centerben |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan az Azure Security Center használja szerepköralapú hozzáférés-vezérlési engedélyek hozzárendelése a felhasználókhoz, és azonosítja az engedélyezett műveletek az egyes szerepkörökhöz.
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
ms.topic: conceptual
ms.date: 06/13/2017
ms.author: terrylan
ms.openlocfilehash: b93b57d50ccf5d5dfb092bdb71820da77f345878
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295462"
---
# <a name="permissions-in-azure-security-center"></a>Engedélyek az Azure Security Centerben

Használja az Azure Security Center [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md), amely biztosítja [beépített szerepkörök](../role-based-access-control/built-in-roles.md) , hogy a felhasználók, csoportok és Azure-szolgáltatások rendelhetők.

A Security Center a biztonsági problémák és biztonsági rések azonosításához erőforrásán konfiguráció értékeli. A Security Centerben csak láthatja az előfizetés vagy az erőforráscsoport, amely egy erőforrás tartozik, a tulajdonos, közreműködő vagy olvasó a szerepkör hozzárendelése esetén egy erőforráshoz kapcsolódó információkat.

Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

* **Biztonsági olvasó**: ehhez a szerepkörhöz tartozó felhasználó rendelkezik megtekintése a Security Center jogosultságait. A felhasználó megtekintheti a javaslatok, riasztások, a biztonsági szabályzatot és biztonsági állapotok, de nem végezhet módosításokat.
* **Biztonsági rendszergazda**: ehhez a szerepkörhöz tartozó felhasználó ugyanazokat a jogosultságokat, a biztonsági olvasó rendelkezik, és is frissítheti a biztonsági házirend és riasztások és javaslatok elvetése.

> [!NOTE]
> A biztonsági szerepkörök, biztonsági olvasó és a biztonsági rendszergazda, csak a Security Center rendelkezik hozzáféréssel. A biztonsági szerepkörök nem rendelkeznek hozzáféréssel, például a Storage, Web & Mobile vagy az eszközök internetes hálózatát az Azure egyéb szolgáltatási területeihez.
>
>

## <a name="roles-and-allowed-actions"></a>Szerepkörök és engedélyezett műveletek

Az alábbi táblázat megjeleníti a szerepköröket, és a műveletek a Security Centerben engedélyezett. Egy X azt jelzi, hogy a művelet szerepkör számára engedélyezett.

| Szerepkör | Biztonsági szabályzat szerkesztése | Erőforrás biztonsági javaslatok alkalmazása | Riasztások és javaslatok elvetése | Riasztások megtekintése és javaslatok |
|:--- |:---:|:---:|:---:|:---:|
| Előfizetés tulajdonosa | X | X | X | X |
| Előfizetési közreműködő | X | X | X | X |
| Erőforráscsoport tulajdonosa | -- | X | -- | X |
| Erőforráscsoport közreműködő | -- | X | -- | X |
| Olvasó | -- | -- | -- | X |
| Biztonsági rendszergazda | X | -- | X | X |
| Biztonsági olvasó | -- | -- | -- | X |

> [!NOTE]
> Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Az Olvasó szerepkör például rendelje hozzá a felhasználók számára, akik csak egy adott erőforrás biztonsági állapotával kapcsolatos információk megtekintéséhez, de nem hajtsa végre a műveletet, például alkalmazhatnak javaslatokat, vagy módosíthatják a szabályzatokat.
>
>

## <a name="next-steps"></a>További lépések
Ez a cikk részletesen a Security Center általi RBAC engedélyek hozzárendelése a felhasználókhoz, és az engedélyezett műveletek az egyes szerepkörökhöz meghatározott. Most, hogy már ismeri az előfizetés biztonsági állapotának figyeléséhez szükséges a szerepkör-hozzárendeléseket, szerkesztheti a biztonsági szabályzatokat, és alkalmazhatja a javaslatokat, megtudhatja, hogyan lehet:

- [Biztonsági szabályzatok beállítása a Security Centerben](security-center-policies.md)
- [A Security Centerben a biztonsági javaslatok kezelése](security-center-recommendations.md)
- [Az Azure-erőforrások biztonsági állapotának figyelése](security-center-monitoring.md)
- [A biztonsági riasztások kezelése és a riasztásokra való válaszadás a Security Centerben](security-center-managing-and-responding-alerts.md)
- [Biztonsági partnermegoldások monitorozása](security-center-partner-solutions.md)
