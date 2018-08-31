---
title: A PIM saját Azure-erőforrás szerepkörök aktiválása |} A Microsoft Docs
description: Ismerje meg, hogyan lehet aktiválni az Azure-erőforrások szerepköreihez az Azure AD Privileged Identity Management (PIM).
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
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 234c1d71f0ec17d15a4dd589e3db92fd9bf68df2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189489"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>A PIM saját Azure-erőforrás szerepkörök aktiválása
Privileged Identity Management (PIM) az Azure-erőforrások szerepkörök aktiválása az új felhasználói élményt vezet be. Jogosult szerepkör tagjai egy jövőbeli dátumot és időpontot az aktiválási ütemezheti. Ezek is kiválaszthatja, hogy egy adott aktiválási időtartamát, a maximális (a rendszergazdák által konfigurált) belül. További információkért lásd: [aktiválása vagy inaktiválása az Azure AD Privileged Identity Management szerepkörök](pim-how-to-activate-role.md).

## <a name="activate-a-role"></a>A szerepkör aktiválása
Keresse meg a **saját szerepkörök** szakaszban a bal oldali panelen. Válassza ki **aktiválás** az aktiválni kívánt szerepkör esetében.

!["Jogosult szerepkörök" lapon a "Saját szerepkör" panelen.](media/azure-pim-resource-rbac/rbac-roles.png)

Az a **aktiválások** menüben adja meg a kezdő dátum és idő a szerepkör aktiválását. Szükség esetén csökkentse az aktiválási időtartamát (mennyi ideig, amely a szerepkör aktív) és a egy indoklás megadása, ha szükséges. Ezután válassza ki **aktiválás**.

Ha a kezdő dátum és idő nem módosulnak, a szerepkör aktiválása másodpercek alatt. Az a **saját szerepkörök** ablaktáblán Szalagcím megjelenik egy üzenet, hogy a szerepkör aktiválási várakozik. Válassza ki a frissítés gombra kattintva törölje ezt az üzenetet.

!["Saját szerepkörök" panelen szalagcímüzenet és a egy függőben lévő jóváhagyási szóló értesítés küldése](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Az aktiválás egy jövőbeli dátum és idő van ütemezve, akkor a függőben lévő kérelem jelenik meg a **függőben lévő kérések** lapjának bal oldali ablaktáblán. Ha már nem szükséges a szerepkör-aktiválásra, a kérelem megszakíthatja kiválasztásával a **Mégse** gombra.

![Függőben lévő kérelmek "Mégse" gombot listáját](media/azure-pim-resource-rbac/rbac-activate-pending.png)

## <a name="use-a-role-immediately-after-activation"></a>Olyan szerepkört használjon az aktiválás után azonnal

Gyorsítótárazás miatt aktiválások nem történik meg azonnal a frissítés nélkül az Azure Portalon. Ha az késleltetések lehetőségét csökkentheti a szerepkör aktiválása után van szüksége, használhatja a **alkalmazás-hozzáférés** lapot a portálon. Ezen a lapon elérhető alkalmazások azonnal ellenőrizze az új szerepkör-hozzárendeléseket.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **alkalmazás-hozzáférés** lapot.

    ![A PIM-alkalmazás-hozzáférés – képernyőkép](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Kattintson a **Azure-erőforrások** , nyissa meg újra a portálon a a **összes erőforrás** lapot.

    Ha erre a hivatkozásra kattint, frissítésének kényszerítése, és a egy új Azure-erőforrás szerepkör-hozzárendelések keresése van.

## <a name="apply-just-enough-administration-practices"></a>Just Enough Administration eljárások alkalmazása

Ajánlott eljárások csak Enough Administration (JEA) használata az erőforrás szerepkör-hozzárendelések használata egyszerű a PIM Használatát az Azure-erőforrásokhoz. Felhasználók és az Azure-előfizetések vagy erőforráscsoportok hozzárendelésekkel csoporttagok aktiválhatja a meglévő szerepkör-hozzárendelés egy csökkentett hatókörben. 

A keresés lapon keresse meg az alárendelt erőforrás kezeléséhez szükséges.

![Erőforrás kiválasztása](media/azure-pim-resource-rbac/azure-resources-02.png)

Válassza ki **saját szerepkörök** a bal oldali ablaktáblán, és válassza ki a megfelelő szerepkört aktiválja. A hozzárendelés típusa **örökölt** , mert a szerepkör lett hozzárendelve, az előfizetés nem található az erőforráscsoport.

![Jogosult szerepkör-hozzárendelések a hozzárendelés-típus kiemelt listája](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD-címtárbeli szerepkörök aktiválása](pim-how-to-activate-role.md)