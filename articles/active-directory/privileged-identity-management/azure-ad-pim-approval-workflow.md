---
title: Hagyja jóvá vagy utasítsa a PIM az Azure AD-címtárbeli szerepkörökhöz tartozó kérelmek |} A Microsoft Docs
description: Megtudhatja, hogyan jóváhagyja vagy elutasítja az Azure AD Privileged Identity Management (PIM) az Azure AD-címtárbeli szerepkörökhöz tartozó kérelmek.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: bc60c0dbe6c609a08907feb243fe514badfe3e4e
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288075"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Hagyja jóvá vagy utasítsa a PIM az Azure AD-címtárbeli szerepkörökhöz tartozó kérelmek

Az Azure AD Privileged Identity Management (PIM) jóváhagyást kér az aktiválási szerepkörök konfigurálása, és válasszon egy vagy több felhasználót, vagy delegált jóváhagyók csoportot.

## <a name="view-pending-requests"></a>Függőben lévő kérelmek megtekintése

Delegált jóváhagyónak Ha a kérelem jóváhagyásra váró kap e-mail-értesítések. Megtekintheti a függőben lévő kérések az PIM-ben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **kérések jóváhagyása**.

    ![A PIM az Azure AD-címtárbeli szerepkörök - szerepkörök](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Jóváhagyásra váró láthatja a kérések listáját.

## <a name="approve-requests"></a>Kérések jóváhagyása

1. Válassza ki a kérelmeket, és kattintson a kívánt **jóváhagyás** megnyitásához a jóváhagyás kijelölt kérések ablaktáblán.

    ![A PIM jóváhagyása kérések listája](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Az a **jóváhagyási OK** mezőbe írja be az okot.

    ![A PIM hagyja jóvá a kijelölt kérések](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Kattintson a **jóváhagyása**.

    A jóváhagyással frissülni fog az állapotjelzőben.

    ![A PIM hagyja jóvá a kijelölt kérések](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Kérelmek elutasítása

1. Válassza ki a kérések elutasítása, és kattintson a kívánt **Megtagadás** , nyissa meg a Megtagadás kijelölt kérések ablaktáblán.

    ![A PIM jóváhagyása kérések listája](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Az a **elutasítás oka** mezőbe írja be az okot.

    ![A PIM megtagadása kijelölt kérések](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Kattintson a **megtagadása**.

    A elutasítási frissül az állapotjelzőben.

## <a name="next-steps"></a>További lépések

- [Hagyja jóvá vagy utasítsa a PIM az Azure-erőforrások szerepköreihez tartozó kérelmek](pim-resource-roles-approval-workflow.md)
- [A PIM e-mail-értesítések](pim-email-notifications.md)
