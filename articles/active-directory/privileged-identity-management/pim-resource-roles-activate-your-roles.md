---
title: Privileged Identity Management az Azure-erőforrások - szerepkörök aktiválása |} Microsoft Docs
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
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Privileged Identity Management - szerepkör erőforrás - aktiválása
Szerepkörök aktiválása az Azure-erőforrások bevezet egy új felület, amely lehetővé teszi az aktiválási a jövőbeni dátum/idő ütemezése, és válasszon egy adott aktiválási időtartamot a legnagyobb (a rendszergazdák által konfigurált) belül jogosult szerepkör tagjai. További tudnivalók [itt az Azure AD-szerepkörök aktiválása](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Szerepkörök aktiválása
Keresse meg a a szerepkörök részben a bal oldali navigációs sávon. Kattintson az "Aktiválási" a szerepkörhöz történő aktiválni szeretné.
![](media/azure-pim-resource-rbac/rbac-roles.png)

Az aktiválások menüből adjon meg a kívánt kezdő dátum és idő, a szerepkör aktiválásához. Opcionálisan csökkentése az aktiválás időtartama (mennyi ideig a szerepköre aktív), és ha szükséges; indoklásának beírásához Kattintson az aktiválás gombra.

A kezdő dátum és idő nem módosul, ha a szerepkör aktiválódik másodpercen belül. Egy szerepkör aszinkron aktiválás szalagcím üzenet a saját szerepkörök lapon jelenik meg. Kattintson a frissítés gombra kattintva törölheti ezt az üzenetet.

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Ha az aktiválás dátuma jövőbeli időpontra van ütemezve, a függőben lévő kérelem függőben lévő kérelmek fülre, a bal oldali navigációs menü megjelenik. Abban az esetben, ha a szerepkör aktiválása már nincs szükség, a felhasználó megszakítja a kérést a lap jobb oldalán a Mégse gombra kattintva.

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Éppen elég felügyelettel

A éppen elegendő (JEA) felügyeleti gyakorlati tanácsok a erőforrás szerepkör-hozzárendelések érték használata egyszerű, Azure-erőforrások PIM. Felhasználók és az Azure-előfizetésekhez vagy erőforrás-csoportok a csoport tagjai aktiválhatják a meglévő szerepkör-hozzárendelés csökkentett hatókörben. 

A lapon, a kezeléséhez szükséges alárendelt erőforrás található.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Jelöljön ki a bal oldali navigációs menü a szerepkört, és válassza ki a megfelelő szerepkört aktiválja. Figyelje meg a hozzárendelés-típus örökölt, mert a szerepkör a lent látható módon az erőforráscsoportot, hanem az előfizetés kapott.

![](media/azure-pim-resource-rbac/my-roles-02.png)
