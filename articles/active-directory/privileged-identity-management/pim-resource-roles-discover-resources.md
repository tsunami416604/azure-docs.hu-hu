---
title: Fedezze fel a PIM-ben kezelendő Azure-erőforrásokat – Azure AD | Microsoft dokumentumok
description: Ismerje meg, hogyan fedezheti fel az Azure-erőforrásokat az Azure AD kiemelt identitáskezelés (PIM) kezelésében.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022894"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Fedezze fel az Azure-erőforrásokat a kiemelt identitáskezelés ben kezelhető

Az Azure Active Directory (Azure AD) privilegizált identitáskezelés (PIM) használatával javíthatja az Azure-erőforrások védelmét. Ez olyan szervezetek számára hasznos, amelyek már használják a Kiemelt identitáskezelés az Azure AD-szerepkörök védelmét, valamint a felügyeleti csoport és az előfizetés-tulajdonosok számára, akik az éles erőforrások at szeretnének biztosítani.

Amikor először állítja be a kiemelt identitáskezelés azure-erőforrások, fel kell derítenie, és válassza ki a kiemelt identitáskezelés védeni kívánt erőforrásokat. Nincs korlátozva, hogy hány erőforrást, amely kezelhető a kiemelt identitáskezelés. Azonban azt javasoljuk, hogy a legkritikusabb (éles) erőforrásokkal kezdje.

## <a name="discover-resources"></a>Erőforrások felfedezése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure-erőforrások lehetőséget.**

    Ha ez az első alkalom, hogy az Azure-erőforrások privilegizált identitáskezelése, megjelenik a **Discover resources** lap.

    ![Erőforrások felderítése ablaktábla az első élményhez nem felsorolt erőforrások nélkül](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Ha a szervezet egy másik rendszergazdája már kezeli az Azure-erőforrásokat a kiemelt identitáskezelés ben, megjelenik a jelenleg felügyelt erőforrások listája.

    ![Az erőforrások felfedezése ablaktábla, amely felsorolja a jelenleg kezelt erőforrásokat](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Válassza **az Erőforrások felderítése** lehetőséget a felderítési élmény elindításához.

    ![Felderítési ablaktábla, amely felsorolja a kezelhető erőforrásokat, például az előfizetéseket és a felügyeleti csoportokat](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. A **Felderítés** lapon használja az **Erőforrás állapotszűrőt** és **az Erőforrástípus kiválasztása parancsot** az írási engedéllyel bíró felügyeleti csoportok vagy előfizetések szűréséhez. Ez talán a legegyszerűbb kezdeni **minden** kezdetben.

    Csak a felügyeleti csoport vagy az előfizetési erőforrások at keresheti meg és választhatja ki a Kiemelt identitáskezelés használatával kezelhető felügyeleti csoport hoz. Ha egy felügyeleti csoportot vagy egy előfizetést kezel a Kiemelt identitáskezelés szolgáltatásban, kezelheti a gyermekerőforrásait is.

1. Jelölje be a kezelni kívánt nem felügyelt erőforrások melletti jelölőnégyzetet.

1. Válassza **az Erőforrás kezelése lehetőséget** a kijelölt erőforrások kezelésének megkezdéséhez.

    > [!NOTE]
    > Egy felügyeleti csoport vagy előfizetés kezelése után nem lehet nem felügyelt. Ez megakadályozza, hogy egy másik erőforrás-rendszergazda távolítsa el a kiemelt identitáskezelési beállításokat.

    ![Felderítési ablaktábla kijelölt erőforrással és kiemelt Erőforrás kezelése beállítással](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Ha megjelenik egy üzenet a kijelölt erőforrás felügyeletre való bevezetésének megerősítéséről, válassza az **Igen**lehetőséget.

    ![A kiválasztott erőforrások felügyeletre vonatkozó üzenet](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrásszerepkör-beállítások konfigurálása a Kiemelt identitáskezelés ben](pim-resource-roles-configure-role-settings.md)
- [Azure-erőforrás-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-resource-roles-assign-roles.md)
