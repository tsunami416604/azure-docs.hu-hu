---
title: Az Azure többtényezős hitelesítés kikényszerítéséhez az Azure-erőforrások a Privileged Identity Management használatával |} A Microsoft Docs
description: Ez a dokumentum ismerteti a multi-factor authentication szolgáltatás engedélyezése a PIM erőforrás.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 21f6fef214f27630ff0eadc39e1e26c9c344f353
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444732"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Az Azure többtényezős hitelesítés kikényszerítéséhez az Azure-erőforrások a Privileged Identity Management használatával

Privileged Identity Management (PIM) az Azure-erőforrások szerepköreihez tartozó erőforrás-rendszergazdák és identitáskezelő adminisztrátorokat, kritikus fontosságú Azure-infrastruktúra időhöz kötött tagsággal és igény szerinti elérésének védelme érdekében lehetővé teszi. Emellett a PIM lehetővé választható kényszerítésének Azure multi-factor Authentication két különböző forgatókönyvekhez.

## <a name="require-multi-factor-authentication-to-activate"></a>Multi-factor Authentication szolgáltatás aktiválásának megkövetelése

Erőforrás a rendszergazdák megkövetelhetik, mielőtt aktiválna futtatásához az Azure multi-factor Authentication a szerepkör jogosult tagjai. Ez a folyamat biztosítja, hogy a felhasználó az aktiválás, akinek mondják ésszerű bizonyossággal kér. Kiemelt fontosságú erőforrások helyzetekben kényszerítése ezt a beállítást védi, ha a felhasználói fiók esetleg sérült a biztonsága. 

Ezt a követelményt kényszerítéséhez válassza ki egy erőforrást a felügyelt erőforrások listájából. Az a [áttekintő irányítópult](pim-resource-roles-overview-dashboards.md), válasszon egy szerepkört a képernyő jobb alsó része a szerepkörök listájában.

Ezenkívül érheti el a szerepkör-beállítások vagy a **szerepkörök** vagy **szerepkör-beállítások** lapokat a bal oldali panelen.

>[!Note]
>Ha a bal oldali ablaktáblán a beállítások szürkén jelennek meg, és megjelenik egy szalagcím, amely arról tájékoztatja a "You have jogosult szerepkörök aktiválása" az oldal tetején lévő, Ön nem rendszergazda aktív. Ez azt jelenti, hogy kell [aktiválása](pim-resource-roles-activate-your-roles.md) a folytatás előtt.

!["Szerepkör" és "Szerepkör-beállítások" lap ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

A szerepkör tagságát megtekintéséhez jelölje ki **szerepkör-beállítások** a nyissa meg a képernyő tetején lévő sávon a **szerepkör-beállítás részletei**.

A szerepkör-beállítások módosításához válassza a **szerkesztése** gombra az oldal tetején.

A szakaszában **aktiválás**, a jelölőnégyzet bejelölésével **multi-factor Authentication megkövetelése aktiváláskor**. Ezután válassza a **Save** (Mentés) lehetőséget.

![Multi-Factor Authentication megkövetelése aktiváláskor](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Kötelező többtényezős hitelesítés a hozzárendelési

Bizonyos esetekben egy erőforrás-kezelője érdemes tag hozzárendelése szerepkörhöz (egy nap, a példában) rövid időre. Ebben az esetben nem szükséges a kijelölt tag(ok) aktiválás kéréséhez. Ebben a forgatókönyvben a PIM nem tudja érvényesíteni multi-factor Authentication, ha a tag használja a saját szerepkör-hozzárendelés, mivel azok már aktív az abban a pillanatban, hozzá vannak rendelve a szerepkör.

Győződjön meg arról, hogy az erőforrás-kezelője a hozzárendelés foglaltak az, akinek mondják, kényszerítheti a multi-factor Authentication szolgáltatás hozzárendelés.

Az azonos szerepkör beállítás részletek képernyőn, a jelölőnégyzet bejelölésével **többtényezős hitelesítés megkövetelése a közvetlen hozzárendelés**.

![Közvetlen hozzárendelés a többtényezős hitelesítés megkövetelése](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>További lépések

[Aktiválásához jóváhagyás szükséges](pim-resource-roles-approval-workflow.md)

[Az auditnapló használata](pim-resource-roles-use-the-audit-log.md)



