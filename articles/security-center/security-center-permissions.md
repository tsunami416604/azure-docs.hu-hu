---
title: Engedélyek az Azure Security Centerben |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan az Azure Security Center használja szerepköralapú hozzáférés-vezérlési engedélyek hozzárendelése a felhasználókhoz, és azonosítja az engedélyezett műveletek az egyes szerepkörökhöz.
services: security-center
cloud: na
documentationcenter: na
author: rkarlin
manager: MBaldwin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 42220a94eb0d0259b8b410e7343ffbfe09e8bfa5
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007858"
---
# <a name="permissions-in-azure-security-center"></a>Engedélyek az Azure Security Centerben

Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) használ, amelynek [beépített szerepköreit](../role-based-access-control/built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti.

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
| Előfizetés Közreműködője | -- | X | X | X |
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
