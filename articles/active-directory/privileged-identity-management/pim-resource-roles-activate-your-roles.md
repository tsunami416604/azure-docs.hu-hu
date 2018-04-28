---
title: Az Azure-erőforrások szerepkörök aktiválása Privileged Identity Management használatával |} Microsoft Docs
description: A PIM szerepkörök aktiválása ismerteti.
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
ms.openlocfilehash: a985e67cc566cc45b3ee6b8dc98e91a8f34abd1b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Az Azure-erőforrások szerepkörök aktiválása Privileged Identity Management használatával
Privileged Identity Management (PIM) egy új Azure-erőforrások szerepkörök aktiválása élményt vezet be. Jogosult szerepkör tagjai aktiválási egy későbbi dátumra és időre is ütemezheti. A maximális (a rendszergazdák által konfigurált) belül egy adott aktiválás időtartama is választhatja. További információkért lásd: [aktiválása vagy inaktiválása szerepkörök az Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Szerepkörök aktiválása
Keresse meg a **a szerepkörök** szakasz a bal oldali ablaktáblán. Válassza ki **aktiválás** az aktiválni kívánt szerepkör esetében.

![Lapján "Jogosult szerepkörök" a "Saját szerepkörök" panelen.](media/azure-pim-resource-rbac/rbac-roles.png)

Az a **aktiválások** menüben adja meg a kezdő dátum és idő a szerepkör aktiválásához. Szükség esetén csökkentse az aktiválás időtartama (időtartama, amely a szerepkör aktív) és indoklásának beírásához, ha szükséges. Ezt követően válassza **aktiválás**.

A kezdő dátum és idő nem módosulnak, ha a szerepkör aktiválásakor másodpercben. Az a **a szerepkörök** ablaktáblán Szalagcím megjelenik egy üzenet, hogy a szerepkör aktiválása várakozik. Válassza ki a frissítés gombra kattintva törölje ezt az üzenetet.

!["A szerepkörök" ablaktáblán egy szalagcím üzenet és a függőben lévő jóváhagyási szóló értesítés](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Ha az aktiválás van ütemezve egy jövőbeli dátumot és időpontot, a függőben lévő kérelem jelenik meg a **függőben lévő kérelmek** lapján a bal oldali ablaktáblán. A szerepkör aktiválása már nincs szükség, ha a kérelem kiválasztásával megszakíthatja a **Mégse** gombra.

![Függőben lévő kérelmek "Mégse" gombot listája](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Csak elég felügyeleti gyakorlatot

Az erőforrás szerepkör-hozzárendelések csak elég adminisztrációs (JEA) gyakorlati tanácsok a PIM az Azure-erőforrások egyszerű érték használata. Felhasználók és az Azure-előfizetések vagy erőforrás-csoportok a csoport tagjai aktiválhatják a meglévő szerepkör-hozzárendelés csökkentett hatókörben. 

A keresési oldaláról, amelyeknek kezelniük kell alárendelt erőforrás található.

![Egy erőforrás kiválasztása](media/azure-pim-resource-rbac/azure-resources-02.png)

Válassza ki **a szerepkörök** a bal oldali ablaktáblán, és válassza ki a megfelelő szerepkört aktiválja. A hozzárendelés típusa **örökölt** a szerepkört rendelték, az előfizetés, nem pedig az erőforráscsoport, mert.

![Jogosult szerepkör-hozzárendelések, a hozzárendelés típusa, a kijelölt listája](media/azure-pim-resource-rbac/my-roles-02.png)
