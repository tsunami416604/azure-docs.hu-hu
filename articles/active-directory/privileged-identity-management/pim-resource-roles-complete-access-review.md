---
title: Fejezze be az Azure erőforrások áttekintése Privileged Identity Management használatával |} Microsoft Docs
description: Ismerteti, hogyan lehet elvégezni egy az Azure-erőforrások áttekintése.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 2e6556d3bac386bff26f9a5ce8f599e099fdb6c4
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233801"
---
# <a name="complete-an-access-review-for-azure-resources-by-using-privileged-identity-management"></a>Fejezze be az Azure erőforrások áttekintése Privileged Identity Management használatával
Kiemelt szerepkörű rendszergazda emelt szintű hozzáférés után tekintse át a [biztonsági felülvizsgálat elindult](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) az Azure-erőforrások automatikusan elküld egy e-mailt, amely kéri a felhasználóktól tekintse át a hozzáférésüket. Ha a felhasználó nem kap egy e-mailt, küldhet nekik az utasításokat [egy biztonsági felülvizsgálat végrehajtása](pim-resource-roles-perform-access-review.md).

A biztonsági felülvizsgálati időszakot követően vagy után a felhasználók a saját tekintse át, kövesse a cikkben leírt lépéseket a felülvizsgálati kezelésére, és az eredmények megtekintéséhez.

## <a name="manage-security-reviews"></a>Biztonsági felülvizsgálat kezelése
1. Nyissa meg az [Azure Portal](https://portal.azure.com/). Ezt követően az irányítópultra, válassza a **Azure-erőforrások** alkalmazás.

2. Válassza ki az erőforrás.

3. Válassza ki a **értékelést hozzáférési** szakasza az irányítópulton.
![Hozzáférési felülvizsgálatok](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Válassza ki a kezelni kívánt áttekintése.

A áttekintése részletek paneljén számos felülvizsgálat kezelésére szolgáló beállítások. A beállítások a következők:

![A felülvizsgálati kezelésére vonatkozó beállítások](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Leállítás
Hozzáférés az összes értékelést rendelkezik befejező dátumát, de használhatja a **leállítása** gomb korai befejezéséhez. A felhasználók, akik még nem fejeződött be a felülvizsgálati időpontig nem fog tudni befejezni, a felülvizsgálati befejezése után. Nem indítható újra áttekintése után a rendszer leállt.

### <a name="reset"></a>Alaphelyzetbe állítás
Visszaállíthatja rajta végzett kapcsolatos összes döntést eltávolítása egy áttekintése. Miután egy áttekintése alaphelyzetbe állítottuk, minden felhasználó be van-e tüntetve újra át. 

### <a name="apply"></a>Alkalmaz
Egy áttekintése befejezése után használja a **alkalmaz** gomb megvalósításához a felülvizsgálati eredményeit. A felhasználói hozzáférés megtagadva a felülvizsgálat alatt, ha ez a lépés eltávolítja a szerepkör-hozzárendelés.  

### <a name="delete"></a>Törlés
Ha Ön nem érdeklődik a felülvizsgálati hozzá több, törölje azt. A **törlése** gomb a felülvizsgálati eltávolítja a PIM alkalmazást.

## <a name="results"></a>Results (Eredmények)
Az a **eredmények** lapon megtekintheti, és töltse le a felülvizsgálati eredményeinek listája. 
![Eredmények lap](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Felülvizsgálók
Megtekintheti és véleményezők hozzáadása a meglévő áttekintése. Az ellenőrzések elvégzéséhez Véleményező emlékeztesse.
![A felülvizsgálók hozzáadása](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



