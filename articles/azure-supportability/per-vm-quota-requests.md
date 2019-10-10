---
title: Azure virtuális gépenként vCPU-kvóta növelésére vonatkozó kérelmek | Microsoft Docs
description: virtuális gépenként vCPU kvóta-növelési kérelmek száma
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e99bdd92d4a1aab833a95943b22aaabe7f0daa1d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248665"
---
# <a name="vm-series-vcpu-limit-increase"></a>Virtuálisgép-sorozatok vCPU-korlátjának növelése

A virtuális gépekhez és a virtuálisgép-méretezési csoportokhoz tartozó Resource Manager-vCPU-kvóták minden egyes régióban az egyes előfizetések esetében két szinten érvényesülnek. 

Az első szint a **teljes regionális vCPU korlát** (az összes virtuálisgép-sorozatban), a második szint pedig a virtuálisgép- **sorozat vCPU korlátja** (például a D sorozat vCPU). Amikor új virtuális gépet kíván üzembe helyezni, az adott virtuálisgép-sorozat új és meglévő vCPU-használatának összege nem haladhatja meg az adott virtuálisgép-sorozathoz jóváhagyott vCPU-kvótát. Továbbá az összes virtuálisgép-sorozat összes új és meglévő vCPU száma nem haladhatja meg az előfizetéshez jóváhagyott teljes regionális vCPU-kvótát. Ha túllépi a kvótákat, a virtuális gép üzembe helyezése nem lesz engedélyezett.
A virtuálisgép-sorozat vCPU-kvótájának növelését Azure Portal-ból kérheti. A virtuálisgép-sorozat kvótájának növekedése automatikusan megnöveli a teljes regionális vCPU-korlátot ugyanazzal az összeggel. 

További információk a [virtuális gépek vCPU kvótái](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és az Azure- [előfizetések és-szolgáltatások korlátozásait](https://aka.ms/quotalimits) ismertető oldalon. 

Mostantól a **Súgó + támogatás** panelen vagy a portálon a **használat + kvóta** panelen is növelheti a növekedést. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Kérelem/virtuálisgép-sorozat vCPU kvótájának növekedése az előfizetés szintjén a **Súgó + támogatás** panelen

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure "Súgó + támogatás" paneljén, amely a Azure Portal érhető el. 

1. @No__t – 0 értéknél válassza a **Súgó + támogatás**lehetőséget.

   ![Súgó és támogatás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Válassza az **Új támogatási kérelem** lehetőséget. 

     ![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. A probléma típusa legördülő menüben válassza a **szolgáltatás és előfizetés korlátai (kvóták)** lehetőséget.

   ![Probléma típusa legördülő lista](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Előfizetés-hírolvasó kiválasztása](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Válassza a **számítási-VM (magok-vCPU) előfizetési korlátot** a **kvóta típusa** legördülő menüben. 

   ![Kvóta típusának kiválasztása](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. A **probléma részletei**területen további információkat adhat meg a kérés feldolgozásához a **részletek megadása**lehetőségre kattintva.

   ![Részletek megadása](./media/resource-manager-core-quotas-request/provide-details.png)

7. A **kvóta részletei** panelen válassza a telepítési modell lehetőséget, és válasszon egy helyet.

   ![Kvóta részletei – DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Válassza ki a növekményt igénylő **SKU-családokat** . 

   ![Termékváltozat-család](./media/resource-manager-core-quotas-request/sku-family.png)

9. Adja meg az előfizetéshez hasonló új korlátozásokat. Egy sor eltávolításához törölje az SKU-t a SKU-család legördülő menüből, vagy kattintson az Elvetés "x" ikonra. Miután megadta a kívánt kvótát az egyes SKU-családokhoz, kattintson a Save (Mentés) gombra, **és folytassa** a kvóta részletei panelen a támogatási kérések létrehozásához.

   ![Új korlátok](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Kérelem/virtuálisgép-sorozat vCPU kvótájának növekedése az előfizetési szinten a használat és a **kvóta** panelen

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure-ban a Azure Portalben elérhető "használati + kvóta" panelen. 

1. @No__t – 0 közül válassza az **előfizetések**lehetőséget.

   ![Előfizetések](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **Használat + kvóták** kiválasztása

   ![Használat és kvóták kiválasztása](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. A jobb felső sarokban válassza a **kérelem növekedés**lehetőséget.

   ![Kérések növekedése](./media/resource-manager-core-quotas-request/request-increase.png)

5. Válassza a **számítási-VM (magok-vCPU) előfizetési korlátot** az árajánlat típusaként. 

   ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. A **kvóta részletei** panelen válassza a telepítési modell lehetőséget, és válasszon egy helyet.

   ![Kvóta problémájának panelje](./media/resource-manager-core-quotas-request/quota-details.png)

7. Válassza ki a növekményt igénylő **SKU-családokat** .

   ![SKU-sorozat kiválasztva](./media/resource-manager-core-quotas-request/sku-family.png)

8. Adja meg az előfizetéshez hasonló új korlátozásokat. Egy sor eltávolításához törölje az SKU-t a SKU-család legördülő menüből, vagy kattintson az Elvetés "x" ikonra. Miután megadta a kívánt kvótát az egyes SKU-családokhoz, kattintson a Save (Mentés) gombra, **és folytassa** a probléma lépése lapon a támogatási kérések létrehozásához.

   ![SKU új kvóta kérése](./media/resource-manager-core-quotas-request/new-limits.png)
 
