---
title: Az Azure-erőforrások szerepkörök hozzárendelése a Privileged Identity Management használatával |} A Microsoft Docs
description: Ismerteti, hogyan rendelhet szerepköröket a PIM.
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
ms.openlocfilehash: 52320fe160fc1df2997dd419b406fce24f43b786
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622976"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Az Azure-erőforrások szerepkörök hozzárendelése a Privileged Identity Management használatával

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Hozzárendelése egy felhasználóhoz vagy csoporthoz egy szerepkör megtekintése közben a **szerepkörök** ablaktáblán válassza ki a szerepkört, és válassza **felhasználó hozzáadása**. 

![A "Felhasználó hozzáadása" gombra kattintva a "Szerepkör" panelen](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

Lehetőség kiválasztásával **felhasználó hozzáadása** származó a **tagok** ablaktáblán.

![A "Felhasználó hozzáadása" gombra kattintva a "Tag" panel](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Ha a felhasználó adja hozzá, vagy a csoport a **tagok** panelen kell: 

1. A munkakör kiválasztása a **Szerepkörválasztás** ablaktáblán egy felhasználó vagy csoport kiválasztása előtt.

   !["Szerepkör kiválasztása" panelen](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Válassza ki egy felhasználót vagy csoportot a címtárból.

3. A legördülő menüből válassza ki a megfelelő hozzárendelés típusa: 

   - **Szerinti**: a felhasználó vagy csoport tagjai a megadott időszakra vagy határozatlan időre szerepkör jogosult, de nem állandó hozzáférést biztosít (Ha be van állítva, a szerepkör-beállítások). 
   - **Közvetlen**: nincs szükség a felhasználó vagy csoport tagjai (más néven az állandó hozzáférés) szerepkör-hozzárendelés aktiválását. Ha hozzáférés nem lesz szükség a feladat befejezésekor rövid távú használatra közvetlen hozzárendelés használatát javasoljuk. Példák a készenléti műszakok és időérzékeny tevékenységeket.

4. Ha a hozzárendelés kell lennie állandó (just-in-time-hozzárendelés állandó jogosultság, vagy egy közvetlen hozzárendelés állandóan aktív), jelölje be az alábbi jelölőnégyzetet a **hozzárendelés-típus** mezőbe.

   ![A "Hozzárendelés-típus" és a kapcsolódó jelölőnégyzeteket "Tagsági beállítások" panelen](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >Előfordulhat, hogy a jelölőnégyzet unmodifiable, ha egy másik rendszergazda adta meg a hozzárendelés maximális időtartam minden hozzárendelés-típus a szerepkör-beállítások a.

   Adjon meg egy adott hozzárendelés időtartama, törölje a jelet a jelölőnégyzetből, és a kezdési és/vagy a záró dátum és idő mezők módosításához.

   !["Tagsági beállítások" panelen szövegbeviteli kezdő dátum, kezdési idő, befejezési dátuma és befejezési időpontja](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Szerepkör-hozzárendelések kezelése

Rendszergazdák kezelhetik a szerepkör-hozzárendelések kiválasztásával vagy **szerepkörök** vagy **tagok** a bal oldali ablaktáblán. Kiválasztásával **szerepkörök** lehetővé teszi a rendszergazdák a felügyeleti feladatok egy adott szerepkör hatóköre. Kiválasztásával **tagok** jeleníti meg az összes felhasználó- és szerepkör-hozzárendelések az erőforráshoz.

!["Szerepkör" panelen](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

!["Tag" panel](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Ha egy függőben lévő aktiválási szerepkör, értesítésszalagról jelenik meg a panel tetején lévő tagság látja.


## <a name="modify-existing-assignments"></a>Meglévő hozzárendelések módosítása

Válassza ki a felhasználó/csoport részletes nézete a meglévő hozzárendelések módosítása esetén **beállításainak módosítása** a művelet sávon. Módosítsa a hozzárendelési típusát **igény szerinti** vagy **közvetlen**.

![A "Beállítások módosítása" gombra kattintva a "Felhasználói details" panel](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
