---
title: A PIM az Azure-erőforrásszerepkörök rendelkező megtekintése |} A Microsoft Docs
description: Nézet, aki rendelkezik az Azure erőforrás-szerepkörökkel az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: aee172bc6fc77aaac8d2d52037a481fdb976d308
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188968"
---
# <a name="view-who-has-azure-resource-roles-in-pim"></a>A PIM az Azure-erőforrásszerepkörök rendelkező megtekintése

Az Azure Active Directory Privileged Identity Management (PIM), Ön is felügyelheti, ellenőrizheti és monitorozása az Azure-erőforrásokhoz való hozzáférést a szervezeten belül. Ez magában foglalja az előfizetések, erőforráscsoportok és még akkor is, virtuális gépeket. Az Azure Portalon, amely kihasználja az Azure szerepköralapú hozzáférés-vezérlés (RBAC) bármely erőforrás kihasználhatja a biztonsági és életciklus felügyeleti képességek az Azure AD PIM-ben. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>Az Azure-erőforrásokhoz a PIM erőforrás rendszergazdáit segíti a

- Tekintse meg, milyen felhasználókkal és csoportokkal hozzárendelt szerepkörök felügyelt Azure-erőforrások
- Igény szerinti, például az előfizetések, erőforráscsoportok és egyéb erőforrások kezelése "csak az időben" hozzáférés engedélyezése
- Hozzárendelt felhasználók/csoportok erőforrás-hozzáférés automatikusan az új időhöz kötött hozzárendelési beállítások elévülése
- Gyors feladatok vagy a készenléti ütemezések ideiglenes erőforrás-hozzáférés hozzárendelése
- Az erőforrások eléréséhez a beépített vagy egyéni szerepkörhöz a többtényezős hitelesítés kényszerítése 
- Aktív felhasználói munkamenet során resource access korrelált erőforrás tevékenységre vonatkozó jelentések lekérése
- Értesítéseket kaphat az új felhasználók vagy csoportok hozzárendelése esetén erőforrásokhoz való hozzáférést, és ha azok a jogosult hozzárendelések aktiválása

## <a name="view-activation-and-azure-resource-activity"></a>Aktiválás és az Azure-erőforrás tevékenység megtekintése

Abban az esetben, ha meg, milyen műveletet egy adott felhasználó hajtott végre a különböző erőforrások van szüksége, áttekintheti az Azure Resource tevékenység társított egy adott aktiválási időszak (a jogosult felhasználók). Első lépésként válassza ki a felhasználó, a tagok megtekintése vagy egy adott szerepkör tagjainak listája. Az eredmény az Azure-erőforrások dátum szerint a felhasználói műveleteket, és ugyanazt az időszakra, hogy a legutóbbi szerepkör-aktiválások grafikus nézetet jelenít meg.

![](media/azure-pim-resource-rbac/user-details.png)

Egy adott szerepkör-aktiválás kiválasztása jelennek meg a szerepkör aktiválásának részletei, és a megfelelő Azure-erőforrás tevékenység, amely történt, miközben a felhasználó volt aktív.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="review-who-has-access-in-a-subscription"></a>Tekintse át, aki hozzáféréssel rendelkezik az előfizetéshez

Tekintse át a szerepkör-hozzárendeléseket az előfizetésében, a tagok lapon válassza a bal oldali navigációs vagy szerepkörök kiválasztása, és válasszon egy adott szerepkör tagjai áttekintéséhez. 

Válassza ki a tekintse át a meglévő hozzáférési felülvizsgálatok megtekintéséhez, és válassza a Hozzáadás létrehoz egy új, a művelet sávon.

![](media/azure-pim-resource-rbac/owner.png)

[További információ a hozzáférési felülvizsgálatok](pim-how-to-perform-security-review.md)

>[!NOTE]
Értékelések jelenleg csak támogatottak előfizetés erőforrástípusok.

## <a name="next-steps"></a>További lépések

- [Rendelje hozzá a PIM az Azure-erőforrásszerepkörök](pim-resource-roles-assign-roles.md)
- [Hagyja jóvá vagy utasítsa a PIM az Azure-erőforrások szerepköreihez tartozó kérelmek](pim-resource-roles-approval-workflow.md)
- [Beépített szerepkörök az Azure-ban](../../role-based-access-control/built-in-roles.md)
