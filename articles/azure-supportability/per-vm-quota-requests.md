---
title: Azure virtuális gépenként vCPU-kvóta növelésére vonatkozó kérelmek | Microsoft Docs
description: virtuális gépenként vCPU kvóta-növelési kérelmek száma
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: ccd0c88c95ae9a752ef8ea2387bbde4f8559bc68
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531706"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>Standard kvóta: virtuálisgép-sorozatok vCPU korlátjának növekedése

A Resource Manager két típusú vCPU-kvótát támogat a virtuális gépekhez. Az utólagos elszámolású **virtuális gépek és a fenntartott VM-példányok** standard kvótát használnak. Az **alacsony prioritású virtuális gépek** alacsony prioritású kvótát használnak. Az utólagos elszámolású és a fenntartott VM-példányokra vonatkozó standard vCPU-kvóta az egyes régiókban található minden egyes előfizetés esetében két szinten érvényes

Az első szint a **teljes regionális vCPU korlát** (az összes virtuálisgép-sorozatban), a második szint pedig a virtuálisgép- **sorozatok vCPU korlátja** (például a Dv3 sorozat vCPU). Az új virtuális gép üzembe helyezésének időpontjában az adott virtuálisgép-sorozat új és meglévő vCPU-használatának összege nem haladhatja meg az adott virtuálisgép-sorozathoz jóváhagyott vCPU-kvótát. Továbbá az összes virtuálisgép-sorozat összes új és meglévő vCPU száma nem haladhatja meg az előfizetéshez jóváhagyott teljes regionális vCPU-kvótát. Ha túllépi a kvótákat, a virtuális gép üzembe helyezése nem lesz engedélyezett.
A virtuálisgép-sorozat vCPU-kvótájának növelését Azure Portal-ból kérheti. A virtuálisgép-sorozat kvótájának növekedése automatikusan megnöveli a teljes regionális vCPU-korlátot ugyanazzal az összeggel. 

További információ a szabványos vCPU-kvótákkal kapcsolatban a [virtuális gépek vCPU kvótái lapján](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és az [Azure-előfizetés és-szolgáltatás korlátai lapon](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

További információ a standard szintű kvóta regionális vCPU korlátjának növeléséről [itt](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)található. 

További információ az **alacsony prioritású virtuális gépek vCPU-korlátainak növeléséről** [.](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)

A **standard szintű vCPU kvóta-korlátozásokat** a **Súgó + támogatás** panelen vagy a portálon a **használat + kvóta** panelen kérheti le.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>Standard vCPU-kvóta növelésének kérése virtuálisgép-sorozatok esetén a Súgó + támogatás panelen

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure "Súgó + támogatás" paneljén, amely a Azure Portal érhető el. 

A kvóta több régió esetében is igényelhető egyetlen támogatási esettel. A részletekért tekintse meg az alábbi 11. lépést.

1. https://portal.azure.comválassza a **Súgó + támogatás**lehetőséget.

   ![Súgó és támogatás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Válassza az **Új támogatási kérelem** lehetőséget. 

     ![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. A probléma típusa legördülő menüben válassza a **szolgáltatás és előfizetés korlátai (kvóták)** lehetőséget.

   ![Probléma típusa legördülő lista](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Előfizetés-hírolvasó kiválasztása](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Válassza a **számítási-VM (magok-vCPU) előfizetési korlátot** a **kvóta típusa** legördülő menüből. 

   ![Kvóta típusának kiválasztása](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. A **probléma részletei**területen további információkat adhat meg a kérés feldolgozásához a **részletek megadása**lehetőségre kattintva.

   ![Részletek megadása](./media/resource-manager-core-quotas-request/provide-details.png)

7. A **kvóta részletei** panelen válassza a **telepítési modell** lehetőséget, és válasszon egy **helyet.**

   ![Kvóta részletei – DM](./media/resource-manager-core-quotas-request/1-7.png)

8. A kiválasztott helyen válassza a **Type** Value as **"standard"** lehetőséget. A **Type (típus** ) mezőben a standard és az alacsony prioritású kvótákat egyetlen támogatási esetben is kérheti a többszörös kijelölés támogatásával. További információ az **alacsony prioritású kvóta korlátainak növeléséről** a **< > oldalon**.

   ![Termékváltozat-család](./media/resource-manager-core-quotas-request/1-8.png)

9. Válassza ki a növekményt igénylő **SKU-családokat**

   ![Termékváltozat-család](./media/resource-manager-core-quotas-request/1-9.png)

10. Adja meg az előfizetéshez hasonló új korlátozásokat. Egy sor eltávolításához törölje az SKU-t a SKU-család legördülő menüből, vagy kattintson az Elvetés "x" ikonra. 

   ![Új korlátok](./media/resource-manager-core-quotas-request/1-10.png)

11. Ha egynél több helyre vonatkozó kvótát szeretne kérelmezni, akkor a legördülő menüből válassza ki a megfelelő **virtuálisgép-** típust. Ezzel a lépéssel előre betöltődik a korábbi **helyhez** kijelölt SKU-családok az új helyen, és egyszerűen megadhatja az új korlátozásokat.

   ![Több hely](./media/resource-manager-core-quotas-request/1-11.png)
   
12. Miután megadta a kívánt kvótát az egyes SKU-családokhoz, kattintson a Save (Mentés) gombra, **és folytassa** a kvóta részletei panelen a támogatási kérések létrehozásához.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>Standard vCPU-kvóta növelésének kérése virtuálisgép-sorozat esetén a használati szint és a kvóta panel használatával

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure-ban a Azure Portalben elérhető "használati + kvóta" panelen.

A **kvóta több régió esetében is igényelhető** egyetlen támogatási esettel. A részletekért tekintse meg az alábbi 10. lépést.

1. https://portal.azure.comválassza az **előfizetések**lehetőséget.

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

   ![Kvóta problémájának panelje](./media/resource-manager-core-quotas-request/1-1-6.png)

7. A kiválasztott helyen válassza a **Type** Value as **"standard"** lehetőséget. A **Type (típus** ) mezőben a standard és az alacsony prioritású kvótákat egyetlen támogatási esetben is kérheti a többszörös kijelölés támogatásával. További információ az **alacsony prioritású vCPU-korlátok** ezen a [lapon](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)való növeléséről.

   ![SKU-sorozat kiválasztva](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. Válassza ki a növekményt igénylő **SKU-családokat**

   ![SKU-sorozat kiválasztva](./media/resource-manager-core-quotas-request/1-1-8.png)

9. Adja meg az előfizetéshez hasonló új korlátozásokat. Egy sor eltávolításához törölje az SKU-t a SKU-család legördülő menüből, vagy kattintson az Elvetés "x" ikonra. 

   ![SKU új kvóta kérése](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. Ha egynél több helyre vonatkozó kvótát szeretne kérelmezni, akkor a legördülő menüből válassza ki a megfelelő **virtuálisgép-** típust. Ezzel a lépéssel előre betöltődik a korábbi **helyhez** kijelölt SKU-családok az új helyen, és egyszerűen megadhatja az új korlátozásokat.
   
    ![SKU új kvóta kérése](./media/resource-manager-core-quotas-request/1-1-10.png)
 
