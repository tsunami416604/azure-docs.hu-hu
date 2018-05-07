---
title: Az Azure-erőforrások hajtsa végre egy áttekintése Privileged Identity Management |} Microsoft Docs
description: Ez a dokumentum ismerteti az Azure-erőforrások, hajtsa végre egy áttekintése PIM erőforrás szerepkör szerint.
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
ms.openlocfilehash: 47d981ce8034d725fe2b119392334a156852698d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="perform-an-access-review-in-pim-according-to-resource-role"></a>Hajtsa végre egy áttekintése PIM, erőforrás szerepkör alapján
Privileged Identity Management (PIM) az Azure-erőforrások egyszerűbbé teszi a hogyan kezelhetik a vállalatok számára az erőforrások az Azure-ban privilegizált hozzáférést. 

Ha egy rendszergazdai szerepkör van rendelve, a szervezet kiemelt szerepkörű rendszergazda fel, hogy rendszeresen győződjön meg arról, hogy továbbra is szerepkörre van szüksége, hogy a feladat. Előfordulhat, hogy kap egy e-mailt, amely tartalmaz egy hivatkozást, vagy elvégezheti a rögtön a [Azure-portálon](https://portal.azure.com). Kövesse a cikkben egy önálló tekintse át a hozzárendelt szerepkörök végrehajtásához.

Ha egy kiemelt szerepkörű rendszergazda hozzáférést értékelést iránt érdeklődik, ezzel további adatokat, [egy hozzáférési felülvizsgálat indítása](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
Az Azure Active Directory (Azure AD) PIM alkalmazást is használhatja a [Azure-portálon](https://portal.azure.com/) a felülvizsgálat végrehajtása. Ha az alkalmazás nem rendelkezik a portálon, kövesse az alábbi lépéseket a kezdéshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki a felhasználó neve az Azure portál jobb felső sarkában, és válassza ki a könyvtárat, ahol meg fog működik.
3. Válassza ki **minden szolgáltatás**, és használja a **szűrő** mező használatával keresheti meg *Azure AD Privileged Identity Management*.
4. Ellenőrizze **rögzítés az irányítópulton**, majd válassza ki **létrehozása**. A PIM alkalmazást nyitja meg.

## <a name="approve-or-deny-access"></a>Hagyja jóvá vagy nem engedélyezik a hozzáférést
Hagyja jóvá vagy nem engedélyezik a hozzáférést, akkor még csak szólítja fel a felülvizsgáló e továbbra is használhatja ezt a szerepkört vagy nem. Válasszon **jóváhagyás** Ha azt szeretné, hogy a szerepkör belül vagy **Megtagadás** Ha már nincs szüksége a hozzáférés. Az állapot változik, csak akkor, ha a felülvizsgáló vonatkozik az eredményeket.

Kövesse az alábbi lépéseket található, majd fejezze be a áttekintése:
1. Tallózással keresse meg az Azure AD PIM alkalmazást.
2. Válassza ki a **tekintse át a hozzáférési** panelen.

   ![Képernyőfelvétel a PIM alkalmazást, a kiválasztott felülvizsgálati hozzáférési panel](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Válassza ki a végrehajtani kívánt áttekintése. 
4. Válasszon **jóváhagyása** vagy **megtagadása**. Az a **adjon meg egy OK mezőben**, szükség lehet közé tartozik a döntési okát.

   ![Képernyőfelvétel a felülvizsgálati részleteit megjelenítő oldalra](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
