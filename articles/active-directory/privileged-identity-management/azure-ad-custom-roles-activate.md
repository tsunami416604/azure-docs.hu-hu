---
title: Egyéni Azure AD-szerepkör aktiválása a Privileged Identity Managementban (PIM) | Microsoft Docs
description: Egyéni Azure AD-szerepkör aktiválása hozzárendelési Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ac8acf2193442de5c85e0a1134d46330821c77b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756441"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Egyéni Azure AD-szerepkör aktiválása Privileged Identity Management

A Azure Active Directory (Azure AD) Privileged Identity Management mostantól támogatja az alkalmazás-felügyelethez létrehozott egyéni szerepköröket az identitás-és hozzáférés-kezelés felügyeleti felületén. További információ az Azure AD-ben az alkalmazások felügyeletének delegálására szolgáló egyéni szerepkörök létrehozásáról: [Azure Active Directory (előzetes verzió) – egyéni rendszergazdai szerepkörök](../users-groups-roles/roles-custom-overview.md).

> [!NOTE]
> Az Azure AD egyéni szerepkörei nincsenek integrálva az előzetes verzióban a beépített címtárbeli szerepkörökbe. Miután a képesség általánosan elérhetővé válik, a szerepkör-kezelés a beépített szerepkörök felületén történik.

## <a name="activate-a-role"></a>Szerepkör aktiválása

Ha egy egyéni Azure AD-szerepkört kell aktiválnia, az aktiválás kéréséhez válassza a saját szerepkörök navigációs lehetőséget a Privileged Identity Management.

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com).
1. Nyissa meg az Azure AD [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart).

1. Válassza az **Egyéni Azure ad-szerepkörök** lehetőséget a jogosult Azure ad egyéni szerepkör-hozzárendelések listájának megtekintéséhez.

   ![Tekintse meg a jogosult Azure AD egyéni szerepkör-hozzárendelések listáját](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

1. Az **Azure ad Custom roles (előzetes verzió)** lapon keresse meg a szükséges hozzárendelést.
1. Válassza a **szerepkör aktiválása** lehetőséget az **aktiválás** lap megnyitásához.
1. Ha a szerepkör többtényezős hitelesítést igényel, akkor **a folytatás előtt válassza a személyazonosság ellenőrzése**lehetőséget. A hitelesítést csak egyszer kell elvégezni egy munkamenetben.
1. Válassza a **személyazonosság ellenőrzése** lehetőséget, és kövesse az utasításokat a további biztonsági ellenőrzés megadásához.
1. Egyéni alkalmazás hatókörének megadásához válassza a **hatókör** lehetőséget a szűrő ablaktábla megnyitásához. A szükséges minimális hatókörű szerepkörhöz hozzáférést kell kérnie. Ha a hozzárendelés alkalmazás-hatókörben van, csak az adott hatókörön lehet aktiválni.

   ![Azure AD-erőforrás hatókörének hozzárendelése a szerepkör-hozzárendeléshez](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Ha szükséges, egy egyéni aktiválás kezdési idejét kell megadnia. Használat esetén a szerepkör tagja aktiválva lesz a megadott időpontban.
1. Az **OK** mezőben adja meg az aktiválási kérelem okát. Ezeket kötelezővé teheti, vagy nem a szerepkör-beállításban.
1. Válassza az **aktiválás**lehetőséget.

Ha a szerepkörhöz nem szükséges jóváhagyás, a beállításoknak megfelelően aktiválódik, és a rendszer hozzáadja az aktív szerepkörök listájához. Ha az aktivált szerepkört szeretné használni, kezdje a következő témakörben ismertetett lépésekkel: az [Azure ad egyéni szerepkörének társítása a Privileged Identity Management](azure-ad-custom-roles-assign.md).

Ha a szerepkör jóváhagyást igényel az aktiváláshoz, egy Azure-értesítés jelenik meg, amely tájékoztatja arról, hogy a kérelem jóváhagyása függőben van.

## <a name="next-steps"></a>Következő lépések

- [Egyéni Azure AD-szerepkör kiosztása](azure-ad-custom-roles-assign.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)
- [Szerepkör-definíciók az Azure AD-ben](../users-groups-roles/directory-assign-admin-roles.md)
