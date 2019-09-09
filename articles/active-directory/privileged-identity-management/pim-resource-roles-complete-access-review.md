---
title: Az Azure-erőforrás szerepköreinek hozzáférési felülvizsgálatának befejezése a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan végezheti el a Azure AD Privileged Identity Management (PIM) Azure Resource szerepköreinek hozzáférési felülvizsgálatát.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b9563a4032011b999bf867fc782ba4cbb9c3fac
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804257"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-pim"></a>Azure-erőforrás szerepköreinek hozzáférési felülvizsgálatának befejezése a PIM-ben
A Kiemelt szerepkörű rendszergazdák megtekinthetik az emelt szintű hozzáférést a [hozzáférési felülvizsgálat elindítása](pim-resource-roles-start-access-review.md)után. Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automatikusan elküld egy e-mailt, amely felszólítja a felhasználókat, hogy tekintsék át a hozzáférésüket. Ha a felhasználó nem kap e-mailt, elküldheti a [hozzáférési felülvizsgálat végrehajtásának](pim-resource-roles-perform-access-review.md)utasításait.

Ha a hozzáférési felülvizsgálati időszak véget ért, vagy miután az összes felhasználó befejezte az önellenőrzést, kövesse a jelen cikkben ismertetett lépéseket a felülvizsgálat kezeléséhez és az eredmények megtekintéséhez.

## <a name="manage-access-reviews"></a>Hozzáférési felülvizsgálatok kezelése
1. Nyissa meg az [Azure Portal](https://portal.azure.com/). Ezután az irányítópulton válassza ki az **Azure-erőforrások** alkalmazást.

2. Válassza ki az erőforrást.

3. Válassza ki az irányítópult **hozzáférési felülvizsgálatok** szakaszát.

    ![Azure-erőforrások – hozzáférési felülvizsgálatok listája, amely a szerepkört, a tulajdonost, a kezdő dátumot, a záró dátumot és az állapotot mutatja](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Válassza ki a felügyelni kívánt hozzáférési felülvizsgálatot.

A hozzáférési felülvizsgálat részletes paneljén számos lehetőség van a felülvizsgálat kezelésére. A lehetőségek a következők:

![A felülvizsgálatok kezelésének lehetőségei – Leállítás, alaphelyzetbe állítás, alkalmazás, törlés](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Leállítás
Az összes hozzáférési felülvizsgálat befejező dátummal rendelkezik, de a **Leállítás** gomb használatával is befejezheti korán. A felülvizsgálat leállítása után minden olyan felhasználó, aki még nem fejezte be a felülvizsgálatot, nem tudja befejezni. A felülvizsgálat leállítását követően nem indítható újra.

### <a name="reset"></a>Visszaállítás
A hozzáférési felülvizsgálat alaphelyzetbe állítható, hogy eltávolítsa a rajta végrehajtott összes döntést. A hozzáférési felülvizsgálat alaphelyzetbe állítása után a rendszer az összes felhasználót újra megjelöli. 

### <a name="apply"></a>Alkalmaz
A hozzáférési felülvizsgálat befejezése után az **Apply (alkalmaz** ) gombra kattintva implementálhatja a felülvizsgálat eredményét. Ha egy felhasználó hozzáférése megtagadva a felülvizsgálatban, ez a lépés eltávolítja a szerepkör-hozzárendelést.  

### <a name="delete"></a>Törlés
Ha még nem érdekli a felülvizsgálat, törölje. A **Törlés** gomb eltávolítja a vizsgálatot a PIM alkalmazásból.

## <a name="results"></a>Results (Eredmények)
Az **eredmények** lapon megtekintheti és letöltheti a felülvizsgálati eredmények listáját. 

![A felhasználók, az eredmény, a Reason, a reviewed by, a által alkalmazott eredmények és az eredmény alkalmazása](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Felülvizsgálók
Felülvizsgálók megtekintése és hozzáadása a meglévő hozzáférési felülvizsgálathoz. Visszajelzések elvégzéséhez emlékeztesse a véleményezőket.

![Véleményezők lap listázási neve és egyszerű felhasználóneve](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>További lépések

- [Azure-erőforrásszerepkörök hozzáférési felülvizsgálatának indítása a PIM szolgáltatásban](pim-resource-roles-start-access-review.md)
- [A saját Azure-erőforrásszerepkörök hozzáférési felülvizsgálatának végrehajtása a PIM szolgáltatásban](pim-resource-roles-perform-access-review.md)
