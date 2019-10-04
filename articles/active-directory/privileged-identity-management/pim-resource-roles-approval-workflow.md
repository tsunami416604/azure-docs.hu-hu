---
title: Azure-beli erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan hagyhatja jóvá vagy tilthatja le a Azure AD Privileged Identity Management (PIM) Azure-beli erőforrás-szerepköreire vonatkozó kérelmeket.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b39434f8763e44a126f74ac9a19596e4413ae9c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804272"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Azure-erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-ben

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával konfigurálhatja a szerepköröket az aktiválás jóváhagyásához, és egy vagy több felhasználót vagy csoportot delegált jóváhagyóként választhat ki. A delegált jóváhagyók 24 órával a kérelmek jóváhagyására jogosultak. Ha a kérést 24 órán belül nem hagyták jóvá, a jogosult felhasználónak újra be kell küldenie egy új kérelmet. A 24 órás jóváhagyási idő ablak nem konfigurálható.

Az Azure-erőforrás szerepköreire vonatkozó kérések jóváhagyásához vagy elutasításához kövesse a jelen cikkben ismertetett lépéseket.

## <a name="view-pending-requests"></a>Függőben lévő kérések megtekintése

Meghatalmazott jóváhagyóként e-mail-értesítést fog kapni, ha az Azure-erőforrás szerepkörre vonatkozó kérelem jóváhagyásra vár. Ezeket a függőben lévő kéréseket a PIM-ben tekintheti meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson a **kérelmek jóváhagyása**gombra.

    ![Kérések jóváhagyása – Azure-erőforrások lap, amely a felülvizsgálati kérelmet tartalmazza](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    A **szerepkör-aktiválási kérések** szakaszban megtekintheti a jóváhagyásra váró kérelmek listáját.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Keresse meg és kattintson a jóváhagyni kívánt kérelemre. Megjelenik egy jóváhagyás vagy megtagadás panel.

    ![Kérelmek jóváhagyása – jóváhagyás vagy megtagadás ablaktábla részletekkel és indoklással](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Az **indoklás** mezőbe írja be az okot.

1. Kattintson a **jóváhagyás**gombra.

    Megjelenik egy értesítés a jóváhagyással.

    ![A kérelem jóváhagyását jelző értesítés jóváhagyása](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Kérelmek megtagadása

1. Keresse meg és kattintson a megtagadni kívánt kérelemre. Megjelenik egy jóváhagyás vagy megtagadás panel.

    ![Kérelmek jóváhagyása – jóváhagyás vagy megtagadás ablaktábla részletekkel és indoklással](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Az **indoklás** mezőbe írja be az okot.

1. Kattintson a **Megtagadás**gombra.

    Megjelenik egy értesítés az elutasítással.

## <a name="workflow-notifications"></a>Munkafolyamat-értesítések

Íme néhány információ a munkafolyamat-értesítésekről:

- A jóváhagyó lista összes tagja e-mailben értesítést kap, ha a szerepkörre vonatkozó kérés függőben van a felülvizsgálatban. Az e-mail-értesítések közé tartozik egy közvetlen hivatkozás a kérésre, ahol a jóváhagyó jóváhagyhatja vagy megtagadhatja a kérelmet.
- A kérelmeket a lista első tagja oldja fel, aki jóváhagyja vagy megtagadja a kérelmet.
- Ha egy jóváhagyó válaszol a kérelemre, a jóváhagyó lista összes tagja értesítést kap a műveletről.
- Az erőforrás-rendszergazdák értesítést kapnak, ha egy jóváhagyott tag aktív lesz a szerepkörében.

>[!Note]
>Egy erőforrás-rendszergazda, aki úgy véli, hogy egy jóváhagyott tag nem lehet aktív, el tudja távolítani az aktív szerepkör-hozzárendelést a PIM-ben. Bár az erőforrás-rendszergazdák nem kapnak értesítést a függőben lévő kérelmekről, kivéve, ha azok tagjai a jóváhagyó listának, az összes felhasználó függőben lévő kérelmeit megtekinthetik és megtörölhetik a PIM-ben függőben lévő kérelmek megtekintésével 

## <a name="next-steps"></a>További lépések

- [Azure-erőforrás szerepköreinek kiterjesztése vagy megújítása a PIM-ben](pim-resource-roles-renew-extend.md)
- [E-mail-értesítések a PIM-ben](pim-email-notifications.md)
- [Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-ben](azure-ad-pim-approval-workflow.md)
