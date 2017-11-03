---
title: "Kvótái és korlátai méretezni a laborban a Azure DevTest Labs szolgáltatásban |} Microsoft Docs"
description: "Útmutató egy tesztlabor méretezése a Azure DevTest Labs szolgáltatásban"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: tarcher
ms.openlocfilehash: f11ed42b474e4f208eac92689bfa33fb188d15a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Skála kvótái és korlátai a DevTest Labs szolgáltatásban
Munka a DevTest Labs szolgáltatásban, Észreveheti, hogy nincsenek-e bizonyos bizonyos Azure-erőforrások, ami kihathat a DevTest Labs szolgáltatás alapértelmezett korlátozások. Ezek a korlátozások nevezzük **kvóták**.

> [!NOTE]
> A DevTest Labs szolgáltatás nem ugyanazok a kvóták. Bármely kvóták léphetnek olyan alapértelmezett tartoznak a teljes Azure-előfizetés.

Minden Azure-erőforrás is használhat, amíg el nem éri a kvótát. Minden előfizetés külön kvóták és a használati előfizetésenként követhető nyomon.

Például az egyes előfizetések rendelkezik 20 magszámra vonatkozó alapértelmezett kvótát. Igen ha virtuális gépeket a tesztlaborban négy magok hoz létre, majd csak létrehozhat öt virtuális gépek. 

[Az Azure előfizetés és szolgáltatásra vonatkozó korlátozások](https://docs.microsoft.com/azure/azure-subscription-service-limits) egy Azure-erőforrások leggyakoribb kvótái sorolja fel. Az erőforrások leggyakrabban használt laborkörnyezetben, és amely léphetnek fel a kvótákat, az tartalmazza az VM mag, nyilvános IP-címek, hálózati adapter, felügyelt lemezek, RBAC szerepkör-hozzárendelés és ExpressRoute-Kapcsolatcsoportok.

## <a name="view-your-usage-and-quotas"></a>A használati és a kvóták megtekintése
Ezeket a lépéseket mutatja be az aktuális kvóták tekintse meg az adott Azure-erőforrások előfizetés, és tekintse meg az egyes hány százalékát használja.

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **több szolgáltatások**, majd válassza ki **számlázási** a listából.
1. A számlázási panelen válassza ki egy előfizetést.
4. Válassza ki **használati + kvóták**.

   ![Használati és kvóták gomb](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   A használati + kvóták panelen megjelenik, a listaelem különböző erőforrások érhető el, hogy az előfizetés és a kvóta erőforrásonként használt százalékos.

   ![Kvóták és használat](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>A kért további erőforrást az előfizetésében
Ha eléri a kvóta kap, az alapértelmezett határérték előfizetés az erőforrások növelhető legfeljebb, a [Azure-előfizetés és a szolgáltatásra vonatkozó korlátozások](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Ezeket a lépéseket mutatja be a kvóta növelését keresztül a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **több szolgáltatások**, jelölje be **számlázási**, majd válassza ki **használati + kvóták**.
1. A használati + kvóták panelen válassza a **kérelem növelése** gombra.

   ![Kérésgombhoz növelése](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Végezze el, és küldje el a kérelmet, töltse ki a szükséges adatokat az összes három lap a **új támogatja a kérelem** űrlap.

   ![Növelje a kérésűrlapra](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Understanding Azure korlátozásai és növekszik](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) további tájékoztatást ad azokról a kvóta növelését az Azure támogatási szolgálatától.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Következő lépések
* Megismerkedhet a [Office DevTest Labs Azure Resource Manager gyorsindítási sablonok](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
