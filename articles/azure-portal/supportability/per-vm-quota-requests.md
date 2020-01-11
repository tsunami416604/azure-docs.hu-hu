---
title: Az Azure-beli virtuálisgép-sorozatokra vonatkozó vCPU-kvóta növelésének igénylése | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan kérheti le a kvóta-korlátot a virtuális gépek vCPU.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897246"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standard kvóta: korlátok megemelése virtuálisgép-sorozat szerint

A Azure Resource Manager két típusú vCPU-kvótát támogat a virtuális gépekhez:
* Az utólagos elszámolású *virtuális gépek* és a *fenntartott virtuálisgép-példányok* *standard vCPU-kvóta*alá esnek.
* A *helyszíni virtuális gépekre* a *helyszínen vCPU kvóta*vonatkozik. 

Az utólagos elszámolású és a fenntartott VM-példányokra vonatkozó standard vCPU-kvóta az egyes régiókban található minden egyes előfizetés esetében két szinten érvényes:
* Az első szint a *regionális vCPU teljes korlátja* (az összes virtuálisgép-sorozaton belül).
* A második réteg a virtuálisgép *-sorozat vCPU korlátja* (például a Dv3 sorozat vCPU). 

Amikor új helyszíni virtuális gépet telepít, az adott virtuálisgép-sorozat összes új és meglévő vCPU-használata nem lépheti túl az adott virtuálisgép-sorozat jóváhagyott vCPU-kvótáját. Emellett az összes virtuálisgép-sorozatban üzembe helyezett új és meglévő vCPU teljes száma nem haladhatja meg az előfizetés teljes jóváhagyott regionális vCPU vonatkozó kvótáját. Ha túllépi a kvóták valamelyikét, a virtuális gép üzembe helyezése nem engedélyezett.

A virtuálisgép-sorozat vCPU-kvótájának növekedését a Azure Portal használatával kérheti le. A virtuálisgép-sorozat kvótájának növekedése automatikusan megnöveli a teljes regionális vCPU-korlátot ugyanazzal az összeggel. 

A standard vCPU-kvótákkal kapcsolatos további információkért lásd a [virtuális gépek vCPU kvótáit](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és az [Azure-előfizetések és-szolgáltatások korlátozásait](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Ha szeretné megtudni, hogyan növelhető a vCPU korlátja régiónként a standard kvóta alapján, tekintse meg a [standard kvóta: korlátok növelése régiónként](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)című témakört. 

Ha többet szeretne megtudni a virtuális gépek vCPU korlátainak növeléséről, tekintse meg a következőt [: a helyszíni kvóta: az összes virtuálisgép-sorozat korlátainak növelése](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

A következő szakaszokban leírtak szerint a virtuálisgép-sorozatokban a standard vCPU-kvóta korlátozásait kétféleképpen lehet megemelni.

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>A "Súgó + támogatás" panel szabványos kvótájának növelését kéri

Ha a **Súgó + támogatás** ablaktáblán a szabványos vCPU-kvóta növelését szeretné elvégezni, tegye a következőket: 

> [!NOTE]
> Egyetlen támogatási esettel több régió esetében is igényelhet kvóta-korlátozást. Részletekért lásd: 8. lépés.

1. A [Azure Portal](https://portal.azure.com)bal oldali ablaktábláján kattintson a **Súgó és támogatás**elemre.

   ![A "Súgó + támogatás" hivatkozás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. A **Súgó + támogatás** ablaktáblán válassza az **új támogatási kérelem**lehetőséget. 

    ![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. A **probléma típusa** legördülő listában válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.

   ![A "probléma típusa" legördülő lista](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Az **előfizetés** legördülő listában válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Az "előfizetés" legördülő lista](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. A **kvóta típusa** legördülő listában válassza a **számítási-VM (magok-vCPU) előfizetési korlát növekszik**lehetőséget. 

   ![A "kvóta típusa" legördülő lista](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. A **részletek** lap **probléma részletei**területén válassza a **részletek**megadása lehetőséget, majd adjon meg további információkat a kérelem feldolgozásához.

   ![A "részletek megadása" hivatkozás](./media/resource-manager-core-quotas-request/provide-details.png)

1. A jobb felső sarokban található **kvóta részletei** panelen tegye a következőket:

   ![A "kvóta részletei" panel](./media/resource-manager-core-quotas-request/1-7.png)

   a. A **telepítési modell** legördülő listában válassza ki a megfelelő modellt.

   b. A **helyek** legördülő listában válasszon ki egy helyet. A kiválasztott helyhez a **types** **(típus**kiválasztása) mezőben adja meg a **standard**értéket.

   ![A "kvóta részletei" panel – kvóta típusai](./media/resource-manager-core-quotas-request/1-8.png)

   A **types (típusok**) területen a standard és a direktszín típusú kvótákat egyetlen támogatási esetről is kérheti a többszörös kijelölés támogatásával.
   
   További információ a helyszíni kvóták korlátainak növeléséről: Azure-beli [virtuális gépek a virtuálisgép-méretezési csoportokhoz](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   c. A **standard** legördülő listában válassza ki azt az SKU-sorozatot, amelynek a kvótáit szeretné bővíteni.

   ![A "kvóta részletei" panel – SKU sorozat](./media/resource-manager-core-quotas-request/1-9.png)

   d. Adja meg az előfizetéshez használni kívánt új kvóta-korlátozásokat. Ha el szeretne távolítani egy SKU-t a listából, törölje az SKU melletti jelölőnégyzet jelölését, vagy válassza a **Törlés** (X) ikont. 

   ![Az "új vCPU korlát" szövegmező](./media/resource-manager-core-quotas-request/1-10.png)

1. Ha egynél több helyre szeretne kvótát emelni, válasszon egy további helyet a legördülő listából, majd válasszon ki egy megfelelő virtuálisgép-típust. Ezután megadhat egy korlátot, amely a további helyre vonatkozik.

   ![További helyszínek a "kvóta részletei" panelen](./media/resource-manager-core-quotas-request/1-11.png)
   
1. Válassza a mentés lehetőséget, **és** folytassa a támogatási kérelem létrehozásával.

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>A standard kvóta növelésének kérése az "előfizetések" panelen

Ha az **előfizetések** ablaktáblán a standard vCPU-kvóta növelését szeretné elvégezni, tegye a következőket:

> [!NOTE]
> Egyetlen támogatási esettel több régió esetében is igényelhet kvóta-korlátozást. Részletekért lásd: 7. lépés.

1. A [Azure Portal](https://portal.azure.com)bal oldali ablaktábláján válassza az **előfizetések**lehetőséget.

   ![Az "előfizetések" hivatkozás](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Az "előfizetések" panel](./media/resource-manager-core-quotas-request/select-subscription.png)

1. A **\<előfizetés neve >** oldal bal oldali ablaktábláján válassza a **használat + kvóták**lehetőséget.

   ![A "használat + kvóták" hivatkozás](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. A jobb felső sarokban válassza a **kérelem növekedés**lehetőséget.

   ![Kérések növekedése](./media/resource-manager-core-quotas-request/request-increase.png)

1. A **kvóta típusa** legördülő listában válassza a **számítási-VM (magok-vCPU) előfizetési korlát növekszik**lehetőséget.

   ![A "kvóta típusa" legördülő lista](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
1. A jobb felső sarokban található **kvóta részletei** panelen tegye a következőket:

   ![A "kvóta részletei" panel](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. A **telepítési modell** legördülő listában válassza ki a megfelelő modellt.

   b. A **helyek** legördülő listában válasszon ki egy helyet. 
   
   c. A kiválasztott helyen a **típusok**területen válassza a **típus kiválasztása**lehetőséget, majd jelölje be a **szabványos** jelölőnégyzetet.

   ![A "standard" jelölőnégyzet](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   A **types (típusok**) területen a standard és az alacsony prioritású kvótákat is kérheti egyetlen támogatási esetről a többszörös kijelölés támogatásával.
   
   További információ a helyszíni kvóták korlátainak növeléséről: Azure-beli [virtuális gépek a virtuálisgép-méretezési csoportokhoz](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   d. A **standard** legördülő listában válassza ki azt az SKU-sorozatot, amelynek a kvótáit szeretné bővíteni.

   ![A "kvóta részletei" panel – SKU sorozat](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. Adja meg az előfizetéshez használni kívánt új kvóta-korlátozásokat. Ha el szeretne távolítani egy SKU-t a listából, törölje az SKU melletti jelölőnégyzet jelölését, vagy válassza a **Törlés** (X) lehetőséget. 

   ![Az "új vCPU korlát" szövegmező](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. Ha egynél több helyre szeretne kvótát emelni, válasszon egy további helyet a legördülő listából, majd válasszon ki egy megfelelő virtuálisgép-típust. 

   Ezzel a lépéssel előre betöltődik a korábbi helyszínekhez kiválasztott SKU-sorozat. Adja meg a további adatsorozatra alkalmazni kívánt kvóták korlátait.
   
   ![További helyszínek a "kvóta részletei" panelen](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. Válassza a mentés lehetőséget, **és** folytassa a támogatási kérelem létrehozásával.
