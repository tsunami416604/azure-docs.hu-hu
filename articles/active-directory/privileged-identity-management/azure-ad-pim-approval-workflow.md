---
title: Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-ben – Azure AD | Microsoft Docs
description: Megtudhatja, hogyan hagyhatja jóvá vagy tilthatja le a Azure AD Privileged Identity Management (PIM) Azure AD-szerepköreire vonatkozó kérelmeket.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3261e30d024cedba5885019a62cba1e296c1c00d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025554"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával konfigurálhatja a szerepköröket az aktiválás jóváhagyásához, és egy vagy több felhasználót vagy csoportot delegált jóváhagyóként választhat ki. A delegált jóváhagyók 24 órával a kérelmek jóváhagyására jogosultak. Ha a kérést 24 órán belül nem hagyták jóvá, a jogosult felhasználónak újra be kell küldenie egy új kérelmet. A 24 órás jóváhagyási idő ablak nem konfigurálható.

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

A 2019-es verziótól kezdődően a Privileged Identity Management Azure AD-szerepkörök részét egy új verzióra frissíti a rendszer, amely megfelel az Azure-erőforrás-szerepkörökkel kapcsolatos élményeknek. Ez további funkciókat hoz létre, valamint [a meglévő API módosításait](azure-ad-roles-features.md#api-changes)is. Az új verzió bevezetését követően a cikkben ismertetett eljárások a jelenleg használt Privileged Identity Management verziójától függenek. Az ebben a szakaszban ismertetett lépéseket követve meghatározhatja, hogy a Privileged Identity Management melyik verzióját kell megadnia. A Privileged Identity Management-verziójának megismerése után kiválaszthatja a jelen cikkben szereplő, az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörnek.
1. Nyissa meg **Azure ad Privileged Identity Management**. Ha az Áttekintés oldal tetején található egy szalagcím, kövesse a jelen cikk **új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **előző verzió** lapon megjelenő utasításokat.

    ![Azure AD-szerepkörök új verziója](./media/pim-how-to-add-role-to-user/pim-new-version.png)

Kövesse a cikkben ismertetett lépéseket az Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyásához vagy elutasításához.

# <a name="new-versiontabnew"></a>[Új verzió](#tab/new)

## <a name="view-pending-requests"></a>Függőben lévő kérések megtekintése

Meghatalmazott jóváhagyóként e-mail-értesítést fog kapni, ha az Azure-erőforrás szerepkörre vonatkozó kérelem jóváhagyásra vár. Ezeket a függőben lévő kérelmeket Privileged Identity Management tekintheti meg.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza a **kérelmek jóváhagyása**lehetőséget.

    ![Kérések jóváhagyása – Azure-erőforrások lap, amely a felülvizsgálati kérelmet tartalmazza](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    A **szerepkör-aktiválási kérések** szakaszban megtekintheti a jóváhagyásra váró kérelmek listáját.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Keresse meg és válassza ki a jóváhagyni kívánt kérelmet. A jóváhagyás vagy megtagadás lap jelenik meg.

    ![Kérelmek jóváhagyása – jóváhagyás vagy megtagadás ablaktábla részletekkel és indoklással](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Az **indoklás** mezőben adja meg az üzleti indoklást.

1. Válassza a **jóváhagyás**lehetőséget. A jóváhagyásról Azure-értesítést fog kapni.

    ![A kérelem jóváhagyását jelző értesítés jóváhagyása](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Kérelmek megtagadása

1. Keresse meg és válassza ki a megtagadni kívánt kérelmet. A jóváhagyás vagy megtagadás lap jelenik meg.

    ![Kérelmek jóváhagyása – jóváhagyás vagy megtagadás ablaktábla részletekkel és indoklással](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Az **indoklás** mezőben adja meg az üzleti indoklást.

1. Válassza a **Megtagadás**lehetőséget. Megjelenik egy értesítés az elutasítással.

## <a name="workflow-notifications"></a>Munkafolyamat-értesítések

Íme néhány információ a munkafolyamat-értesítésekről:

- E-mailben értesítjük a jóváhagyókat, ha a szerepkörre vonatkozó kérések a felülvizsgálattól függőben vannak. Az e-mail-értesítések közé tartozik egy közvetlen hivatkozás a kérésre, ahol a jóváhagyó jóváhagyhatja vagy megtagadhatja a kérelmet.
- A kérelmeket az első jóváhagyó, aki jóváhagyja vagy megtagadja.
- Ha egy jóváhagyó válaszol a kérelemre, az összes jóváhagyó értesítést kap a műveletről.
- Az erőforrás-rendszergazdák értesítést kapnak, ha egy jóváhagyott felhasználó aktív lesz a szerepkörében.

>[!NOTE]
>Egy erőforrás-rendszergazda, aki úgy véli, hogy egy jóváhagyott felhasználó nem lehet aktív, el tudja távolítani az aktív szerepkör-hozzárendelést Privileged Identity Management. Bár az erőforrás-rendszergazdák nem kapnak értesítést a függőben lévő kérelmekről, kivéve, ha a jóváhagyók, megtekinthetik és megszakítják az összes felhasználóra vonatkozó függőben lévő kérelmek megtekintését Privileged Identity Management.

# <a name="previous-versiontabprevious"></a>[Előző verzió](#tab/previous)

## <a name="view-pending-requests"></a>Függőben lévő kérések megtekintése

Meghatalmazott jóváhagyóként e-mail-értesítést fog kapni, ha egy Azure AD-szerepkörre vonatkozó kérelem jóváhagyása függőben van. Ezeket a függőben lévő kérelmeket Privileged Identity Management tekintheti meg.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure ad-szerepkörök**elemre.

1. Kattintson a **kérelmek jóváhagyása**gombra.

    ![Azure AD-szerepkörök – kérelmek jóváhagyása](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Ekkor megjelenik a jóváhagyásra váró kérelmek listája.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Válassza ki a jóváhagyni kívánt kéréseket, majd kattintson a **jóváhagyás** gombra a kijelölt kérések jóváhagyása panel megnyitásához.

    ![A kérelmek jóváhagyása a jóváhagyás lehetőség kijelölve](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. A **jóváhagyás oka** mezőbe írjon be egy okot.

    ![A kijelölt kérések jóváhagyása a jóváhagyás indoklásával](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Kattintson a **jóváhagyás**gombra.

    Az állapotjelző szimbólumot a rendszer a jóváhagyással frissíti.

    ![Kijelölt kérelmek jóváhagyása ablaktábla jóváhagyása után gombra kattintás](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Kérelmek megtagadása

1. Válassza ki a megtagadni kívánt kéréseket, majd kattintson a **Megtagadás** gombra a kiválasztott kérelmek megtagadása ablaktábla megnyitásához.

    ![Megtagadási lehetőséggel rendelkező kérelmek listájának jóváhagyása kijelölve](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. A **Megtagadás oka** mezőbe írjon be egy okot.

    ![A kijelölt kérések ablaktábla megtagadása megtagadás miatt](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Kattintson a **Megtagadás**gombra.

    A rendszer a megtagadásával frissíti az állapotjelző szimbólumot.

---

## <a name="next-steps"></a>Következő lépések

- [E-mail-értesítések Privileged Identity Management](pim-email-notifications.md)
- [Azure-beli erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management](pim-resource-roles-approval-workflow.md)
