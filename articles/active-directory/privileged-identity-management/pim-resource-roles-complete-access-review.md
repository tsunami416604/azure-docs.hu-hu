---
title: A PIM az Azure-erőforrások szerepköreihez tartozó hozzáférési felülvizsgálat befejezése |} A Microsoft Docs
description: Ismerje meg, hogyan végezheti el az Azure AD Privileged Identity Management (PIM) az Azure-erőforrások szerepköreihez tartozó hozzáférési felülvizsgálat.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 1e2bdeeb8f2b59d69e761303176c36b26f47d4c8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165489"
---
# <a name="complete-an-access-review-for-azure-resource-roles-in-pim"></a>A PIM az Azure-erőforrások szerepköreihez tartozó hozzáférési felülvizsgálat befejezése
A kiemelt szerepkörű rendszergazdák tekintheti át az emelt szintű hozzáférés után egy [el lett indítva a hozzáférési felülvizsgálat](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) az Azure-erőforrások automatikus küld egy e-mailt, amely kéri a felhasználóktól, tekintse át a hozzáférésüket. Ha a felhasználó nem kap egy e-mailt, küldhet nekik az utasításokat [hozzáférési felülvizsgálat végrehajtása](pim-resource-roles-perform-access-review.md).

A hozzáférési felülvizsgálati időszak után, vagy ha minden felhasználó már nem a helyi tekintse át, kövesse a cikkben az eredmények megtekintéséhez és kezeléséhez a felülvizsgálatot.

## <a name="manage-access-reviews"></a>A hozzáférési felülvizsgálatok kezelése
1. Nyissa meg az [Azure Portal](https://portal.azure.com/). Ezután az irányítópulton, válassza ki a **Azure-erőforrások** alkalmazás.

2. Válassza ki az erőforrást.

3. Válassza ki a **hozzáférési felülvizsgálatokkal** szakasz az irányítópult.
![Hozzáférési felülvizsgálatok](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Válassza ki a hozzáférési felülvizsgálatot, amelyet kezelni szeretne.

A hozzáférési felülvizsgálat részletei panelen számos kezelése, tekintse át a beállításokat. A beállítások a következők:

![Felülvizsgálat kezelésének lehetőségei](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Leállítás
Minden hozzáférési felülvizsgálatok a befejező dátum rendelkezik, de használhatja a **leállítása** gomb korai befejezéséhez. Minden felhasználó, aki még nem fejeződött be a felülvizsgálatot, az időpontig nem fog tudni a felülvizsgálat leállítása után fejezze be. Felülvizsgálat nem indítható újra, miután van állítva.

### <a name="reset"></a>Alaphelyzetbe állítás
Alaphelyzetbe állíthatja a hozzáférési felülvizsgálat rajta az összes döntések eltávolítása. Miután ezzel alaphelyzetbe állítja a hozzáférési felülvizsgálat, minden felhasználó be vannak-e megjelölve felülvizsgálatát újra. 

### <a name="apply"></a>Alkalmaz
Hozzáférési felülvizsgálat befejezése után használja a **alkalmaz** gomb a felülvizsgálat eredményének megvalósításához. Ha a felhasználó megtagadja a felülvizsgálati, ez a lépés eltávolítja a szerepkör-hozzárendelés.  

### <a name="delete"></a>Törlés
Ha nem szeretne a felülvizsgálat kapcsolatban, törölje azt. A **törlése** gomb a felülvizsgálat távolít el a PIM alkalmazást.

## <a name="results"></a>Results (Eredmények)
Az a **eredmények** lapon megtekintheti, letöltheti a felülvizsgálati eredmények listáját. 
![Eredmények lap](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Felülvizsgálók
Megtekintheti, és a felülvizsgálók hozzáadása a meglévő hozzáférési felülvizsgálatot. Az ellenőrzések elvégzéséhez Véleményező emlékeztesse.
![Felülvizsgálók hozzáadása](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure-erőforrások szerepköreihez tartozó hozzáférési felülvizsgálat indítása](pim-resource-roles-start-access-review.md)
- [A saját Azure-erőforrásszerepkörök hozzáférési felülvizsgálatának végrehajtása a PIM szolgáltatásban](pim-resource-roles-perform-access-review.md)
