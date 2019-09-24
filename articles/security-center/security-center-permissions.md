---
title: Engedélyek a Azure Security Centerban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Security Center szerepköralapú hozzáférés-vezérléssel a felhasználók engedélyeinek kiosztásához és az egyes szerepkörökhöz engedélyezett műveletek azonosításához.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: f52d518d2ed1dfb62eed72cf9c0b839a37b7f856
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201646"
---
# <a name="permissions-in-azure-security-center"></a>Engedélyek Azure Security Center

Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) használ, amelynek [beépített szerepköreit](../role-based-access-control/built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti.

A Security Center a biztonsági problémák és biztonsági rések azonosításához erőforrásán konfiguráció értékeli. A Security Centerben csak láthatja az előfizetés vagy az erőforráscsoport, amely egy erőforrás tartozik, a tulajdonos, közreműködő vagy olvasó a szerepkör hozzárendelése esetén egy erőforráshoz kapcsolódó információkat.

Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

* **Biztonsági olvasó**: Az ehhez a szerepkörhöz tartozó felhasználó megtekinti a jogosultságokat a Security Center. A felhasználók megtekinthetik a javaslatokat, a riasztásokat, a biztonsági házirendeket és a biztonsági állapotokat, de nem módosíthatják azokat.
* **Biztonsági rendszergazda**: Az ehhez a szerepkörhöz tartozó felhasználók ugyanazokkal a jogokkal rendelkeznek, mint a biztonsági olvasó, valamint frissíthetik a biztonsági házirendet, és elérhetik a riasztásokat és a javaslatokat.

> [!NOTE]
> A biztonsági szerepkörök, a biztonsági olvasó és a biztonsági rendszergazda csak Security Center rendelkezik hozzáféréssel. A biztonsági szerepkörök nem férnek hozzá az Azure más szolgáltatási területeihez, például a Storage, a Web & Mobile vagy a eszközök internetes hálózatahoz.
>
>

## <a name="roles-and-allowed-actions"></a>Szerepkörök és engedélyezett műveletek

A következő táblázat a szerepköröket és az engedélyezett műveleteket mutatja Security Centerban. Az X azt jelzi, hogy a művelet engedélyezett a szerepkörhöz.

| Role | Biztonsági házirend szerkesztése | Erőforrásokra vonatkozó biztonsági javaslatok alkalmazása | Riasztások és javaslatok bezárása | Riasztások és javaslatok megtekintése |
|:--- |:---:|:---:|:---:|:---:|
| Előfizetés tulajdonosa | X | X | X | X |
| Előfizetés közreműködői | -- | X | X | X |
| Erőforráscsoport tulajdonosa | -- | X | -- | X |
| Erőforráscsoport közreműködői | -- | X | -- | X |
| Olvasó | -- | -- | -- | X |
| Biztonsági rendszergazda | X | -- | X | X |
| Biztonsági olvasó | -- | -- | -- | X |

> [!NOTE]
> Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Például rendelje hozzá az olvasó szerepkört azokhoz a felhasználókhoz, akik csak az erőforrások biztonsági állapotával kapcsolatos információkat szeretnének megtekinteni, de nem végeznek műveleteket, például javaslatok alkalmazása vagy szerkesztési szabályzatok.
>
>

## <a name="next-steps"></a>További lépések
Ez a cikk azt ismerteti, hogyan használja a Security Center a RBAC-t a felhasználók engedélyeinek hozzárendeléséhez és az egyes szerepkörökhöz engedélyezett műveletek azonosításához. Most, hogy már ismeri az előfizetés biztonsági állapotának figyeléséhez szükséges szerepkör-hozzárendeléseket, a biztonsági szabályzatok szerkesztését és a javaslatok alkalmazását, Ismerje meg a következőket:

- [Biztonsági szabályzatok beállítása Security Center](tutorial-security-policy.md)
- [Biztonsági javaslatok kezelése Security Center](security-center-recommendations.md)
- [Azure-erőforrások biztonsági állapotának figyelése](security-center-monitoring.md)
- [A biztonsági riasztások kezelése és a riasztásokra való válaszadás a Security Centerben](security-center-managing-and-responding-alerts.md)
- [Partneri biztonsági megoldások figyelése](security-center-partner-solutions.md)
