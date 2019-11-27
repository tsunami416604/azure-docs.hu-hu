---
title: Alacsony prioritású kvóta | Microsoft Docs
description: Alacsony prioritású kvóta-kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 49ac478330cf73dff050a3edcc15933692fa6448
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535170"
---
# <a name="low-priority-quota-limit-increase-for-all-vm-series"></a>Alacsony prioritású kvóta: az összes virtuálisgép-sorozat növelésének korlátozása

Az alacsony prioritású virtuális gépek eltérő modellt biztosítanak az Azure-használathoz, így alacsonyabb áron juthatnak el az Azure-ba, ha az utólagos elszámolású vagy a fenntartott VM-példányok üzembe helyezéséhez szükség van a virtuális gép eltávolítására. Az alacsony prioritású virtuális gépekről [itt](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority)olvashat bővebben.

A Resource Manager két típusú vCPU-kvótát támogat a virtuális gépekhez. Az utólagos elszámolású **virtuális gépek és a fenntartott VM-példányok** standard kvótát használnak. Az **alacsony prioritású virtuális gépek** alacsony prioritású kvótát használnak. 

Az **alacsony prioritású kvóta** esetében az erőforrás-kezelő vCPU kvótái az összes elérhető virtuálisgép-sorozatra érvényesek egyetlen regionális korlátként.

Egy új, alacsony prioritású virtuális gép üzembe helyezésének időpontjában az alacsony prioritású virtuálisgép-példányok új és meglévő vCPU-használatának összege nem haladhatja meg az alacsony prioritású vCPU engedélyezett kvótát. Ha túllépi az alacsony prioritású kvótát, az alacsony prioritású virtuális gép telepítése nem lesz engedélyezett. A Azure Portal alacsony prioritású vCPU-kvóta növelését is kérheti. 

További információ a szabványos vCPU-kvótákkal kapcsolatban a virtuális gépek vCPU kvótái lapján és az Azure-előfizetés és-szolgáltatás korlátai lapon. További információ az ezen a [lapon](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)található standard kvóta regionális vCPU korlátjának növeléséről.

Most már kérheti az **alacsony prioritású kvóta korlátozását az összes virtuálisgép-sorozathoz** a **Súgó + támogatás** panelen vagy a portálon a **használat + kvóta** panelen.

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Kis prioritású kvóta korlátozása az összes virtuálisgép-sorozatra a Súgó + támogatás panel használatával

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure "Súgó + támogatás" paneljén, amely a Azure Portal érhető el.

A **kvóta több régió esetében is igényelhető** egyetlen támogatási esettel. A részletekért tekintse meg az alábbi 10. lépést. 


1. https://portal.azure.com válassza a **Súgó + támogatás**lehetőséget.

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
   
7.  A **kvóta részletei*** panelen válassza a **telepítési modell** lehetőséget, és válasszon egy **helyet**.

![Részletek megadása](./media/resource-manager-core-quotas-request/3-7.png)

8. A kiválasztott hely esetében válassza az érték **típusa** **"alacsony prioritás"** lehetőséget. A **Type (típus** ) mezőben a standard és az alacsony prioritású kvótákat egyetlen támogatási esetben is kérheti a többszörös kijelölés támogatásával. További információk a **standard kvóta virtuális gépenként való növeléséről** ezen az [oldalon](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Részletek megadása](./media/resource-manager-core-quotas-request/3-8.png)

9.  Adja meg az előfizetéshez tartozó új korlátot. 
 
 ![Részletek megadása](./media/resource-manager-core-quotas-request/3-9.png)

10. Ha egynél több helyre vonatkozó kvótát szeretne kérelmezni, akkor a legördülő menüből válassza ki a megfelelő virtuálisgép-típust. Ezután megadhatja a kívánt új korlátozásokat.

![Részletek megadása](./media/resource-manager-core-quotas-request/3-10.png)

11. A kívánt kvóta megadása után kattintson a **Mentés gombra, és folytassa** a kvóta részletei panelen a támogatási kérelem létrehozásával folytatott folytatáshoz.

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Kis prioritású kvóta korlátozása az összes virtuálisgép-sorozatra a használat és a kvóta panelen

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure-ban a Azure Portalben elérhető "használati + kvóta" panelen.

A **kvóta több régió esetében is igényelhető** egyetlen támogatási esettel. A részletekért tekintse meg az alábbi 9. lépést. 

1.  https://portal.azure.com válassza az **előfizetések**lehetőséget.

 ![Előfizetések](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

 ![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Válassza a **használat + kvóták**lehetőséget.

 ![Használat és kvóták kiválasztása](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  A jobb felső sarokban válassza a **kérelem növekedés lehetőséget.**

   ![Kérések növekedése](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Válassza a **számítási-VM (magok-vCPU) előfizetési korlátot** az árajánlat típusaként.

  ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  A **kvóta részletei** panelen válassza a telepítési modell lehetőséget, és válasszon egy helyet.

  ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  A kiválasztott hely esetében válassza az érték **típusa** **"alacsony prioritás" lehetőséget.** A **Type (típus** ) mezőben a standard és az alacsony prioritású kvótákat egyetlen támogatási esetben is kérheti a többszörös kijelölés támogatásával. További információk a **standard kvóta virtuális gépenként való növeléséről** ezen az [oldalon](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Adja meg az előfizetéshez tartozó új korlátot.

  ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Ha egynél több helyre vonatkozó kvótát szeretne kérelmezni, akkor a legördülő menüből válassza ki a megfelelő **virtuálisgép-** típust. Ezután megadhatja a kívánt új korlátozásokat.

  ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. A kívánt kvóta megadása után kattintson a **Mentés gombra, és folytassa** a kvóta részletei panelen a támogatási kérelem létrehozásával folytatott folytatáshoz.

