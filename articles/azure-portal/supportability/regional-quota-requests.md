---
title: Az Azure regionális vCPU-kvótakorlátainak növelésének kérése
description: Hogyan kérheti a vCPU kvótakorlát növelését egy régióban az Azure Portalon.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843684"
---
# <a name="standard-quota-increase-limits-by-region"></a>Normál kvóta: A határértékek növelése régiónként

Az Azure Resource Manager kétféle vCPU-kvótát támogat a virtuális gépekhez:

* *Használatalapú virtuális gépek* re és a lefoglalt *virtuálisgép-példányokra* *szabványos vCPU-kvóta*vonatkozik.
* *A direkt virtuális gépekre* *azonnali vCPU-kvóta vonatkozik.*

A használatalapú és a fenntartott virtuálisgép-példányok normál vCPU-kvótája az egyes régiók minden egyes előfizetéséhez két szinten van kényszerítve:

* Az első szint a *teljes regionális vCPU-k korlátját,* az összes virtuálisgép-sorozatban.
* A második szint a *vm-sorozatú vCPU-k korlátja,* például a D-sorozatú vCPU-k.

Amikor üzembe helyez egy új virtuális gép azonnali virtuális gép, a teljes új és a meglévő vCPU-használat az adott virtuálisgép-sorozat nem haladhatja meg a jóváhagyott vCPU-kvótát az adott virtuálisgép-sorozat. Emellett az összes virtuálisgép-sorozatban üzembe helyezett új és meglévő vCPU-k teljes száma nem haladhatja meg az előfizetés teljes jóváhagyott regionális vCPU-kvótáját. Ha a kvóták bármelyikét túllépi, a virtuális gép központi telepítése nem engedélyezett.

Kérheti a vCPU kvótakorlát ának növelését a virtuális gép sorozataz Azure Portalhasználatával. A virtuális gép sorozat kvóta növekedése automatikusan növeli a teljes regionális vCPU-korlátot ugyanazzal az összeggel.

Új előfizetés létrehozásakor előfordulhat, hogy a regionális vCPU-k alapértelmezett teljes száma nem egyezik meg az összes virtuális gépsorozat teljes alapértelmezett vCPU-kvótájával. Ez az eltérés azt eredményezheti, hogy egy előfizetés elegendő kvótát minden egyes virtuálisgép-sorozat, amely üzembe kívánja helyezni. De lehet, hogy nem elég kvóta elhelyezésére a teljes regionális vCPU-k minden központi telepítéshez. Ebben az esetben kérelmet kell benyújtania a regionális vCPU-k teljes számának kifejezett növelésére. A teljes regionális vCPU-korlát nem haladhatja meg a régió összes virtuálisgép-sorozatának teljes jóváhagyott kvótáját.

A szabványos vCPU-kvótákról a [Virtuálisgép vCPU-kvóták](../../virtual-machines/windows/quotas.md) és [az Azure előfizetési és szolgáltatáskorlátok, kvóták és korlátozások.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Ha többet szeretne megtudni a virtuális gép vCPU-korlátjának növeléséről, olvassa el a [Direktségkvóta: Az összes virtuálisgép-sorozat korlátozásainak növelése című témakört.](low-priority-quota.md)

A vCPU-szabványkvóta-korlát régiónkénti növelését kétféleképpen kérheti.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Kvótanövelés kérése régiónként a Súgó + támogatás szolgáltatásból

VCPU-kvóta növelésének kérése régiónként a **Súgó + támogatás szolgáltatásból:**

1. Az [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás**lehetőséget.

   ![A "Súgó + támogatás" link](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. A **Súgó + támogatás**csoportban válassza az Új támogatási **kérelem**lehetőséget.

    ![Új támogatási kérelem](./media/resource-manager-core-quotas-request/new-support-request.png)

1. A **Probléma típusmezőben**válassza a **Szolgáltatás- és előfizetési korlátok (kvóták)** lehetőséget.

   ![Problématípus kiválasztása](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **Előfizetés esetén**válassza ki azt az előfizetést, amelynek a kvótáját növelni szeretné.

   ![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. A **Kvóta típushoz**válassza az **Egyéb kérelmek**lehetőséget.

   ![Kvótatípus kiválasztása](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Válassza a **Tovább: Megoldások lehetőséget** a **PROBLÉMA RÉSZLETEI**megnyitásához. A **Leírás csoportban**adja meg a következő információkat:

    1. A **telepítési modellhez**adja meg az **Erőforrás-kezelő**t.  
    1. A **Régió**területen adja meg a szükséges régiót, például **AZ USA keleti régiója 2.**  
    1. Az **Új korlát**esetében adjon meg egy új vCPU-korlátot a régióhoz. Ez az érték nem haladhatja meg az előfizetéshez az egyes Termékváltozat-sorozatok jóváhagyott kvótáinak összegét.

    ![A kvótakérelem részleteinek megadása](./media/resource-manager-core-quotas-request/regional-details.png)

1. A támogatási kérelem létrehozásának folytatásához válassza a **Véleményezés + létrehozás** lehetőséget.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Kvótanövelés kérése régiónként az Előfizetések szolgáltatásból

VCPU kvótanövelésének kérése régiónként az **Előfizetések**szolgáltatásból:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az Előfizetések lehetőséget, és válassza az **Előfizetések**lehetőséget.

   ![Ugrás az Előfizetések az Azure Portalon](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Válassza ki azt az előfizetést, amelynek a kvótáját növelni szeretné.

   ![Módosítandó előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. A bal oldali ablaktáblában válassza a **Használat + kvóták**lehetőséget.

   ![Kövesse a Felhasználás és kvóták hivatkozást](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. A jobb felső sarokban válassza a **Kérelem növelése lehetőséget.**

   ![Jelölje be a kvóta növeléséhez.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. A **Kvóta típusa mezőben**válassza az **Egyéb kérelmek**lehetőséget.

   ![A kvótatípus kiválasztása](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Válassza a **Tovább: Megoldások lehetőséget** a **PROBLÉMA RÉSZLETEI**megnyitásához. A **Leírás** mezőben adja meg a következő további információkat:

    1. A **telepítési modellhez**adja meg az **Erőforrás-kezelő**t.  
    1. A **Régió**területen adja meg a szükséges régiót, például **AZ USA keleti régiója 2.**  
    1. Az **Új korlát**esetében adjon meg egy új vCPU-korlátot a régióhoz. Ez az érték nem haladhatja meg az előfizetéshez az egyes Termékváltozat-sorozatok jóváhagyott kvótáinak összegét.

    ![Adatok megadása részletekben](./media/resource-manager-core-quotas-request/regional-details.png)

1. A támogatási kérelem létrehozásának folytatásához válassza a **Véleményezés + létrehozás** lehetőséget.
