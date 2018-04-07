---
title: Hogyan hajthat végre egy áttekintése a Privileged Identity Management, az Azure-erőforrások |} Microsoft Docs
description: Ez a dokumentum ismerteti, hogyan hajtsa végre, és tekintse át a PIM az Azure-erőforrások eléréséhez.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4afb923058143dd1771043db8433aa3a65541bf7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---perform-access-review"></a>Hajtsa végre a privileged Identity Management - szerepkör erőforrás - hozzáférési áttekintése
A privileged Identity Management az Azure-erőforrások egyszerűbbé teszi a hogyan kezelhetik a vállalatok számára az erőforrások az Azure-ban privilegizált hozzáférést. 

Ha egy rendszergazdai szerepkör van rendelve, a szervezete kiemelt szerepkörű rendszergazda megkérheti, hogy rendszeresen győződjön meg arról, hogy továbbra is szerepkörre van szüksége, hogy a feladat. Előfordulhat, hogy kap egy e-mailt, amely tartalmaz egy hivatkozást, vagy elvégezheti a rögtön a [Azure-portálon](https://portal.azure.com). Kövesse a cikkben egy önálló tekintse át a hozzárendelt szerepkörök végrehajtásához.

Ha egy kiemelt szerepkörű rendszergazda hozzáférést értékelést iránt érdeklődik, ezzel további adatokat, [egy hozzáférési felülvizsgálat indítása](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
Az Azure AD Privileged Identity Management (PIM) alkalmazást is használhatja a [Azure-portálon](https://portal.azure.com/) a felülvizsgálat végrehajtása.  Ha az Azure AD Privileged Identity Management alkalmazás nem rendelkezik a portálon, kövesse az alábbi lépéseket a kezdéshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Jelölje be az Azure-portálon, és válassza ki a könyvtárat, ahol meg fog jobb felső sarokban a felhasználónevére működik.
3. Válassza a **Minden szolgáltatás** lehetőséget, és a Szűrő szövegmezővel keresse meg az **Azure AD Privileged Identity Management** elemet.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

## <a name="approve-or-deny-access"></a>Hagyja jóvá vagy nem engedélyezik a hozzáférést
Hagyja jóvá vagy nem engedélyezik a hozzáférést, akkor még csak szólítja fel a felülvizsgáló e továbbra is használhatja ezt a szerepkört vagy nem. Válasszon **jóváhagyás** Ha azt szeretné, hogy a szerepkör belül vagy **Megtagadás** Ha már nincs szüksége a hozzáférés. Az állapota nem azonnal, megváltoztatni, amíg meg nem a felülvizsgáló vonatkozik az eredményeket.
Kövesse az alábbi lépéseket található, majd fejezze be a áttekintése:
1. Nyissa meg az Azure AD Privileged Identity Management alkalmazás.
2. Válassza ki a felülvizsgálati hozzáférési panel

![](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Válassza ki a végrehajtani kívánt áttekintése. 
4. Válasszon **jóváhagyása** vagy **megtagadása**. Szükség lehet a döntés okát közé tartozik a **okot** szövegmezőben.

![](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
