---
title: Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan hagyhatja jóvá vagy tilthatja le a Azure AD Privileged Identity Management (PIM) Azure AD-szerepköreire vonatkozó kérelmeket.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3245f7343a48d3e54795c14dcb23b836c8d9d988
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756429"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával konfigurálhatja a szerepköröket az aktiválás jóváhagyásához, és egy vagy több felhasználót vagy csoportot delegált jóváhagyóként választhat ki. A delegált jóváhagyók 24 órával a kérelmek jóváhagyására jogosultak. Ha a kérést 24 órán belül nem hagyták jóvá, a jogosult felhasználónak újra be kell küldenie egy új kérelmet. A 24 órás jóváhagyási idő ablak nem konfigurálható.

Kövesse a cikkben ismertetett lépéseket az Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyásához vagy elutasításához.

## <a name="view-pending-requests"></a>Függőben lévő kérések megtekintése

Meghatalmazott jóváhagyóként e-mail-értesítést fog kapni, ha egy Azure AD-szerepkörre vonatkozó kérelem jóváhagyása függőben van. Ezeket a függőben lévő kérelmeket Privileged Identity Management tekintheti meg.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure ad-szerepkörök**elemre.

1. Kattintson a **kérelmek jóváhagyása**gombra.

    ![Azure AD-szerepkörök – kérelmek jóváhagyása](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

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

## <a name="next-steps"></a>Következő lépések

- [E-mail-értesítések Privileged Identity Management](pim-email-notifications.md)
- [Azure-beli erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management](pim-resource-roles-approval-workflow.md)
