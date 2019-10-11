---
title: Azure regionális kvóta-növelési kérelmek | Microsoft Docs
description: Regionális kvóta-növelési kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e73f22b0e617ad8f20b98c3bb0fb1647bf5fe61d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249078"
---
# <a name="total-regional-vcpu-limit-increase"></a>A teljes regionális vCPU-korlát növelése 

A virtuális gépekhez és a virtuálisgép-méretezési csoportokhoz tartozó Resource Manager-vCPU-kvóták minden egyes régióban az egyes előfizetések esetében két szinten érvényesülnek. 

Az első szint a **teljes regionális vCPU korlát** (az összes virtuálisgép-sorozatban), a második szint pedig a virtuálisgép- **sorozat vCPU korlátja** (például a D sorozat vCPU). Amikor új virtuális gépet kíván üzembe helyezni, az adott virtuálisgép-sorozat új és meglévő vCPU-használatának összege nem haladhatja meg az adott virtuálisgép-sorozathoz jóváhagyott vCPU-kvótát. Továbbá az összes virtuálisgép-sorozat összes új és meglévő vCPU száma nem haladhatja meg az előfizetéshez jóváhagyott teljes regionális vCPU-kvótát. Ha túllépi a kvótákat, a virtuális gép üzembe helyezése nem lesz engedélyezett.
A virtuálisgép-sorozat vCPU-kvótájának növelését Azure Portal-ból kérheti. A virtuálisgép-sorozat kvótájának növekedése automatikusan megnöveli a teljes regionális vCPU-korlátot ugyanazzal az összeggel. 

Új előfizetés létrehozásakor előfordulhat, hogy az alapértelmezett teljes regionális vCPU nem egyenlő az egyes virtuálisgép-sorozatok alapértelmezett vCPU-kvótáinak összegével. Ez egy olyan előfizetést eredményezhet, amely elegendő kvótával rendelkezik minden olyan virtuálisgép-sorozathoz, amelyet telepíteni kíván, de nincs elég kvóta az összes üzemelő példány regionális vCPU. Ebben az esetben kérelmet kell benyújtania a teljes regionális vCPU-korlát explicit módon történő növeléséhez. A regionális vCPU-határértékek teljes száma nem haladhatja meg a jóváhagyott kvóta összegét a régió összes virtuálisgép-sorozatában.

További információk a [virtuális gépek vCPU kvótái](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és az Azure- [előfizetések és-szolgáltatások korlátozásait](https://aka.ms/quotalimits) ismertető oldalon. 

Mostantól a **Súgó + támogatás** panelen vagy a portálon a **használat + kvóta** panelen is növelheti a növekedést. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>A **Súgó + támogatás** panelen igényeljen teljes regionális vCPU-kvóta-növelést az előfizetési szinten

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure "Súgó + támogatás" paneljén, amely a Azure Portal érhető el. 

1. @No__t – 0 értéknél válassza a **Súgó + támogatás**lehetőséget.

![Súgó és támogatás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Válassza az **Új támogatási kérelem** lehetőséget. 

![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. A probléma típusa legördülő menüben válassza a **szolgáltatás és előfizetés korlátai (kvóták)** lehetőséget.

![Probléma típusa legördülő lista](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

![Előfizetés-hírolvasó kiválasztása](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Válasszon **más kérelmeket** a **kvóta típusa** legördülő listából.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. A **részletek** ablaktáblán további információkat adhat meg az alábbi példában látható módon, hogy feldolgozza a kérést, és folytassa az eset létrehozásával. 
    1.  **Üzembe helyezési modell** – a Resource Manager meghatározása
    2.  **Kért régió** – adja meg a szükséges régiót, például: USA 2. keleti régiója
    3.  **Új határérték** – Itt adhatja meg az új régió korlátját. Ez nem haladhatja meg az előfizetéshez tartozó egyedi SKU-családokra vonatkozó jóváhagyott kvóta összegét

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Az előfizetési szinten az összes regionális vCPU-kvóta növelésének kérése a **használat + kvóta** panel használatával

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure-ban a Azure Portalben elérhető "használati + kvóta" panelen. 

1. @No__t – 0 közül válassza az **előfizetések**lehetőséget.

![Előfizetések](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **Használat + kvóták** kiválasztása

![Használat és kvóták kiválasztása](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. A jobb felső sarokban válassza a **kérelem növekedés**lehetőséget.

![Kérések növekedése](./media/resource-manager-core-quotas-request/request-increase.png)

5. Válasszon **más kérelmeket** a **kvóta típusa** legördülő listából.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. A **részletek** ablaktáblán további információkat adhat meg az alábbi példában látható módon, hogy feldolgozza a kérést, és folytassa az eset létrehozásával. 
    1.  **Üzembe helyezési modell** – a Resource Manager meghatározása
    2.  **Kért régió** – adja meg a szükséges régiót, például: USA 2. keleti régiója
    3.  **Új határérték** – Itt adhatja meg az új régió korlátját. Ez nem haladhatja meg az előfizetéshez tartozó egyedi SKU-családokra vonatkozó jóváhagyott kvóta összegét

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



