---
title: Privileged Identity Management az Azure-erőforrások - MFA |} Microsoft Docs
description: Ez a dokumentum ismerteti a PIM erőforrások többtényezős hitelesítés engedélyezése.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - szerepkör erőforrás - MFA

Az Azure erőforrás-szerepkörök PIM lehetővé teszi, hogy erőforrás és identitás rendszergazdák időhöz kötött tagságát, és közvetlenül az időponthoz kötött elérése kritikus Azure-infrastruktúra védelméhez. Ezenkívül PIM választható kikényszerítését biztosítja az Azure multi-factor Authentication (MFA) a két különböző forgatókönyveket.

## <a name="require-mfa-to-activate"></a>Megkövetelő aktiválása

Erőforrás-Rendszergazdák szerepkör tagjai jogosultak az Azure MFA sikeres legyen, mielőtt aktiválna lehet szükség. Ez a folyamat biztosítja, hogy a kérelmező felhasználó aktiválása az, akinek azok ésszerű Biztosan fel azokat. Helyzetekben fontos erőforrásokra ezt a beállítást kényszerítése védi, ha a felhasználói fiók sérült. 

Ez a követelmény kényszeríti, válasszon ki egy erőforrástípust a felügyelt erőforrások közül. Az a [áttekintő irányítópulthoz](pim-resource-roles-overview-dashboards.md), szerepkör kiválasztása a szerepkörök, a képernyő jobb alsó listájáról.

Emellett kaphat a szerepkör beállításai vagy a "szerepkörök" vagy "Szerepkör-beállítások" lapon a bal oldali navigációs menü.

>[!Note]
>Ha a bal oldali navigációs menü beállítások szürkén jelennek meg, és megjelenik egy szalagcím, mely szerint a "Rendelkezik aktiválható jogosult szerepkörtől" a lap tetején nincsenek aktív rendszergazdaként, és kell [aktiválása](pim-resource-roles-activate-your-roles.md) folytatása előtt.

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Ha a szerepköri tagság a megtekintése, válassza ki a "Beállítások" a "szerepkör beállítás részletei" lehetőségre a képernyő tetején a sáv az.

Kattintson a **szerkesztése** gombra a beállítások módosításához.

A szakaszában **aktiválás**, a jelölőnégyzet bejelölésével **többtényezős hitelesítés megkövetelése aktiválásához**, majd kattintson a Mentés gombra.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>Többtényezős hitelesítés megkövetelése hozzárendelés

Bizonyos esetekben egy erőforrás-kezelője érdemes tag hozzárendelése egy szerepkörhöz rövid időtartamra (például egy nap), és nem kell az aktiválás kéréséhez rendelt tag. Ebben a forgatókönyvben PIM nem érvényesíthetők többtényezős Hitelesítést, amikor a tag a szerepkör-hozzárendelés használja, mert azok már aktív abban a pillanatban hozzárendeli a szerepkörben.

Annak érdekében, hogy az erőforrás-kezelője teljesítését a hozzárendelés az, akinek azokat fel vannak, a hozzárendelés kényszerítheti a többtényezős Hitelesítést.

Az azonos szerepkör beállítás részletei képernyőn jelölje be a jelölőnégyzetet "Hozzárendelés a többtényezős hitelesítés megkövetelése".

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>További lépések

[Jóváhagyás aktiválásához szükséges](pim-resource-roles-approval-workflow.md)

[Az auditnapló használata](pim-resource-roles-use-the-audit-log.md)



