---
title: Új hozzáférési csomag létrehozása a jogosultságkezelésben – Azure AD
description: Ismerje meg, hogyan hozhat létre új hozzáférési csomagot az Azure Active Directory jogosultságkezelésében megosztani kívánt erőforrásokból.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262008"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Új hozzáférési csomag létrehozása az Azure AD-jogosultságkezelésben

A hozzáférési csomag lehetővé teszi, hogy az erőforrások és szabályzatok egyszeri beállítását végezze el, amely automatikusan felügyeli a hozzáférést a hozzáférési csomag élettartama alatt. Ez a cikk bemutatja, hogyan hozhat létre új hozzáférési csomagot.

## <a name="overview"></a>Áttekintés

Minden hozzáférési csomagot katalógusnak nevezett tárolóba kell helyezni. A katalógus határozza meg, hogy milyen erőforrásokat adhat hozzá a hozzáférési csomaghoz. Ha nem ad meg katalógust, a hozzáférési csomag bekerül az Általános katalógusba. Jelenleg nem helyezhet át egy meglévő hozzáférési csomagot egy másik katalógusba.

Ha Ön hozzáférés-csomagkezelő, nem adhat hozzá erőforrásokat a katalógushoz. A katalógusban elérhető erőforrások használatára korlátozódik. Ha erőforrásokat kell hozzáadnia egy katalógushoz, megkérdezheti a katalógus tulajdonosát.

Minden hozzáférési csomagnak legalább egy házirenddel kell rendelkeznie. A házirendek határozzák meg, hogy ki kérheti a hozzáférési csomagot, valamint a jóváhagyási és életciklus-beállításokat. Új hozzáférési csomag létrehozásakor létrehozhat egy kezdeti házirendet a címtárban lévő felhasználók, a címtárban nem lévő felhasználók, a közvetlen rendszergazdai hozzárendelések számára, vagy dönthet úgy, hogy később hozza létre a házirendet.

![Hozzáférési csomag létrehozása](./media/entitlement-management-access-package-create/access-package-create.png)

Az alábbiakban az új hozzáférési csomag létrehozásának magas szintű lépéseit ismertetik.

1. Az identitás-irányítási, indítsa el a folyamatot egy új hozzáférési csomag létrehozásához.

1. Jelölje ki azt a katalógust, amelyben létre szeretné hozni a hozzáférési csomagot.

1. Erőforrások hozzáadása a katalógusból a hozzáférési csomaghoz.

1. Erőforrás-szerepkörök hozzárendelése az egyes erőforrásokhoz.

1. Adja meg azokat a felhasználókat, akik hozzáférést kérhetnek.

1. Adja meg a jóváhagyási beállításokat.

1. Adja meg az életciklus-beállításokat.

## <a name="start-new-access-package"></a>Új hozzáférési csomag indítása

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Kattintson **az Azure Active Directory,** majd **az Identitáscégcégcég**elemre.

1. A bal oldali menüben kattintson az **Access-csomagok parancsra.**

1. Kattintson **az Új hozzáférési csomag gombra.**
   
    ![Jogosultságkezelés az Azure Portalon](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Alapvető beállítások

Az **Alapok lapon** nevet ad a hozzáférési csomagnak, és megadja, hogy melyik katalógusban hozza létre a hozzáférési csomagot.

1. Adja meg a hozzáférési csomag megjelenítendő nevét és leírását. A felhasználók ezt az információt a hozzáférési csomagra vonatkozó kérelem benyújtásakor fogják látni.

1. A **Katalógus** legördülő listában jelölje ki azt a katalógust, amelyben létre szeretné hozni a hozzáférési csomagot. Előfordulhat például, hogy van egy katalógustulajdonosa, amely kezeli a kérhető összes marketingerőforrást. Ebben az esetben kiválaszthatja a marketingkatalógust.

    Csak azokat a katalógusokat fogja látni, amelyekhez hozzáférési csomagok létrehozására van engedélye. Ahhoz, hogy egy meglévő katalógusban hozzáférési csomagot hozzon létre, globális rendszergazdának vagy felhasználónak kell lennie, vagy katalógustulajdonosnak vagy hozzáférés-csomagkezelőnek kell lennie a katalógusban.

    ![Access csomag – alapok](./media/entitlement-management-access-package-create/basics.png)

    Ha Ön globális rendszergazda, felhasználó vagy katalóguskészítő, és a hozzáférési csomagot egy nem felsorolt új katalógusban szeretné létrehozni, kattintson **az Új katalógus létrehozása**gombra. Írja be a katalógus nevét és leírását, majd kattintson a **Létrehozás gombra.**

    A létrehozandó hozzáférési csomag és a benne lévő erőforrások hozzáadódnak az új katalógushoz. Később további katalógustulajdonosokat is hozzáadhat.

1. Kattintson a **Tovább** gombra.

## <a name="resource-roles"></a>Erőforrás-szerepkörök

Az **Erőforrás-szerepkörök** lapon kiválaszthatja a hozzáférési csomagba felvenni kívánt erőforrásokat. A hozzáférési csomagot kérő és fogadó felhasználók megkapják a hozzáférési csomag összes erőforrásszerepkörét.

1. Kattintson a hozzáadni kívánt erőforrástípusra (**Csoportok és csapatok,** **alkalmazások**vagy **SharePoint-webhelyek**).

1. A megjelenő Kijelölés ablaktáblán jelöljön ki egy vagy több erőforrást a listából.

    ![Access csomag – Erőforrás-szerepkörök](./media/entitlement-management-access-package-create/resource-roles.png)

    Ha az általános katalógusban vagy egy új katalógusban hozza létre a hozzáférési csomagot, akkor a saját könyvtárából bármelyik erőforrást kiválaszthatja. Legalább globális rendszergazdának, felhasználó rendszergazdának vagy katalóguskészítőnek kell lennie.

    Ha egy meglévő katalógusban hozza létre a hozzáférési csomagot, kiválaszthat minden olyan erőforrást, amely már szerepel a katalógusban anélkül, hogy rendelkezne vele.

    Ha Ön globális rendszergazda, felhasználó vagy katalógustulajdonos, akkor további lehetőség van olyan erőforrások kiválasztására, amelyek még nem szerepelnek a katalógusban. Ha olyan erőforrásokat választ, amelyek jelenleg nem szerepelnek a kijelölt katalógusban, ezek az erőforrások is hozzáadódnak a katalógushoz, hogy más katalógus-rendszergazdák hozzáférési csomagokat hoznak létre. Ha csak a kijelölt katalógusban lévő erőforrásokat szeretné kijelölni, jelölje be a **Csak** a Kijelölés ablaktábla tetején látható jelölőnégyzetet.

1. Miután kiválasztotta az erőforrásokat, a **Szerepkör** listában válassza ki azt a szerepkört, amelyhez a felhasználókat hozzá kívánja rendelni az erőforráshoz.

    ![Access csomag – Erőforrásszerepkör kiválasztása](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Kattintson a **Tovább** gombra.

## <a name="requests"></a>Kérelmek

A **Kérések** lapon hozza létre az első házirendet, amely meghatározza, hogy ki kérheti a hozzáférési csomagot, valamint a jóváhagyási beállításokat. Később további kérési házirendeket hozhat létre, hogy további felhasználói csoportok is kérhetik a hozzáférési csomagot a saját jóváhagyási beállításaikkal.

![Access csomag – Kérések lap](./media/entitlement-management-access-package-create/requests.png)

Attól függően, hogy ki szeretné kérni ezt a hozzáférési csomagot, hajtsa végre a következő szakaszok egyikében ismertetett lépéseket.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Véleményezés + létrehozás

A **Véleményezés + létrehozás** lapon áttekintheti a beállításokat, és ellenőrizheti az érvényesítési hibákat.

1. A hozzáférési csomag beállításainak áttekintése

    ![Access csomag – Házirend – Házirendbeállítás engedélyezése](./media/entitlement-management-access-package-create/review-create.png)

1. A hozzáférési csomag létrehozásához kattintson a **Létrehozás** gombra.

    Az új hozzáférési csomag megjelenik a hozzáférési csomagok listájában.

## <a name="creating-an-access-package-programmatically"></a>Hozzáférési csomag létrehozása programozott módon

Hozzáférési csomagot a Microsoft Graph segítségével is létrehozhatunk.  A delegált `EntitlementManagement.ReadWrite.All` engedéllyel rendelkező alkalmazással rendelkező alkalmazással rendelkező felhasználó megfelelő szerepkörben hívhatja meg az API-t

1. [Sorolja fel az accessPackageResources-t a katalógusban,](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta) és [hozzon létre egy accessPackageResourceRequest-et](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta) minden olyan erőforráshoz, amely még nem szerepel a katalógusban.
1. [Sorolja fel az accessPackageResourceRoles](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta) minden accessPackageResource egy accessPackageCatalog. Ezt a szerepkörlistát ezután egy szerepkör kiválasztásához használja, amikor ezt követően létrehoz egy accessPackageResourceRoleScope-ot.
1. [AccessPackage létrehozása](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta).
1. [Hozzon létre egy accessPackageAssignmentPolicy](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta).
1. [Hozzon létre egy accessPackageResourceRoleScope-ot](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta) az access csomagban szükséges minden erőforrásszerepkörhöz.

## <a name="next-steps"></a>További lépések

- [Hozzáférési csomag igényléséhez hivatkozás megosztása](entitlement-management-access-package-settings.md)
- [Hozzáférési csomag erőforrásszerepköreinek módosítása](entitlement-management-access-package-resources.md)
- [Felhasználó közvetlen hozzárendelése a hozzáférési csomaghoz](entitlement-management-access-package-assignments.md)
