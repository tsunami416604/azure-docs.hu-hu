---
title: Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan hagyhatja jóvá vagy tilthatja le a Azure AD Privileged Identity Management (PIM) Azure AD-szerepköreire vonatkozó kérelmeket.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
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
ms.openlocfilehash: 60a8d373a7e6edeaefd933e4f8ec8ee11e3c14ee
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804035"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-ben

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával konfigurálhatja a szerepköröket az aktiválás jóváhagyásához, és egy vagy több felhasználót vagy csoportot delegált jóváhagyóként választhat ki. A delegált jóváhagyók 24 órával a kérelmek jóváhagyására jogosultak. Ha a kérést 24 órán belül nem hagyták jóvá, a jogosult felhasználónak újra be kell küldenie egy új kérelmet. A 24 órás jóváhagyási idő ablak nem konfigurálható.

Kövesse a cikkben ismertetett lépéseket az Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyásához vagy elutasításához.

## <a name="view-pending-requests"></a>Függőben lévő kérések megtekintése

Meghatalmazott jóváhagyóként e-mail-értesítést fog kapni, ha egy Azure AD-szerepkörre vonatkozó kérelem jóváhagyása függőben van. Ezeket a függőben lévő kéréseket a PIM-ben tekintheti meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

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

## <a name="next-steps"></a>További lépések

- [E-mail-értesítések a PIM-ben](pim-email-notifications.md)
- [Azure-erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-ben](pim-resource-roles-approval-workflow.md)
