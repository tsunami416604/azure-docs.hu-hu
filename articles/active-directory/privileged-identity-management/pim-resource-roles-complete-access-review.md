---
title: Privileged Identity Management az Azure-erőforrások – az Azure-erőforrások teljes áttekintése |} Microsoft Docs
description: Ismerteti, hogyan lehet elvégezni egy áttekintése Azure-erőforrások.
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
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Privileged Identity Management - szerepkör erőforrás - Befejezés áttekintése
Kiemelt szerepkörű rendszergazda egyszer emelt szintű hozzáférés is tekintheti a [biztonsági felülvizsgálat elindult](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) az Azure-erőforrások automatikusan elküld egy e-mailt arra kéri a felhasználót a hozzáférésüket áttekintéséhez. Ha a felhasználó nem kapott e-mailt, elküldheti azokat az utasításokat [egy biztonsági felülvizsgálat végrehajtása](pim-resource-roles-perform-access-review.md).

Után a biztonsági felülvizsgálat időszak alatt, vagy a felhasználók végzett, az önálló tekintse át, kövesse a cikkben a felülvizsgálati kezelésére, és az eredmények megtekintéséhez.

## <a name="manage-security-reviews"></a>Biztonsági felülvizsgálat kezelése
1. Lépjen a [Azure-portálon](https://portal.azure.com/) válassza ki a **Azure-erőforrások** alkalmazás az irányítópulton.
2. Válassza ki az erőforrás.
3. Válassza ki a **értékelést hozzáférési** szakasza az irányítópulton.
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. Válassza ki a kezelni kívánt áttekintése.

A hozzáférés felülvizsgálati részletei panelen számos felülvizsgálat kezelésére szolgáló beállítások.
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Leállítás
Hozzáférés az összes értékelést rendelkezik befejező dátumát, de használhatja a **leállítása** gomb korai befejezéséhez. A felhasználók még nem ellenőrzött időpontig, ha azok nem fogja tudni a felülvizsgálati befejezése után. Nem indítható újra áttekintése után a rendszer leállt.

### <a name="reset"></a>Alaphelyzetbe állítás
Visszaállíthatja egy eltávolítja azt a döntések áttekintése. Miután egy áttekintése alaphelyzetbe állítottuk, minden felhasználó be van-e tüntetve újra át. 

### <a name="apply"></a>Alkalmaz
A rendszer egy áttekintése után, mert elérte a záró dátum, vagy manuálisan, megszakította a **alkalmaz** gomb valósítja meg a felülvizsgálati eredményeit. A felhasználói hozzáférés megtagadva a felülvizsgálat alatt, ha ez a lépés, amely a szerepkör-hozzárendelés, azzal eltávolítja az.  

### <a name="delete"></a>Törlés
Ha nem a felülvizsgálati további iránt érdeklődik, törölje azt. A **törlése** gomb a felülvizsgálati eltávolítja a PIM alkalmazást.

## <a name="results"></a>Results (Eredmények)
Megtekintheti, és töltse le a felülvizsgálati eredményeinek az eredmények lapon listáját. ![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Felülvizsgálók
Megtekintheti és véleményezők hozzáadása a meglévő áttekintése. Az ellenőrzések elvégzéséhez Véleményező emlékeztesse.
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



