---
title: Új hozzáférési csomag létrehozása az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan hozhat létre egy új, az Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban megosztani kívánt erőforrás-hozzáférési csomagot.
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
ms.date: 09/24/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1283549ce78535757b7ba42eaf7054538f0c9784
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326337"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Új hozzáférési csomag létrehozása az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Egy hozzáférési csomag lehetővé teszi az erőforrások és házirendek egyszeri beállítását, amely automatikusan felügyeli a hozzáférést a hozzáférési csomag élettartamához. Ez a cikk azt ismerteti, hogyan hozható létre új hozzáférési csomag.

## <a name="overview"></a>Áttekintés

Minden hozzáférési csomagot egy katalógus nevű tárolóba kell helyezni. A katalógus meghatározza, hogy milyen erőforrásokat adhat hozzá a hozzáférési csomaghoz. Ha nem ad meg katalógust, a rendszer a hozzáférési csomagot az általános katalógusba helyezi. Jelenleg nem helyezhető át egy meglévő hozzáférési csomag egy másik katalógusba.

Minden hozzáférési csomagnak rendelkeznie kell legalább egy házirenddel. A szabályzatok határozzák meg, hogy kik igényelhetik a hozzáférési csomagot, valamint a jóváhagyás és a lejárat beállításait is. Új hozzáférési csomag létrehozásakor létrehozhat egy kezdeti szabályzatot a címtárban lévő felhasználók számára, a címtárban nem szereplő felhasználók számára, csak a rendszergazdai közvetlen hozzárendelésekhez, vagy később is létrehozhatja a szabályzatot.

Az alábbi ábrán egy új hozzáférési csomag létrehozásának magas szintű folyamata látható.

![Hozzáférési csomag létrehozásának folyamata](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Új hozzáférési csomag elindítása

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy katalógus tulajdonosa

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Kattintson a **Azure Active Directory** , majd az **identitás-szabályozás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok**elemre.

    ![Jogosultságok kezelése a Azure Portal](./media/entitlement-management-shared/elm-access-packages.png)

1. Kattintson az **új hozzáférési csomag**elemre.

## <a name="basics"></a>Alapadatok

Az **alapvető beállítások** lapon adja meg a hozzáférési csomag nevét, és adja meg, hogy melyik katalógusban hozza létre a hozzáférési csomagot a alkalmazásban.

1. Adja meg a hozzáférési csomag megjelenítendő nevét és leírását. A felhasználók ezeket az adatokat fogják látni, amikor kérelmet küldenek a hozzáférési csomaghoz.

1. A **katalógus** legördülő listában válassza ki azt a katalógust, amelyben a hozzáférési csomagot létre szeretné hozni. Előfordulhat például, hogy rendelkezik egy katalógus tulajdonosával, amely az összes igényelhető marketing-erőforrást kezeli. Ebben az esetben kiválaszthatja a marketing katalógust.

    Csak azok a katalógusok jelennek meg, amelyekkel hozzáférési csomagokat hozhat létre a alkalmazásban. Egy meglévő katalógusban lévő hozzáférési csomag létrehozásához legalább egy globális rendszergazdának, egy felhasználói rendszergazdának, a katalógus tulajdonosának kell lennie, vagy az adott katalógusban hozzá kell férnie a Package Managerhez.

    ![Hozzáférési csomag – alapismeretek](./media/entitlement-management-access-package-create/basics.png)

    Ha Ön globális rendszergazda vagy felhasználói rendszergazda, és egy új katalógusban szeretné létrehozni a hozzáférési csomagot, kattintson az **új létrehozása**lehetőségre. Adja meg a katalógus nevét és leírását, majd kattintson a **Létrehozás**gombra.

    A létrehozott hozzáférési csomag és a benne található összes erőforrás hozzá lesz adva az új katalógushoz. Később további katalógus-tulajdonosokat is hozzáadhat.


1. Kattintson a **Tovább** gombra.

## <a name="resource-roles"></a>Erőforrás-szerepkörök

Az **erőforrás-szerepkörök** lapon válassza ki a hozzáférési csomagban szerepeltetni kívánt erőforrásokat.  A hozzáférési csomagot kérő és fogadó felhasználók megkapják a hozzáférési csomagban található összes erőforrás-szerepkört.

1. Kattintson a hozzáadni kívánt erőforrástípus (**csoportok**, munkacsoportok, **alkalmazások**vagy SharePoint- **webhelyek**) elemre.

1. A megjelenő Select (kiválasztás) ablaktáblán válasszon ki egy vagy több erőforrást a listából.

    ![Hozzáférési csomag – erőforrás-szerepkörök](./media/entitlement-management-access-package-create/resource-roles.png)

    Ha a hozzáférési csomagot az általános katalógusban vagy egy új katalógusban hozza létre, a saját címtárból is kiválaszthatja az összes erőforrást. Legalább egy globális rendszergazdának, egy felhasználói rendszergazdának vagy katalógus létrehozójának kell lennie.

    Ha egy meglévő katalógusban hozza létre a hozzáférési csomagot, kiválaszthatja, hogy a katalógusban lévő összes erőforrás tulajdonos nélkül legyen.

    Ha Ön globális rendszergazda, a felhasználó rendszergazdája vagy a katalógus tulajdonosa, lehetősége van arra, hogy kiválassza a katalógusban még nem használt erőforrásokat. Ha olyan erőforrásokat választ ki, amelyek jelenleg nem szerepelnek a kiválasztott katalógusban, akkor ezek az erőforrások is hozzáadódnak a katalógushoz más katalógus-rendszergazdák számára, hogy hozzáférési csomagokat hozzanak létre a használatával. Ha csak azokat az erőforrásokat szeretné kiválasztani, amelyek jelenleg a kiválasztott katalógusban vannak, jelölje be a csak a Pan (pásztázás) jelölőnégyzet felső részén található **Megtekintés** jelölőnégyzetet.

1. Miután kiválasztotta az erőforrásokat, a **szerepkör** listából válassza ki azt a szerepkört, amelyhez hozzá szeretné rendelni a felhasználókat az erőforráshoz.

    ![Hozzáférési csomag – erőforrás-szerepkör kiválasztása](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Kattintson a **Tovább** gombra.

## <a name="policy"></a>Szabályzat

A **házirend** lapon létrehozhatja az első szabályzatot annak megadásához, hogy ki kérheti a hozzáférési csomagot, valamint a jóváhagyás és a lejárat beállításait is. Később további házirendeket is létrehozhat, amelyek lehetővé teszik a további felhasználói csoportok számára a hozzáférési csomag kérését a saját jóváhagyási és lejárati beállításaival. A szabályzatot később is létrehozhatja.

1. Állítsa az **első házirend létrehozása** váltógomb **most** vagy **újabb**értékre.

    ![Hozzáférési csomag – szabályzat](./media/entitlement-management-access-package-create/policy.png)

1. Ha **később**választja, ugorjon a [felülvizsgálat + létrehozás](#review--create) szakaszra a hozzáférési csomag létrehozásához.

1. Ha a **most**lehetőséget választja, hajtsa végre a következő szabályzatok egyikének lépéseit.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Felülvizsgálat + létrehozás

A **felülvizsgálat + létrehozás** lapon áttekintheti a beállításait, és ellenőrizheti az érvényesítési hibákat.

1. A hozzáférési csomag beállításainak áttekintése

    ![Hozzáférési csomag – házirend – házirend-beállítás engedélyezése](./media/entitlement-management-access-package-create/review-create.png)

1. A hozzáférési csomag létrehozásához kattintson a **Létrehozás** gombra.

    Az új hozzáférési csomag megjelenik a hozzáférési csomagok listájában.

## <a name="next-steps"></a>További lépések

- [Meglévő hozzáférési csomag szerkesztése és kezelése](entitlement-management-access-package-edit.md)
- [Katalógus tulajdonosának vagy egy Access Package Managernek a hozzáadása](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Katalógus létrehozása és kezelése](entitlement-management-catalog-create.md)
