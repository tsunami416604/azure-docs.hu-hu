---
title: Engedélyek az Azure Security Centerben | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogy az Azure Security Center hogyan használja a szerepköralapú hozzáférés-vezérlést a felhasználók engedélyek hozzárendeléséhez, és azonosítja az egyes szerepkörökhöz engedélyezett műveleteket.
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
ms.openlocfilehash: 0e60e782fa65cd5868bebe081673f9a158e07799
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921317"
---
# <a name="permissions-in-azure-security-center"></a>Engedélyek az Azure Security Centerben

Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) használ, amelynek [beépített szerepköreit](../role-based-access-control/built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti.

A Security Center felméri az erőforrások konfigurációját a biztonsági problémák és biztonsági rések azonosítására. A Biztonsági központban csak akkor jelenik meg egy erőforrással kapcsolatos információ, ha tulajdonos, közreműködő vagy olvasó szerepkört kap ahhoz az előfizetéshez vagy erőforráscsoporthoz, amelyhez egy erőforrás tartozik.

Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

* **Biztonsági olvasó:** A szerepkörhöz tartozó felhasználók rendelkeznek a Biztonsági központ megtekintési jogosultságával. A felhasználó megtekintheti a javaslatokat, riasztásokat, biztonsági házirendeket és biztonsági állapotokat, de nem módosíthatja azokat.
* **Biztonsági rendszergazda:** A szerepkörhöz tartozó felhasználók a Biztonsági olvasóval azonos jogokkal rendelkeznek, és frissíthetik a biztonsági házirendet, és elvethetik a riasztásokat és javaslatokat.

> [!NOTE]
> A biztonsági szerepkörök , a Biztonsági olvasó és a Biztonsági rendszergazda csak a Security Centerben rendelkeznek hozzáféréssel. A biztonsági szerepkörök nem férnek hozzá az Azure más szolgáltatási területeihez, például a Storage, a Web & Mobile vagy a Things internete.
>
>

## <a name="roles-and-allowed-actions"></a>Szerepkörök és engedélyezett műveletek

Az alábbi táblázat a Biztonsági központban található szerepköröket és engedélyezett műveleteket jeleníti meg.

| Szerepkör | Biztonsági házirend szerkesztése | Biztonsági javaslatok alkalmazása egy erőforrásra</br> (beleértve a "Quick Fix!") | Figyelmeztetések és ajánlások elvetése | Riasztások és javaslatok megtekintése |
|:--- |:---:|:---:|:---:|:---:|
| Előfizetés tulajdonosa | ✔ | ✔ | ✔ | ✔ |
| Előfizetés közreműködője | -- | ✔ | ✔ | ✔ |
| Erőforráscsoport tulajdonosa | -- | ✔ | -- | ✔ |
| Erőforráscsoport közreműködője | -- | ✔ | -- | ✔ |
| Olvasó | -- | -- | -- | ✔ |
| Biztonsági rendszergazda | ✔ | -- | ✔ | ✔ |
| Biztonsági olvasó | -- | -- | -- | ✔ |

> [!NOTE]
> Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Rendelje hozzá például az Olvasó szerepkört azoknak a felhasználóknak, akiknek csak az erőforrás biztonsági állapotára vonatkozó információkat kell megtekinteniük, de nem kell végrehajtaniuk a műveletet, például javaslatokat vagy szerkesztési házirendeket.
>
>

## <a name="next-steps"></a>További lépések
Ez a cikk ismerteti, hogy a Security Center hogyan használja az RBAC-ot a felhasználók engedélyek hozzárendelésére, és azonosította az egyes szerepkörek engedélyezett műveleteket. Most, hogy már ismeri az előfizetés biztonsági állapotának figyeléséhez, a biztonsági házirendek szerkesztéséhez és a javaslatok alkalmazásához szükséges szerepkör-hozzárendeléseket, megtudhatja, hogyan tehet idát:

- [Biztonsági szabályzatok beállítása a Security Centerben](tutorial-security-policy.md)
- [Biztonsági javaslatok kezelése a Biztonsági központban](security-center-recommendations.md)
- [Az Azure-erőforrások biztonsági állapotának figyelése](security-center-monitoring.md)
- [Biztonsági riasztások kezelése és megválaszolása a Biztonsági központban](security-center-managing-and-responding-alerts.md)
- [Partnerbiztonsági megoldások figyelése](security-center-partner-solutions.md)
