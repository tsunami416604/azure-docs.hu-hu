---
title: Az Azure AD egyéni szerepkör frissítése vagy eltávolítása – Kiemelt identitáskezelés (PIM)
description: Azure AD egyéni szerepkör-hozzárendelés pim-kezelése (PIM) frissítése vagy eltávolítása
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad5747be47e250cf9c623cc40d21d12c91ee16f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499100"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Hozzárendelt Azure AD egyéni szerepkör frissítése vagy eltávolítása a kiemelt identitáskezelésben

Ez a cikk bemutatja, hogyan használhatja a kiemelt identitáskezelés (PIM) frissítésére vagy eltávolítására just-in-time és időhöz kötött hozzárendelés egyéni szerepkörök höz létrehozott egyéni szerepkörök az Azure Active Directory (Azure AD) felügyeleti élményt. 

- Az Azure AD-ben az alkalmazáskezelés delegálására szolgáló egyéni szerepkörök létrehozásáról az [Egyéni rendszergazdai szerepkörök az Azure Active Directoryban (előzetes verzió)](../users-groups-roles/roles-custom-overview.md)című témakörben talál további információt. 
- Ha még nem használta a Kiemelt identitáskezelés szolgáltatást, további információt a [Kiemelt identitáskezelés használatának megkezdése](pim-getting-started.md)című részen talál.

> [!NOTE]
> Az Azure AD egyéni szerepkörök nincsenek integrálva a beépített címtárszerepkörök előzetes verzió során. Ha a képesség általánosan elérhetővé válik, a szerepkör-kezelés a beépített szerepkörök ben kerül sor. Ha a következő szalagcím jelenik meg, ezeket a szerepköröket [a beépített szerepkörök ben](pim-how-to-add-role-to-user.md) kell kezelni, és ez a cikk nem vonatkozik:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Hozzárendelés frissítése vagy eltávolítása

Meglévő egyéni szerepkör-hozzárendelés frissítéséhez vagy eltávolításához kövesse az alábbi lépéseket.

1. Jelentkezzen be a [kiemelt identitáskezelés](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) az Azure Portalon egy felhasználói fiókkal, amely hozzá van rendelve a kiemelt szerepkör-rendszergazdai szerepkörhöz.
1. Válassza az **Azure AD egyéni szerepkörök (előzetes verzió)** lehetőséget.

    ![Válassza ki az Azure AD egyéni szerepkörök előnézetét a jogosult szerepkör-hozzárendelések megtekintéséhez](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Válassza ki a **szerepkörök** az Azure AD-alkalmazások egyéni szerepköreinek **hozzárendelések** listájának megtekintéséhez.

    ![Válassza ki a Szerepkörök lehetőséget, tekintse meg a jogosult szerepkör-hozzárendelések listáját](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Jelölje ki a frissíteni vagy eltávolítani kívánt szerepkört.
1. Keresse meg a szerepkör-hozzárendelést a **Jogosult szerepkörök** vagy **az Aktív szerepkörök** lapon.
1. A **szerepkör-hozzárendelés** frissítéséhez vagy eltávolításához válassza a Frissítés vagy az **Eltávolítás** lehetőséget.

    ![Eltávolítás vagy frissítés kiválasztása a jogosult szerepkör-hozzárendelésben](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>További lépések

- [Egyéni Azure AD-szerepkör aktiválása](azure-ad-custom-roles-assign.md)
- [Azure AD egyéni szerepkör hozzárendelése](azure-ad-custom-roles-assign.md)
- [Azure AD egyéni szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)
