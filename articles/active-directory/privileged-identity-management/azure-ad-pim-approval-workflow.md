---
title: Azure AD-szerepkörök kéréseinek jóváhagyása vagy elutasítása a PIM-ben – Azure AD | Microsoft dokumentumok
description: Ismerje meg, hogyan hagyhatja jóvá vagy tagadhatja meg az Azure AD-szerepkörök kéréseit az Azure AD emelt szintű identitáskezelés (PIM) szolgáltatásában.
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
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccfd1ad270072989e9b575fda538b94fd8927c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049019"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörök kéréseinek jóváhagyása vagy megtagadása a kiemelt identitáskezelésben

Az Azure Active Directory (Azure AD) privilegizált identitáskezelés (PIM) segítségével konfigurálhatja a szerepköröket az aktiválás jóváhagyásának megkövetelésére, és egy vagy több felhasználót vagy csoportot delegált jóváhagyóként választhat. A delegált jóváhagyóknak 24 órájuk van a kérelmek jóváhagyására. Ha egy kérelmet 24 órán belül nem hagynak jóvá, akkor a jogosult felhasználónak újra be kell nyújtania egy új kérelmet. A 24 órás jóváhagyási időablak nem konfigurálható.

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

2019 novemberétől kezdődően a kiemelt identitáskezelés Azure AD-szerepkörök része frissül egy új verzióra, amely megfelel az Azure-szerepkörök élményének. Ez további funkciókat, valamint [a meglévő API-t is módosítja.](azure-ad-roles-features.md#api-changes) Az új verzió bevezetés alatt, hogy mely eljárásokat követi ebben a cikkben attól függ, hogy a kiemelt identitáskezelés jelenleg rendelkezik-e. Ebben a szakaszban ismertetett lépéseket követve határozza meg, hogy a Kiemelt identitáskezelés melyik verzióját használja. Miután ismeri a kiemelt identitáskezelés verzióját, kiválaszthatja a cikkben az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) egy olyan felhasználóval, aki a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörben van.
1. Nyissa meg **az Azure AD kiemelt identitáskezelés .** Ha az áttekintő oldal tetején szalaghirdetés található, kövesse a cikk **Új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **Előző verzió** lap utasításait.

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Kövesse a cikkben leírt lépéseket az Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyásához vagy elutasításához.

# <a name="new-version"></a>[Új verzió](#tab/new)

## <a name="view-pending-requests"></a>Függőben lévő kérelmek megtekintése

Delegált jóváhagyóként e-mailben értesítést kap, ha egy Azure AD-szerepkör-kérelem jóváhagyásra vár. Ezeket a függőben lévő kérelmeket a Kiemelt identitáskezelés.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza **a Kérelmek jóváhagyása**lehetőséget.

    ![Kérések jóváhagyása – az Azure AD-szerepkörök véleményezésére irányuló kérést megjelenítő lap](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    A **Szerepkör-aktiváláskérések csoportban** megjelenik a jóváhagyásra váró kérelmek listája.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Keresse meg és jelölje ki a jóváhagyni kívánt kérelmet. Megjelenik egy jóváhagyó vagy megtagadási lap.

    ![Kérelmek jóváhagyása – a részletekkel és az Indoklás mezővel rendelkező ablaktábla jóváhagyása vagy megtagadása](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Az **Indoklás** mezőbe írja be az üzleti indoklást.

1. Válassza **a Jóváhagyás**lehetőséget. A jóváhagyásról értesítést fog kapni az Azure-ban.

    ![A kérelem jóváhagyásáról szóló értesítés jóváhagyása](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

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
- A globális rendszergazdák és a kiemelt szerepkör-rendszergazdák értesítést kapnak, ha egy jóváhagyott felhasználó aktívvá válik a szerepkörükben.

>[!NOTE]
>A globális rendszergazda vagy kiemelt szerepkör-rendszergazda, aki úgy véli, hogy egy jóváhagyott felhasználó nem lehet aktív eltávolíthatja az aktív szerepkör-hozzárendelés kiemelt identitáskezelés. Bár a rendszergazdák nem kapnak értesítést a függőben lévő kérelmekről, kivéve, ha jóváhagyó, megtekinthetik és visszavonhatják az összes felhasználó függőben lévő kérelmeit a függőben lévő kérelmek megtekintésével a Kiemelt identitáskezelés ben.

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

## <a name="view-pending-requests"></a>Függőben lévő kérelmek megtekintése

Delegált jóváhagyóként e-mailben értesítést kap, ha egy Azure AD-szerepkör-kérelem jóváhagyásra vár. Ezeket a függőben lévő kérelmeket a Kiemelt identitáskezelés.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Kattintson **az Azure AD-szerepkörök**elemre.

1. Kattintson **a Kérelmek jóváhagyása gombra.**

    ![Azure AD-szerepkörök – kérések jóváhagyása](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Megjelenik a jóváhagyásra váró kérelmek listája.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Jelölje ki a jóváhagyni kívánt kérelmeket, majd kattintson a **Jóváhagyás** gombra a Kijelölt kérelmek jóváhagyása ablaktábla megnyitásához.

    ![Kérelmek listájának jóváhagyása kiemelt Jóváhagyás beállítással](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Az **Ok jóváhagyása** mezőbe írjon be egy okot.

    ![Kijelölt kérelmek ablaktábla jóváhagyása okokkal](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Kattintson az **Approve** (Jóváhagyás) elemre.

    Az Állapot szimbólum az Ön jóváhagyásával frissül.

    ![A kijelölt kérelmek ablaktábla jóváhagyása a Jóváhagyás gombra kattintás után](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Kérések megtagadása

1. Jelölje ki az elutasítandó kérelmeket, majd kattintson a **Megtagadás gombra** a Kijelölt kérelmek megtagadása ablaktábla megnyitásához.

    ![Kérelmek listájának jóváhagyása kiemelt Megtagadás beállítással](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Az **Ok megtagadása** mezőbe írjon be egy okot.

    ![A kijelölt kérelmek panelmegtagadási okból megtagadva](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Válassza **a Megtagadás**lehetőséget.

    Az Állapot szimbólum frissül a tagadással.

---

## <a name="next-steps"></a>További lépések

- [E-mail értesítések a kiemelt identitáskezelésben](pim-email-notifications.md)
- [Azure-erőforrás-szerepkörök kéréseinek jóváhagyása vagy megtagadása a kiemelt identitáskezelésben](pim-resource-roles-approval-workflow.md)
