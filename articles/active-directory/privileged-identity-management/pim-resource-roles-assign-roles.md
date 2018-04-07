---
title: Privileged Identity Management az Azure-erőforrások - hozzárendelése szerepkörökhöz |} Microsoft Docs
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
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Privileged Identity Management - szerepkör erőforrás - hozzárendelése

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Egy felhasználó vagy csoport hozzárendelése egy szerepkörhöz, válassza ki a szerepkör (Ha a szerepkörök megtekintése) 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

vagy kattintson a Hozzáadás gombra az műveletsávon (Ha a tagok megtekintése).

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Ha egy felhasználó vagy csoport hozzáadása a tagok lapon, akkor kell: 

1. Kattintson a Hozzáadás menüjének szerepkör, felhasználó vagy csoport kiválasztása előtt.

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Válasszon egy felhasználót vagy csoportot a könyvtárból.

3. Válassza ki a megfelelő hozzárendelési típusát a legördülő menüből. 

    - **Csak az idő-hozzárendelési:** azt a felhasználó vagy csoport tagjai támogatható, de nem állandó hozzáférést biztosít a szerepkör egy megadott ideig idő vagy korlátlan ideig (Ha be van állítva, a szerepkör beállításai). 
    - **Közvetlen hozzárendelés:** nem követeli meg a felhasználó vagy csoport tagjai (más néven állandó hozzáférés) szerepkör-hozzárendelés aktiválásához. A Microsoft azt javasolja, hogy közvetlen hozzárendelés használatával elmozdul a telefonos vagy bizalmas tevékenységek, például a rövid távú használatra, ahol hozzáférés nem lesz szükség a feladat befejezésekor.

A hozzárendelés típusa legördülő lista alatt egy jelölőnégyzet teszi adja meg, ha a hozzárendelés állandó kell lennie (csak az idő hozzárendelés/véglegesen aktív aktiválásához közvetlen hozzárendelés véglegesen jogosult).

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>Lehet, hogy a jelölőnégyzet unmodifiable, ha egy másik rendszergazda adta meg a maximális hozzárendelés időtartama, az egyes hozzárendelés szerepkör beállításai.

 Adjon meg egy adott hozzárendelés időtartama, törölje a jelölőnégyzet és módosítása, illetve befejező dátum-és.

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Szerepkör-hozzárendelések kezelése

Rendszergazdák kezelhetik a szerepkör-hozzárendelések szerepkörök vagy a tagot a bal oldali navigációs való kiválasztással. Szerepkörök kiválasztásával lehetővé teszi, hogy a rendszergazdák számára, hogy a fájlkezelési feladatokat egy adott szerepkör hatóköre, amíg tagok megjeleníti az összes felhasználó- és szerepkör-hozzárendelések az erőforrás.

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Ha van függőben lévő aktiválási szerepkör, értesítésszalagról jelenik meg a lap tetején tagsági megtekintésekor.


## <a name="modify-existing-assignments"></a>Módosítsa a meglévő hozzárendelést

A felhasználó vagy csoport részletes nézete a meglévő hozzárendelést módosításához válassza a beállítások módosítása az műveletsávon az oldal tetején. Módosítsa a hozzárendelés-típus csak az idő-hozzárendelés vagy rendelését.

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
