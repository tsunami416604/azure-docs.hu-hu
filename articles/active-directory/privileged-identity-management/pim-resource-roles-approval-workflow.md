---
title: Azure-erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása a PIM -Azure AD - Microsoft dokumentumok
description: Ismerje meg, hogyan hagyhatja jóvá vagy tagadhatja meg az Azure-erőforrás-szerepkörökre vonatkozó kérelmeket az Azure AD emelt szintű identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021969"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Azure-erőforrás-szerepkörök kéréseinek jóváhagyása vagy megtagadása a kiemelt identitáskezelésben

A kiemelt identitáskezelés (PIM) az Azure Active Directoryban (Azure AD), konfigurálhatja szerepkörök jóváhagyást igényel aktiváláshoz, és válassza ki a felhasználók vagy csoportok az Azure AD-szervezet delegált jóváhagyóként. Azt javasoljuk, hogy válasszon ki két vagy több jóváhagyók minden szerepkör a kiemelt szerepkör-rendszergazda számítási feladatának csökkentése érdekében. A delegált jóváhagyóknak 24 órájuk van a kérelmek jóváhagyására. Ha egy kérelmet 24 órán belül nem hagynak jóvá, akkor a jogosult felhasználónak újra be kell nyújtania egy új kérelmet. A 24 órás jóváhagyási időablak nem konfigurálható.

Kövesse a cikkben leírt lépéseket az Azure-erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyásához vagy elutasításához.

## <a name="view-pending-requests"></a>Függőben lévő kérelmek megtekintése

Delegált jóváhagyóként e-mailben értesítést kap, ha egy Azure-erőforrásszerepkör-kérelem jóváhagyásra vár. Ezeket a függőben lévő kérelmeket a Kiemelt identitáskezelés.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza **a Kérelmek jóváhagyása**lehetőséget.

    ![Kérések jóváhagyása – Az Azure-erőforrások lap az áttekintési kérelmet jeleníti meg](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    A **Szerepkör-aktiváláskérések csoportban** megjelenik a jóváhagyásra váró kérelmek listája.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Keresse meg és jelölje ki a jóváhagyni kívánt kérelmet. Megjelenik egy jóváhagyó vagy megtagadási lap.

    ![Kérelmek jóváhagyása – a részletekkel és az Indoklás mezővel rendelkező ablaktábla jóváhagyása vagy megtagadása](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Az **Indoklás** mezőbe írja be az üzleti indoklást.

1. Válassza **a Jóváhagyás**lehetőséget. A jóváhagyásról értesítést fog kapni az Azure-ban.

    ![A kérelem jóváhagyásáról szóló értesítés jóváhagyása](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Kérések megtagadása

1. Keresse meg és jelölje ki a megtagadni kívánt kérést. Megjelenik egy jóváhagyó vagy megtagadási lap.

    ![Kérelmek jóváhagyása – a részletekkel és az Indoklás mezővel rendelkező ablaktábla jóváhagyása vagy megtagadása](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Az **Indoklás** mezőbe írja be az üzleti indoklást.

1. Válassza **a Megtagadás**lehetőséget. Megjelenik egy értesítés a megtagadással együtt.

## <a name="workflow-notifications"></a>Munkafolyamat-értesítések

Íme néhány információ a munkafolyamat-értesítésekről:

- A jóváhagyók e-mailben kapnak értesítést, ha egy szerepkörre vonatkozó kérelem felülvizsgálatra vár. Az e-mail értesítések közvetlen hivatkozást tartalmaznak a kérelemre, ahol a jóváhagyó jóváhagyhatja vagy megtagadhatja.
- A kérelmeket az első jóváhagyó oldja meg, aki jóváhagyja vagy elutasítja azokat.
- Amikor egy jóváhagyó válaszol a kérésre, minden jóváhagyó értesítést kap a műveletről.
- Az erőforrás-rendszergazdák értesítést kapnak, ha egy jóváhagyott felhasználó aktívvá válik a szerepkörükben.

>[!Note]
>Az erőforrás-rendszergazda, aki úgy véli, hogy egy jóváhagyott felhasználó nem lehet aktív, eltávolíthatja az aktív szerepkör-hozzárendelés a kiemelt identitáskezelés. Bár az erőforrás-rendszergazdák csak jóváhagyóként értesülnek a függőben lévő kérelmekről, a függőben lévő kérelmeket a Kiemelt identitáskezelés szolgáltatásban függőben lévő kérelmek megtekintésével tekinthetik meg és vonhatják vissza.

## <a name="next-steps"></a>További lépések

- [Azure-erőforrás-szerepkörök bővítése vagy megújítása a kiemelt identitáskezelésben](pim-resource-roles-renew-extend.md)
- [E-mail értesítések a kiemelt identitáskezelésben](pim-email-notifications.md)
- [Azure AD-szerepkörök kéréseinek jóváhagyása vagy megtagadása a kiemelt identitáskezelésben](azure-ad-pim-approval-workflow.md)
