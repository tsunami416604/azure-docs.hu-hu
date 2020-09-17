---
title: Új hozzáférési csomag létrehozása a jogosultságok kezelésében – Azure AD
description: Megtudhatja, hogyan hozhat létre olyan új erőforrás-hozzáférési csomagot, amelyet meg szeretne osztani a Azure Active Directory jogosultságok kezelésében.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0126c5348a2acaebea0400a94ca0a1d14a1bf6ff
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707543"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Új hozzáférési csomag létrehozása az Azure AD-jogosultságok kezelésében

Egy hozzáférési csomag lehetővé teszi az erőforrások és házirendek egyszeri beállítását, amely automatikusan felügyeli a hozzáférést a hozzáférési csomag élettartamához. Ez a cikk azt ismerteti, hogyan hozható létre új hozzáférési csomag.

## <a name="overview"></a>Áttekintés

Minden hozzáférési csomagot egy katalógus nevű tárolóba kell helyezni. A katalógus meghatározza, hogy milyen erőforrásokat adhat hozzá a hozzáférési csomaghoz. Ha nem ad meg katalógust, a rendszer a hozzáférési csomagot az általános katalógusba helyezi. Jelenleg nem helyezhető át egy meglévő hozzáférési csomag egy másik katalógusba.

Ha Ön egy Access Package Manager, nem adhat hozzá saját erőforrásokat a katalógushoz. A katalógusban elérhető erőforrások használatára korlátozódik. Ha erőforrásokat kell hozzáadnia egy katalógushoz, megkérheti a katalógus tulajdonosát.

Minden hozzáférési csomagnak rendelkeznie kell legalább egy házirenddel. A szabályzatok határozzák meg, hogy kik igényelhetik a hozzáférési csomagot, valamint a jóváhagyási és életciklus-beállításokat is. Új hozzáférési csomag létrehozásakor létrehozhat egy kezdeti szabályzatot a címtárban lévő felhasználók számára, a címtárban nem szereplő felhasználók számára, csak a rendszergazdai közvetlen hozzárendelésekhez, vagy később is létrehozhatja a szabályzatot.

![Hozzáférési csomag létrehozása](./media/entitlement-management-access-package-create/access-package-create.png)

Az alábbi lépésekkel hozhat létre új hozzáférési csomagot.

1. Az identitás irányítása területen indítsa el a folyamatot egy új hozzáférési csomag létrehozásához.

1. Válassza ki azt a katalógust, amelyben a hozzáférési csomagot létre szeretné hozni.

1. Erőforrások hozzáadása a katalógusból a hozzáférési csomagba.

1. Rendeljen erőforrás-szerepköröket az egyes erőforrásokhoz.

1. Itt adhatja meg, hogy mely felhasználók igényelhetnek hozzáférést.

1. Határozza meg a jóváhagyási beállításokat.

1. Az életciklus beállításainak megadása.

## <a name="start-new-access-package"></a>Új hozzáférési csomag elindítása

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Kattintson a **Azure Active Directory** , majd az **identitás-szabályozás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok**elemre.

1. Kattintson az **új hozzáférési csomag**elemre.
   
    ![Jogosultságok kezelése a Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Alapvető beállítások

Az **alapvető beállítások** lapon adja meg a hozzáférési csomag nevét, és adja meg, hogy melyik katalógusban hozza létre a hozzáférési csomagot a alkalmazásban.

1. Adja meg a hozzáférési csomag megjelenítendő nevét és leírását. A felhasználók ezeket az adatokat fogják látni, amikor kérelmet küldenek a hozzáférési csomaghoz.

1. A **katalógus** legördülő listában válassza ki azt a katalógust, amelyben a hozzáférési csomagot létre szeretné hozni. Előfordulhat például, hogy rendelkezik egy katalógus tulajdonosával, amely az összes igényelhető marketing-erőforrást kezeli. Ebben az esetben kiválaszthatja a marketing katalógust.

    Csak azok a katalógusok jelennek meg, amelyekkel hozzáférési csomagokat hozhat létre a alkalmazásban. Ahhoz, hogy egy meglévő katalógusban hozzon létre egy hozzáférési csomagot, globális rendszergazdának vagy felhasználói rendszergazdának kell lennie, vagy a katalógus tulajdonosának vagy a Package Managernek kell lennie.

    ![Hozzáférési csomag – alapismeretek](./media/entitlement-management-access-package-create/basics.png)

    Ha Ön globális rendszergazda, felhasználói rendszergazda vagy katalógus létrehozója, és egy új katalógusban szeretné létrehozni a hozzáférési csomagot, akkor kattintson az **új katalógus létrehozása**lehetőségre. Adja meg a katalógus nevét és leírását, majd kattintson a **Létrehozás**gombra.

    A létrehozott hozzáférési csomag és a benne található összes erőforrás hozzá lesz adva az új katalógushoz. Később további katalógus-tulajdonosokat is hozzáadhat.

1. Kattintson a **Tovább** gombra.

## <a name="resource-roles"></a>Erőforrás-szerepkörök

Az **erőforrás-szerepkörök** lapon válassza ki a hozzáférési csomagban szerepeltetni kívánt erőforrásokat. A hozzáférési csomagot kérő és fogadó felhasználók megkapják a hozzáférési csomagban található összes erőforrás-szerepkört.

1. Kattintson a hozzáadni kívánt erőforrástípus (**csoportok**, munkacsoportok, **alkalmazások**vagy SharePoint- **webhelyek**) elemre.

1. A megjelenő Select (kiválasztás) ablaktáblán válasszon ki egy vagy több erőforrást a listából.

    ![Hozzáférési csomag – erőforrás-szerepkörök](./media/entitlement-management-access-package-create/resource-roles.png)

    Ha a hozzáférési csomagot az általános katalógusban vagy egy új katalógusban hozza létre, a saját címtárból is kiválaszthatja az összes erőforrást. Legalább egy globális rendszergazdának, egy felhasználói rendszergazdának vagy egy katalógus létrehozójának kell lennie.

    Ha egy meglévő katalógusban hozza létre a hozzáférési csomagot, kiválaszthatja, hogy a katalógusban lévő összes erőforrás tulajdonos nélkül legyen.

    Ha Ön globális rendszergazda, felhasználói rendszergazda vagy katalógus tulajdonosa, lehetősége van arra, hogy olyan erőforrásokat válasszon ki, amelyek még nem szerepelnek a katalógusban. Ha olyan erőforrásokat választ ki, amelyek jelenleg nem szerepelnek a kiválasztott katalógusban, akkor ezek az erőforrások is hozzáadódnak a katalógushoz más katalógus-rendszergazdák számára, hogy hozzáférési csomagokat hozzanak létre a használatával. Ha meg szeretné tekinteni a katalógusba felvehető összes erőforrást, jelölje be az **összes megtekintése** jelölőnégyzetet a kiválasztás ablaktábla tetején. Ha csak azokat az erőforrásokat szeretné kiválasztani, amelyek jelenleg a kiválasztott katalógusban vannak, hagyja bejelölve az **összes** jelölőnégyzetet (alapértelmezett állapot) jelölőnégyzetet.

1. Miután kiválasztotta az erőforrásokat, a **szerepkör** listából válassza ki azt a szerepkört, amelyhez hozzá szeretné rendelni a felhasználókat az erőforráshoz.

    ![Hozzáférési csomag – erőforrás-szerepkör kiválasztása](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Kattintson a **Tovább** gombra.

## <a name="requests"></a>Kérelmek

A **kérelmek** lapon létrehozhatja az első szabályzatot annak megadásához, hogy kik igényelhetik a hozzáférési csomagot és a jóváhagyási beállításokat. Később további kérési házirendeket is létrehozhat, amelyek lehetővé teszik további felhasználói csoportok számára, hogy a hozzáférési csomagot saját jóváhagyási beállításokkal kérjenek.

![Hozzáférési csomag – kérelmek lap](./media/entitlement-management-access-package-create/requests.png)

Attól függően, hogy ki szeretné használni ezt a hozzáférési csomagot, hajtsa végre az alábbi részekben ismertetett lépéseket.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Ellenőrzés és létrehozás

A **felülvizsgálat + létrehozás** lapon áttekintheti a beállításait, és ellenőrizheti az érvényesítési hibákat.

1. A hozzáférési csomag beállításainak áttekintése

    ![Hozzáférési csomag – házirend – házirend-beállítás engedélyezése](./media/entitlement-management-access-package-create/review-create.png)

1. A hozzáférési csomag létrehozásához kattintson a **Létrehozás** gombra.

    Az új hozzáférési csomag megjelenik a hozzáférési csomagok listájában.

## <a name="creating-an-access-package-programmatically"></a>Hozzáférési csomag programozott módon történő létrehozása

Microsoft Graph használatával is létrehozhat hozzáférési csomagokat.  Egy megfelelő szerepkörbe tartozó felhasználó, aki a delegált engedéllyel rendelkezik, `EntitlementManagement.ReadWrite.All` meghívhatja az API-t a következőre:

1. [Sorolja fel a katalógusban szereplő accessPackageResources](https://docs.microsoft.com/graph/api/accesspackagecatalog-list?view=graph-rest-beta&tabs=http) , és [hozzon létre egy accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&tabs=http) minden olyan erőforráshoz, amely még nem szerepel a katalógusban.
1. Egy accessPackageCatalog egyes accessPackageResource [AccessPackageResourceRoles listázása](https://docs.microsoft.com/graph/api/accesspackage-list-accesspackageresourcerolescopes?view=graph-rest-beta&tabs=http) . A szerepkörök listáját ezután egy szerepkör kiválasztására fogjuk használni, amikor a későbbiekben létrehoz egy accessPackageResourceRoleScope.
1. [Hozzon létre egy accessPackage](https://docs.microsoft.com/graph/tutorial-access-package-api?view=graph-rest-beta).
1. [Hozzon létre egy accessPackageAssignmentPolicy](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta&tabs=http).
1. [Hozzon létre egy accessPackageResourceRoleScope](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta&tabs=http) minden, a hozzáférési csomagban szükséges erőforrás-szerepkörhöz.

## <a name="next-steps"></a>További lépések

- [Hozzáférési csomag igénylésére szolgáló hivatkozás megosztása](entitlement-management-access-package-settings.md)
- [Hozzáférési csomag erőforrás-szerepköreinek módosítása](entitlement-management-access-package-resources.md)
- [Felhasználó közvetlen kiosztása a hozzáférési csomaghoz](entitlement-management-access-package-assignments.md)