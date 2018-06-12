---
title: Azure többtényezős hitelesítés kikényszerítéséhez a Azure-erőforrások Privileged Identity Management használatával |} Microsoft Docs
description: Ez a dokumentum ismerteti a PIM erőforrások többtényezős hitelesítés engedélyezése.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 0c50a17b651fc1fb5d49915da5da4a37d40a0d3e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260063"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Azure többtényezős hitelesítés kikényszerítéséhez a Azure-erőforrások Privileged Identity Management használatával

Privileged Identity Management (PIM) az Azure erőforrás-szerepkörök lehetővé teszi, hogy erőforrás és identitás rendszergazdák időhöz kötött tagság és just-in-time elérése kritikus Azure-infrastruktúra védelméhez. Emellett a PIM két különböző forgatókönyvekhez Azure multi-factor Authentication választható kikényszerítését biztosítja.

## <a name="require-multi-factor-authentication-to-activate"></a>Aktiválja a többtényezős hitelesítést

Erőforrás-Rendszergazdák szerepkör jogosult tagjai Azure multi-factor Authentication futtatásához, mielőtt aktiválna lehet szükség. Ez a folyamat biztosítja, hogy a felhasználó által kért aktiválási az, akinek azok ésszerű Biztosan fel azokat. Helyzetekben fontos erőforrásokra ezt a beállítást kényszerítése védi, ha a felhasználói fiók hatékonyságát. 

Ez a követelmény kényszeríti, válasszon ki egy erőforrástípust a felügyelt erőforrások közül. Az a [áttekintő irányítópulthoz](pim-resource-roles-overview-dashboards.md), válasszon egy szerepkört a képernyő jobb alsó részén található szerepkörök listája.

Emellett letölthető szerepkör beállításai vagy a **szerepkörök** vagy **szerepkör beállításainak** a bal oldali panelen fülek.

>[!Note]
>Ha a bal oldali panelen a beállítások használhatók, és megjelenik egy szalagcím, mely szerint a "Rendelkezik aktiválható jogosult szerepkörtől" a lap tetején, nem aktív rendszergazdaként jelentkezett. Ez azt jelenti, hogy kell [aktiválása](pim-resource-roles-activate-your-roles.md) folytatása előtt.

!["Szerepkörök" és "Szerepkör-beállítások" lapon ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Válassza ki, ha a szerepköri tagság **szerepkör beállításainak** az segítségével nyissa meg a képernyő tetején a **szerepkör beállítás részletei**.

A beállítások módosításához válassza a **szerkesztése** gombra az oldal tetején.

A szakaszában **aktiválás**, a jelölőnégyzet bejelölésével **többtényezős hitelesítés szükséges a aktiválás**. Ezután válassza a **Save** (Mentés) lehetőséget.

![Multi-Factor Authentication megkövetelése aktiváláskor](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Többtényezős hitelesítés megkövetelése hozzárendelés

Bizonyos esetekben egy erőforrás-kezelője célszerű tagja hozzárendelése egy szerepkörhöz rövid időtartamra (egy nap, a példában). Ebben az esetben nem kell a hozzárendelt tag aktiválás kéréséhez. Ebben a forgatókönyvben a PIM nem többtényezős hitelesítés kikényszerítéséhez amikor tag a szerepkör-hozzárendelés használja, mert azok már aktív abban a pillanatban hozzárendeli a szerepkörben.

Annak biztosításához, hogy az erőforrás-kezelője, a hozzárendelés teljesítését számára azok fel vannak, a hozzárendelés kényszerítheti a többtényezős hitelesítést.

Az azonos szerepkör beállítás részletei képernyőn, a jelölőnégyzet bejelölésével **többtényezős hitelesítést igényelnek rendelését**.

![Többtényezős hitelesítés megkövetelése közvetlen hozzárendelés](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>További lépések

[Jóváhagyás aktiválásához szükséges](pim-resource-roles-approval-workflow.md)

[Az auditnapló használata](pim-resource-roles-use-the-audit-log.md)



