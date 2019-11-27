---
title: Azure regionális kvóta-növelési kérelmek | Microsoft Docs
description: Regionális kvóta-növelési kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 6c2d7dc64bca00ce664dc470e7c4405d69b49779
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531522"
---
# <a name="standard-quota-regional-vcpu-limit-increase"></a>Standard kvóta: regionális vCPU korlátjának növekedése 

A Resource Manager két típusú vCPU-kvótát támogat a virtuális gépekhez. Az utólagos elszámolású **virtuális gépek** és a **fenntartott VM-példányok** standard kvótát használnak. Az **alacsony prioritású virtuális gépek** alacsony prioritású kvótát használnak. 

Az utólagos elszámolású és a fenntartott virtuálisgép-példányok esetében a standard szintű vCPU kvóta az egyes régiókban lévő előfizetések esetében két szinten érvényes.
 
Az első szint a **teljes regionális vCPU korlát** (az összes virtuálisgép-sorozatban), a második szint pedig a virtuálisgép- **sorozat vCPU korlátja** (például a D sorozat vCPU). Az új virtuális gép üzembe helyezésének időpontjában az adott virtuálisgép-sorozat új és meglévő vCPU-használatának összege nem haladhatja meg az adott virtuálisgép-sorozathoz jóváhagyott vCPU-kvótát. Továbbá az összes virtuálisgép-sorozat összes új és meglévő vCPU száma nem haladhatja meg az előfizetéshez jóváhagyott teljes regionális vCPU-kvótát. Ha túllépi a kvótákat, a virtuális gép üzembe helyezése nem lesz engedélyezett. A virtuálisgép-sorozat vCPU-kvótájának növelését Azure Portal-ból kérheti. A virtuálisgép-sorozat kvótájának növekedése automatikusan megnöveli a teljes regionális vCPU-korlátot ugyanazzal az összeggel.

Új előfizetés létrehozásakor előfordulhat, hogy az alapértelmezett teljes regionális vCPU nem egyenlő az egyes virtuálisgép-sorozatok alapértelmezett vCPU-kvótáinak összegével. Ez egy olyan előfizetést eredményezhet, amely elegendő kvótával rendelkezik minden olyan virtuálisgép-sorozathoz, amelyet telepíteni kíván, de nincs elég kvóta az összes üzemelő példány regionális vCPU. Ebben az esetben kérelmet kell benyújtania a teljes regionális vCPU-korlát explicit módon történő növeléséhez. A regionális vCPU-határértékek teljes száma nem haladhatja meg a jóváhagyott kvóta összegét a régió összes virtuálisgép-sorozatában.

További információ a szabványos vCPU-kvótákkal kapcsolatban a [virtuális gépek vCPU kvótái lapján](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és az [Azure-előfizetés és-szolgáltatás korlátai](https://aka.ms/quotalimits) lapon.

További információ az **alacsony prioritású virtuális gépek vCPU-korlátainak növeléséről** [.](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)

A **standard szintű virtuális gép teljes regionális vCPU-korlátját** a **Súgó + támogatás** panelen, vagy a portálon a **használat + kvóta** panelen kérheti le.

## <a name="request-standard-quota-regional-vcpu-limit-increase-at-subscription-level-using-the-help--support-blade"></a>A standard kvóta regionális vCPU korlátozása az előfizetés szintjén a Súgó + támogatás panel használatával

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure "Súgó + támogatás" paneljén, amely a Azure Portal érhető el. 

1. https://portal.azure.com válassza a **Súgó + támogatás**lehetőséget.

![Súgó és támogatás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Válassza az **Új támogatási kérelem** lehetőséget. 

![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. A probléma típusa legördülő menüben válassza a **szolgáltatás és előfizetés korlátai (kvóták)** lehetőséget.

![Probléma típusa legördülő lista](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

![Előfizetés-hírolvasó kiválasztása](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Válasszon **más kérelmeket** a **kvóta típusa** legördülő listából.

![Kvóta típusa](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. A **részletek** ablaktáblán további információkat adhat meg az alábbi példában látható módon, hogy feldolgozza a kérést, és folytassa az eset létrehozásával. 
    1.  **Üzembe helyezési modell** – a Resource Manager meghatározása
    2.  **Kért régió** – adja meg a szükséges régiót, például: USA 2. keleti régiója
    3.  **Új határérték** – Itt adhatja meg az új régió korlátját. Ez nem haladhatja meg az előfizetéshez tartozó egyedi SKU-családokra vonatkozó jóváhagyott kvóta összegét

![Kvóta részletei](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Az előfizetési szinten az összes regionális vCPU-kvóta növelésének kérése a **használat + kvóta** panel használatával

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure-ban a Azure Portalben elérhető "használati + kvóta" panelen. 

1. https://portal.azure.com válassza az **előfizetések**lehetőséget.

![Előfizetések](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **Használat + kvóták** kiválasztása

![Használat és kvóták kiválasztása](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. A jobb felső sarokban válassza a **kérelem növekedés**lehetőséget.

![Kérések növekedése](./media/resource-manager-core-quotas-request/request-increase.png)

5. Válasszon **más kérelmeket** a **kvóta típusa** legördülő listából.

![Kvóta típusa](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. A **részletek** ablaktáblán további információkat adhat meg az alábbi példában látható módon, hogy feldolgozza a kérést, és folytassa az eset létrehozásával. 
    1.  **Üzembe helyezési modell** – a Resource Manager meghatározása
    2.  **Kért régió** – adja meg a szükséges régiót, például: USA 2. keleti régiója
    3.  **Új határérték** – Itt adhatja meg az új régió korlátját. Ez nem haladhatja meg az előfizetéshez tartozó egyedi SKU-családokra vonatkozó jóváhagyott kvóta összegét

![Kvóta részletei](./media/resource-manager-core-quotas-request/regional-details.png)



