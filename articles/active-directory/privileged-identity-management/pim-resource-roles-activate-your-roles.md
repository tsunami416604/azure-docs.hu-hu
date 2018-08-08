---
title: Az Azure-erőforrások szerepkörök aktiválása a Privileged Identity Management használatával |} A Microsoft Docs
description: Ismerteti, hogyan lehet a PIM-szerepkörök aktiválása.
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
ms.openlocfilehash: 45a2747a60df4d91c2fe1c5247e1d4ac82ff819f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617149"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Az Azure-erőforrások szerepkörök aktiválása a Privileged Identity Management használatával
Privileged Identity Management (PIM) az Azure-erőforrások szerepkörök aktiválása az új felhasználói élményt vezet be. Jogosult szerepkör tagjai egy jövőbeli dátumot és időpontot az aktiválási ütemezheti. Ezek is kiválaszthatja, hogy egy adott aktiválási időtartamát, a maximális (a rendszergazdák által konfigurált) belül. További információkért lásd: [aktiválása vagy inaktiválása az Azure AD Privileged Identity Management szerepkörök](pim-how-to-activate-role.md).

## <a name="activate-roles"></a>Szerepkörök aktiválása
Keresse meg a **saját szerepkörök** szakaszban a bal oldali panelen. Válassza ki **aktiválás** az aktiválni kívánt szerepkör esetében.

!["Jogosult szerepkörök" lapon a "Saját szerepkör" panelen.](media/azure-pim-resource-rbac/rbac-roles.png)

Az a **aktiválások** menüben adja meg a kezdő dátum és idő a szerepkör aktiválását. Szükség esetén csökkentse az aktiválási időtartamát (mennyi ideig, amely a szerepkör aktív) és a egy indoklás megadása, ha szükséges. Ezután válassza ki **aktiválás**.

Ha a kezdő dátum és idő nem módosulnak, a szerepkör aktiválása másodpercek alatt. Az a **saját szerepkörök** ablaktáblán Szalagcím megjelenik egy üzenet, hogy a szerepkör aktiválási várakozik. Válassza ki a frissítés gombra kattintva törölje ezt az üzenetet.

!["Saját szerepkörök" panelen szalagcímüzenet és a egy függőben lévő jóváhagyási szóló értesítés küldése](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Az aktiválás egy jövőbeli dátum és idő van ütemezve, akkor a függőben lévő kérelem jelenik meg a **függőben lévő kérések** lapjának bal oldali ablaktáblán. Ha már nem szükséges a szerepkör-aktiválásra, a kérelem megszakíthatja kiválasztásával a **Mégse** gombra.

![Függőben lévő kérelmek "Mégse" gombot listáját](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Just Enough Administration eljárások alkalmazása

Ajánlott eljárások csak Enough Administration (JEA) használata az erőforrás szerepkör-hozzárendelések használata egyszerű a PIM Használatát az Azure-erőforrásokhoz. Felhasználók és az Azure-előfizetések vagy erőforráscsoportok hozzárendelésekkel csoporttagok aktiválhatja a meglévő szerepkör-hozzárendelés egy csökkentett hatókörben. 

A keresés lapon keresse meg az alárendelt erőforrás kezeléséhez szükséges.

![Erőforrás kiválasztása](media/azure-pim-resource-rbac/azure-resources-02.png)

Válassza ki **saját szerepkörök** a bal oldali ablaktáblán, és válassza ki a megfelelő szerepkört aktiválja. A hozzárendelés típusa **örökölt** , mert a szerepkör lett hozzárendelve, az előfizetés nem található az erőforráscsoport.

![Jogosult szerepkör-hozzárendelések a hozzárendelés-típus kiemelt listája](media/azure-pim-resource-rbac/my-roles-02.png)
