---
title: "Erőforrás-házirendek az Azure portálon |} Microsoft Docs"
description: "Ismerteti az erőforrás-kezelő szabályzatok létrehozása és kezelése az Azure portál használatával. Házirendek: az előfizetés vagy az erőforrás-csoportok alkalmazhatók."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>Az Azure portál használatával és kezelheti az erőforrás-házirendekkel
Az Azure-portál lehetővé teszi, hogy rendelheti hozzá az erőforrás-házirendek erőforráscsoport-sablonok és előfizetések. A felhasználói felület segítségével egyszerűen válassza ki a házirendet, amelyet szeretne hozzárendelni, és adja meg a házirend-beállítások testreszabása házirendhez tartozó paraméterértékeket. 

A portálon keresztül egy házirend hozzárendelése, a házirend-definíció már léteznie kell az előfizetéshez. Az előfizetés rendelkezik, amely készen rendelheti hozzá az erőforráscsoport-sablonok és előfizetések számos beépített házirend-definíciók. Láthatja, hogy a beépített házirendek és a portál használata házirendek rendelhetők definiált egyéni házirendekben. A házirendek és hogyan adhat meg egyéni házirend bevezető, lásd: [erőforrás házirendek – áttekintés](resource-manager-policy.md).

Összes gyermek-erőforrás által örökölt házirendek. Igen a házirend vonatkozik egy erőforráscsoport, esetén alkalmazandó az erőforráscsoport összes erőforrást. Ebben a cikkben kifejezés **hatókör** az erőforráscsoport vagy -előfizetéssel, amely hozzá van rendelve a házirend vonatkozik. 

Házirendek létrehozása, és az erőforrások (PUT és műveletek javítás) frissítésekor kiértékelése.

## <a name="assign-a-policy"></a>A házirend kijelölése

1. Egy házirend hozzárendelése egy erőforráscsoport vagy egy előfizetés, válassza ki, hogy erőforráscsoportba vagy előfizetésbe. Válassza a beállítások **házirendek**.

   ![Jelölje ki a házirendek](./media/resource-manager-policy-portal/select-policies.png)

2. A hatókör házirend-hozzárendelés létrehozásához válassza **adja hozzá a hozzárendelés**.

   ![hozzárendelés hozzáadása](./media/resource-manager-policy-portal/add-assignment.png)

3. Válassza ki a hozzárendelni kívánt házirendet. Akkor is, ha az előfizetés nem hozzáadott összes házirend-definíciót, tekintse meg a beépített házirendek, amelyek rendelhető hozzá. A beépített házirendek számos gyakori helyzetek foglalkozik.

   ![Válassza ki a definíció](./media/resource-manager-policy-portal/select-definition.png)

4. A házirend kijelölése után tekintse meg a házirendet, és a paramétereket, hogy a házirend leírását. Például az alábbi képen látható a **engedélyezett helyek** paraméter, amely pedig szükséges a házirend, amely korlátozza a helyeket.

   ![paraméterek megjelenítése](./media/resource-manager-policy-portal/show-parameters.png)

5. A felhasználói felületen jelölje ki az értékeket adja meg a házirend paraméterek (például a hely központi telepítéshez használható).

   ![Válassza ki a paramétert értékek](./media/resource-manager-policy-portal/select-parameters.png)

6. Adja meg a többi paraméter értékét. A hatókör automatikusan a panelen, a házirend-hozzárendelés indításakor kijelölt alapján. Kattintson az **OK** gombra, amikor végzett.

   ![Paraméterek megadása](./media/resource-manager-policy-portal/define-parameters.png)

  A házirend rendelt-e a megadott hatókörben.

## <a name="view-policy-assignments"></a>Házirend-hozzárendelések megtekintése

Házirend hozzárendelése után azt látja, a házirendek az adott hatókörnél listájában. A **részletek** lapján a házirend-hozzárendelés összegzését jeleníti meg.

![Részletek megjelenítése](./media/resource-manager-policy-portal/show-details.png)

A **hozzárendelési szabály** lapon láthatók a JSON a házirend-definíció.

![hozzárendelési szabály megjelenítése](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>Egy meglévő házirend-hozzárendelés módosítása

Ha módosítani szeretné a házirendet, válassza ki **hozzárendelés szerkesztése** vagy **törlése**

![szerkesztheti és törölheti a hozzárendelés](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>Rendelje hozzá az egyéni házirendek

Egyéni házirendeket az előfizetés adta meg, ha ezek a házirendek legyenek a portálon keresztül. Ezek a házirendek vannak végrehajtásával kerüli meg a **[egyéni]**

![Egyéni házirendek](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>Következő lépések
* A házirendek definiálása a JSON-szintaxissal kapcsolatos további tudnivalókért lásd: [erőforrás házirendek – áttekintés](resource-manager-policy.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
* A házirend séma közzé van téve a [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

