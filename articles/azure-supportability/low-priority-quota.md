---
title: Helyszíni kvóta | Microsoft Docs
description: Helyszíni kvóta-kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850567"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>Helyszíni kvóta: az összes virtuálisgép-sorozat növelésének korlátozása

A helyszíni virtuális gépek az Azure-használat egy másik modelljét biztosítják, és alacsonyabb költségeket biztosítanak az Azure számára az utólagos elszámolású vagy fenntartott virtuálisgép-példányok igény szerinti üzembe helyezéséhez. További információ a [helyszíni virtuális](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)gépekről.

A Resource Manager két típusú vCPU-kvótát támogat a virtuális gépekhez. Az utólagos elszámolású **virtuális gépek és a fenntartott VM-példányok** standard kvótát használnak. A **helyszíni virtuális gépek** helyszíni kvótát használnak. 

A **helyszíni kvóta** típusához a Resource Manager vCPU kvótái az összes rendelkezésre álló virtuálisgép-sorozatban egyetlen regionális korlátként lesznek kikényszerítve.

Egy új helyszíni virtuális gép üzembe helyezésének időpontjában az összes helyszíni virtuálisgép-példány új és meglévő vCPU-használatának összege nem haladhatja meg a jóváhagyott helyszíni vCPU kvóta korlátját. Ha túllépi a helyszíni kvótát, a helyszíni virtuális gép üzembe helyezése nem lesz engedélyezett. A helyszíni vCPU kvóta korlátját a Azure Portaltól kérheti. 

További információ a szabványos vCPU-kvótákkal kapcsolatban a virtuális gépek vCPU kvótái lapján és az Azure-előfizetés és-szolgáltatás korlátai lapon. További információ az ezen a [lapon](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)található standard kvóta regionális vCPU korlátjának növeléséről.

Mostantól az összes virtuálisgép-sorozathoz a **Súgó + támogatás** panelen vagy a portálon található **használati és kvóta** panelen is megnövelheti a **helyszíni kvóta korlátozását** .

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>A helyszíni kvóta korlátozásának kérése az összes virtuálisgép-sorozathoz a Súgó + támogatás panel használatával

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure "Súgó + támogatás" paneljén, amely a Azure Portal érhető el.

A **kvóta több régió esetében is igényelhető** egyetlen támogatási esettel. A részletekért tekintse meg az alábbi 10. lépést. 


1. https://portal.azure.com válassza a **Súgó + támogatás**lehetőséget.

   ![Súgó + támogatás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
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

8. A kiválasztott helyen válassza a **Type** Value as **"spot"** lehetőséget. A **Type (típus** ) mezőben a standard és a direktszín típusú kvótákat egyetlen támogatási esetben is kérheti a többszörös kijelölés támogatásával. További információk a **standard kvóta virtuális gépenként való növeléséről** ezen az [oldalon](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Részletek megadása](./media/resource-manager-core-quotas-request/3-8.png)

9.  Adja meg az előfizetéshez tartozó új korlátot. 
 
 ![Részletek megadása](./media/resource-manager-core-quotas-request/3-9.png)

10. Ha egynél több helyre vonatkozó kvótát szeretne kérelmezni, akkor a legördülő menüből válassza ki a megfelelő virtuálisgép-típust. Ezután megadhatja a kívánt új korlátozásokat.

![Részletek megadása](./media/resource-manager-core-quotas-request/3-10.png)

11. A kívánt kvóta megadása után kattintson a **Mentés gombra, és folytassa** a kvóta részletei panelen a támogatási kérelem létrehozásával folytatott folytatáshoz.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>A helyszíni kvóta korlátjának korlátozása minden virtuálisgép-sorozatra a használat és a kvóta panelen

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
 
7.  A kiválasztott helyen válassza a **Type** Value as **"spot" lehetőséget.** A **Type (típus** ) mezőben a standard és a direktszín típusú kvótákat egyetlen támogatási esetben is kérheti a többszörös kijelölés támogatásával. További információk a **standard kvóta virtuális gépenként való növeléséről** ezen az [oldalon](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Adja meg az előfizetéshez tartozó új korlátot.

  ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Ha egynél több helyre vonatkozó kvótát szeretne kérelmezni, akkor a legördülő menüből válassza ki a megfelelő **virtuálisgép-** típust. Ezután megadhatja a kívánt új korlátozásokat.

  ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. A kívánt kvóta megadása után kattintson a **Mentés gombra, és folytassa** a kvóta részletei panelen a támogatási kérelem létrehozásával folytatott folytatáshoz.


