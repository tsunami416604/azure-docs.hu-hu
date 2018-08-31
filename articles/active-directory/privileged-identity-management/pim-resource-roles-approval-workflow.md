---
title: Hagyja jóvá vagy utasítsa a PIM az Azure-erőforrások szerepköreihez tartozó kérelmek |} A Microsoft Docs
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
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e5cda31af5ac95e7ebe2b858b1d7355ea3f2a6bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189804"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Hagyja jóvá vagy utasítsa a PIM az Azure-erőforrások szerepköreihez tartozó kérelmek

Jóváhagyja vagy elutasítja a kérelmet, a jóváhagyó lista tagjának kell lennie. 

1. A PIM, válassza ki **kérések jóváhagyása** a lapról, a bal oldali menüben, és keresse meg a kérelmet.

   !["A kérések jóváhagyása" panel](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Válassza ki a kérést, adja meg a döntést az indoklást, és válassza **jóváhagyás** vagy **Megtagadás**. A kérelem majd fog állni.

   ![Részletes információkat a kijelölt kérelem](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>A munkafolyamat-értesítések

Az alábbiakban néhány alapvető tudnivalók a munkafolyamat-értesítést:

- A jóváhagyó lista tagjainak az összes értesítést kap e-mailben a felülvizsgálatot függőben van egy kérelem egy adott szerepkör esetében. E-mail értesítések közé tartoznak a kérést, ha a jóváhagyó jóváhagyhatják vagy megtagadhatják a közvetlen hivatkozást.
- Kérelmek jóváhagyja vagy megtagadja a lista első tag által megoldott. 
- Amikor egy jóváhagyó válaszolt a kérelemre, a jóváhagyó lista összes tagja, a művelet értesítést kap. 
- Erőforrás-rendszergazdák értesítést kap egy jóváhagyott tagot a szerepkörük aktívvá válik. 

>[!Note]
>Úgy véli, hogy egy jóváhagyott tag nem lehet aktív erőforrás rendszergazda eltávolíthatja a aktív szerepkör-hozzárendelés az PIM-ben. Bár az erőforrás-rendszergazdák nem kapnak értesítést a függőben lévő kérések kivéve, ha a jóváhagyó lista tagjainak, megtekintheti és visszavonása függőben lévő kéréseket az összes olyan felhasználó megtekinti a függőben lévő kéréseket az PIM-ben. 

## <a name="next-steps"></a>További lépések

- [Hagyja jóvá vagy utasítsa a PIM az Azure AD-címtárbeli szerepkörökhöz tartozó kérelmek](azure-ad-pim-approval-workflow.md)
- [A PIM e-mail-értesítések](pim-email-notifications.md)
