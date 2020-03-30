---
title: Az Azure AD egyéni szerepköraktiválása – Kiemelt identitáskezelés (PIM)
description: Azure AD egyéni szerepkör aktiválása a hozzárendelési jogosultságú identitáskezeléshez (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
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
ms.openlocfilehash: cbd60d1311bd84adb303a0d329ab4e42f4d61525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498733"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Azure AD egyéni szerepkör aktiválása a kiemelt identitáskezelésben

Az Azure Active Directory (Azure AD) kiemelt identitáskezelés mostantól támogatja a just-in-time és az időhöz kötött hozzárendelést az identitás- és hozzáférés-kezelési felügyeleti felületen az alkalmazáskezeléshez létrehozott egyéni szerepkörökhöz. Az Azure AD-ben az alkalmazáskezelés delegálására szolgáló egyéni szerepkörök létrehozásáról az [Egyéni rendszergazdai szerepkörök az Azure Active Directoryban (előzetes verzió)](../users-groups-roles/roles-custom-overview.md)című témakörben talál további információt.

> [!NOTE]
> Az Azure AD egyéni szerepkörök nincsenek integrálva a beépített címtárszerepkörök előzetes verzió során. Ha a képesség általánosan elérhetővé válik, a szerepkör-kezelés a beépített szerepkörök ben kerül sor. Ha a következő szalagcím jelenik meg, ezeket a szerepköröket [a beépített szerepkörök ben](pim-how-to-activate-role.md) kell kezelni, és ez a cikk nem vonatkozik:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="activate-a-role"></a>Szerepkör aktiválása

Ha aktiválnia kell egy Azure AD egyéni szerepkört, kérje az aktiválást a Szerepkörök közötti navigációs beállítás kiválasztásával a Kiemelt identitáskezelésben.

1. Jelentkezzen be [az Azure Portalra.](https://portal.azure.com)
1. Nyissa meg az Azure AD [kiemelt identitáskezelés .](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)

1. Válassza ki **az Azure AD egyéni szerepkörök** a jogosult Azure AD egyéni szerepkör-hozzárendelések listájának megtekintéséhez.

   ![Tekintse meg a jogosult Azure AD egyéni szerepkör-hozzárendelések listáját](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
>  Szerepkör hozzárendelése előtt létre kell hoznia/konfigurálnia kell egy szerepkört. Az AAD egyéni szerepkörök konfigurálásával kapcsolatos további információkért lásd [itt] (https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-custom-roles-configure)

1. Az **Azure AD egyéni szerepkörök (előzetes verzió)** lapon keresse meg a szükséges hozzárendelést.
1. Válassza **a Szerepkör aktiválása** lehetőséget az **Aktiválás** lap megnyitásához.
1. Ha a szerepkör többtényezős hitelesítést igényel, a folytatás előtt válassza **a Személyazonosság ellenőrzése lehetőséget.** Munkamenetenként csak egyszer kell hitelesítenie magát.
1. Válassza **a Személyazonosságom ellenőrzése lehetőséget,** és kövesse az utasításokat a további biztonsági ellenőrzésekhez.
1. Egyéni alkalmazáshatókör megadásához válassza a **Hatókör** lehetőséget a szűrőablak megnyitásához. Hozzáférést kell kérnie egy szerepkörhöz a szükséges minimális hatókörön. Ha a hozzárendelés egy alkalmazáshatókörben van, csak az adott hatókörben aktiválhatja azaktiválást.

   ![Azure AD erőforráshatókör hozzárendelése a szerepkör-hozzárendeléshez](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Szükség esetén adjon meg egyéni aktiválási kezdési időpontot. Ha használja, a szerepkör tag aktiválódik a megadott időpontban.
1. Az **Ok** mezőbe írja be az aktiválási kérelem okát. Ezek lehet szükség, vagy nem a szerepkör-beállítás.
1. Válassza **az Aktiválás**lehetőséget.

Ha a szerepkör nem igényel jóváhagyást, akkor a beállításoknak megfelelően aktiválódik, és hozzáadódik az aktív szerepkörök listájához. Ha az aktivált szerepkört szeretné használni, kezdje az [Azure AD egyéni szerepkör hozzárendelése a kiemelt identitáskezelésben](azure-ad-custom-roles-assign.md)című lépéseivel.

Ha a szerepkör aktiválásához jóváhagyásszükséges, egy Azure-értesítést kap, amely tájékoztatja, hogy a kérelem jóváhagyásra vár.

## <a name="next-steps"></a>További lépések

- [Azure AD egyéni szerepkör hozzárendelése](azure-ad-custom-roles-assign.md)
- [Azure AD egyéni szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Azure AD egyéni szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)
- [Szerepkör-definíciók az Azure AD-ben](../users-groups-roles/directory-assign-admin-roles.md)
