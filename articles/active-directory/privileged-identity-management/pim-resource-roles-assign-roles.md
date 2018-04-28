---
title: Az Azure-erőforrások szerepkörök hozzárendelése a Privileged Identity Management használatával |} Microsoft Docs
description: Ismerteti, hogyan rendelhet szerepköröket a PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 501f063992d2f5c7769a5c9059b346aa2b5c2bb4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Az Azure-erőforrások szerepkörök hozzárendelése a Privileged Identity Management használatával

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Hozzárendelése egy felhasználóhoz vagy csoporthoz egy szerepkört a Megtekintés a **szerepkörök** ablaktáblán jelölje ki a szerepkört, és válassza ki **felhasználó hozzáadása**. 

!["-Szerepkörök ablaktáblában a"Felhasználó hozzáadása"gombra](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

Igény szerint kiválaszthatja **felhasználó hozzáadása** a a **tagok** ablaktáblán.

![A "Felhasználó hozzáadása" gombra a "Tagok" ablaktábla](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Ha a felhasználó adja hozzá, vagy a csoport a **tagok** ablaktáblán kell: 

1. Válassza ki a szerepkör eltávolítása a **Szerepkörválasztás** ablaktáblán egy felhasználó vagy csoport kiválasztása előtt.

   !["Szerepkör kiválasztása" ablak](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Válasszon egy felhasználót vagy csoportot a könyvtárból.

3. A legördülő menüből válassza ki a megfelelő hozzárendelés-típus: 

   - **JIT**: a felhasználó vagy csoport tagjai a megadott idő vagy korlátlan ideig szerepkör támogatható, de nem állandó hozzáférést biztosít (Ha be van állítva, a szerepkör beállításai). 
   - **Közvetlen**: nincs szükség a felhasználó vagy csoport tagjai (más néven állandó hozzáférés) szerepkör-hozzárendelés aktiválásához. Ha hozzáférés nem lesz szükség a feladat befejezésekor rövid távú használatra rendelését használatát javasoljuk. Többek között a készenléti elmozdul és időérzékeny tevékenységeket.

4. Ha a hozzárendelés kell állandó (just-in-time-hozzárendelés véglegesen jogosult, vagy véglegesen aktív közvetlen hozzárendelés), jelölje be a jelölőnégyzetet a **hozzárendelés-típus** mezőbe.

   ![A "Hozzárendelés-típus" és a kapcsolódó jelölőnégyzeteket "Tagsági beállítások" ablaktábla](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >Előfordulhat, hogy a jelölőnégyzet unmodifiable, ha egy másik rendszergazda adta meg a maximális hozzárendelés időtartama, az egyes hozzárendelés szerepkör beállításai.

   Adjon meg egy adott hozzárendelés időtartama, törölje a jelet a jelölőnégyzetből, és módosítsa a kezdő vagy záró dátum és idő mezőkbe.

   !["Tagsági beállítások" ablakban a kezdő dátum, a kezdő időpont, a befejezési dátum és a befejezési időpontja](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Szerepkör-hozzárendelések kezelése

Rendszergazdák kezelhetik a szerepkör-hozzárendelések kiválasztásával vagy **szerepkörök** vagy **tagok** a bal oldali ablaktáblán. Kiválasztása **szerepkörök** lehetővé teszi a rendszergazdák számára, hogy a felügyeleti feladatokat egy adott szerepkör hatóköre. Kiválasztása **tagok** jeleníti meg az összes felhasználó- és szerepkör-hozzárendelések az erőforráshoz.

!["-Szerepkörök panelen](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

!["Tagok" ablak](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Ha van függőben lévő aktiválási szerepkör, értesítésszalagról jelenik meg, ha a panel tetején tagsági megtekintése közben.


## <a name="modify-existing-assignments"></a>Módosítsa a meglévő hozzárendelést

A felhasználó vagy csoport részletes nézete a meglévő hozzárendelést módosításához válassza **beállításainak módosítása** az műveletsávon. A hozzárendelés-típus a **időben csak** vagy **közvetlen**.

!["Felhasználó" ablaktáblában a "Beállítások módosítása" gombra](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
