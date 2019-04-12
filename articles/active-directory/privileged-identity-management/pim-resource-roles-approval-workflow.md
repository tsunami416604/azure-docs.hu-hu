---
title: Hagyja jóvá vagy utasítsa a PIM - Azure Active Directory Azure-erőforrások szerepköreihez tartozó kérelmek |} A Microsoft Docs
description: Megtudhatja, hogyan jóváhagyja vagy elutasítja az Azure AD Privileged Identity Management (PIM) az Azure-erőforrások szerepköreihez tartozó kérelmek.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f645b7077ef43dc7eb4d70261b6b601b5e4af1b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492158"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Hagyja jóvá vagy utasítsa a PIM az Azure-erőforrások szerepköreihez tartozó kérelmek

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM), konfigurálhatja szerepkörök jóváhagyást kér az aktiválás, és válasszon egy vagy több felhasználót, vagy delegált jóváhagyók csoportot. Delegált jóváhagyók rendelkezik jóváhagyásra váró kérelmek 24 óra. Ha 24 órán belül nem jóváhagyják a kérését, majd a jogosult felhasználó újra egy új kérelmet kell benyújtania. A 24 órás jóváhagyási időtartomány érték nem módosítható.

Kövesse az ebben a cikkben jóváhagyja vagy elutasítja a kérelmeket az Azure-erőforrások szerepköreihez tartozó lépéseket.

## <a name="view-pending-requests"></a>Függőben lévő kérelmek megtekintése

Delegált jóváhagyójaként e-mailben értesítést fog kapni, ha egy Azure-erőforrás szerepkör kérelem jóváhagyásra váró. Megtekintheti a függőben lévő kérések az PIM-ben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **kérések jóváhagyása**.

    ![Azure-erőforrás - kérelmek jóváhagyása](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Az a **szerepkör-aktiválásokhoz tartozó kérelmek** szakaszban jóváhagyásra váró kérelmek listáját láthatja.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Keresse meg és kattintson a jóváhagyni kívánt kérést. Egy jóváhagyási panel jelenik meg.

    ![Hagyja jóvá a kérelmek panel](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Az a **indoklás** mezőbe írja be az okot.

1. Kattintson a **jóváhagyása**.

    A jóváhagyással megjelenik egy értesítés.

    ![Értesítés jóváhagyása](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Kérelmek elutasítása

1. Keresse meg és kattintson a kérelemhez, amely meg szeretné tagadni. Egy jóváhagyási panel jelenik meg.

    ![Hagyja jóvá a kérelmek panel](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Az a **indoklás** mezőbe írja be az okot.

1. Kattintson a **megtagadása**.

    Megjelenik egy értesítés, a elutasítva.

## <a name="workflow-notifications"></a>A munkafolyamat-értesítések

A következő munkafolyamat-értesítést némi információt:

- A jóváhagyó lista tagjainak az összes értesítést kap e-mailben a felülvizsgálatot függőben van egy kérelem egy adott szerepkör esetében. E-mail értesítések közé tartoznak a kérést, ha a jóváhagyó jóváhagyhatják vagy megtagadhatják a közvetlen hivatkozást.
- Kérelmek jóváhagyja vagy megtagadja a lista első tag által megoldott.
- Amikor egy jóváhagyó válaszolt a kérelemre, a jóváhagyó lista összes tagja, a művelet értesítést kap.
- Erőforrás-rendszergazdák értesítést kap egy jóváhagyott tagot a szerepkörük aktívvá válik.

>[!Note]
>Úgy véli, hogy egy jóváhagyott tag nem lehet aktív erőforrás rendszergazda eltávolíthatja a aktív szerepkör-hozzárendelés az PIM-ben. Bár az erőforrás-rendszergazdák nem kapnak értesítést a függőben lévő kérések kivéve, ha a jóváhagyó lista tagjainak, megtekintheti és visszavonása függőben lévő kéréseket az összes olyan felhasználó megtekinti a függőben lévő kéréseket az PIM-ben. 

## <a name="next-steps"></a>További lépések

- [A PIM az Azure-erőforrás szerepkörök meghosszabbítása vagy megújítása](pim-resource-roles-renew-extend.md)
- [A PIM e-mail-értesítések](pim-email-notifications.md)
- [Hagyja jóvá vagy utasítsa a PIM az Azure AD-szerepkörökhöz tartozó kérelmek](azure-ad-pim-approval-workflow.md)
