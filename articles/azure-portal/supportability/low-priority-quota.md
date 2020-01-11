---
title: Helyszíni virtuális gépek kvótája | Microsoft Docs
description: A kvóták korlátainak növelésével a helyszíni kvóta-kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9567df95e4e66d9c6b82b29cb9bba9adaf39349d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897298"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Helyszíni kvóta: az összes virtuálisgép-sorozat korlátainak emelése

A helyszíni virtuális gépek (VM-EK) az Azure-használat egy másik modelljét nyújtják. Lehetővé teszik, hogy alacsonyabb költségekkel járjon el, ha az Azure-beli virtuális gépeket az utólagos elszámolású vagy fenntartott virtuálisgép-példányok üzembe helyezéséhez igénybe veszi. A helyszíni virtuális gépekkel kapcsolatos további információkért lásd: Azure-beli [virtuális gépek a virtuálisgép-méretezési csoportokhoz](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

A Azure Resource Manager két típusú vCPU-kvótát támogat a virtuális gépekhez:
* Az utólagos elszámolású *virtuális gépek* és a *fenntartott virtuálisgép-példányok* *standard vCPU-kvóta*alá esnek.
* A *helyszíni virtuális gépekre* a *helyszínen vCPU kvóta*vonatkozik. 

A *helyszíni vCPU-kvóta* esetében az erőforrás-kezelő vCPU kvótái az összes elérhető virtuálisgép-sorozatra érvényesek egyetlen regionális korlátként.

Amikor új helyszíni virtuális gépet telepít, az összes új és meglévő vCPU-használat az összes helyszíni virtuálisgép-példány esetében nem haladhatja meg a jóváhagyott helyszíni vCPU-kvóta korlátját. Ha a helyszíni kvóta túllépve, a helyszíni virtuális gép üzembe helyezése nem engedélyezett. 

Ez a cikk azt ismerteti, hogyan lehet megemelni a helyszíni vCPU-kvóta korlátozását a Azure Portal használatával. 

A standard vCPU-kvótákkal kapcsolatos további információkért lásd a [virtuális gépek vCPU kvótáit](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és az [Azure-előfizetések és-szolgáltatások korlátozásait](https://aka.ms/quotalimits). 

Ha többet szeretne megtudni a vCPU-korlát régiónként való növeléséről, tekintse meg a [standard kvóta: regionális vCPU korlátjának növekedését](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)ismertető témakört.

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>Kvóta korlátozásának kérése a "Súgó + támogatás" panelen 

Ha a **Súgó + támogatás** ablaktáblán szeretné megkeresni az összes virtuálisgép-sorozathoz a helyszíni kvóta korlátját, tegye a következőket:

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

1. Válassza a **részletek** fület, majd a **probléma részletei**területen válassza a **részletek**megadása lehetőséget, és adjon meg további információkat a kérelem feldolgozásához.

   ![A "részletek megadása" hivatkozás](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. A jobb felső sarokban található **kvóta részletei** panelen tegye a következőket:

   ![A "kvóta részletei" panel](./media/resource-manager-core-quotas-request/3-7.png)

   a. A **telepítési modell** legördülő listában válassza ki a megfelelő modellt.

   b. A **helyek** legördülő listában válasszon ki egy helyet. A kiválasztott hely **típus**területén, a **típus kiválasztása** mezőben adja meg a **spot**értéket. 
   
   ![Az "új támogatási kérelem" Részletek lap](./media/resource-manager-core-quotas-request/3-8.png)

    A **types (típusok**) területen a standard és a direktszín típusú kvótákat egyetlen támogatási esetről is kérheti a többszörös kijelölés támogatásával. 
    
    További információ [: standard kvóta: virtuálisgép-sorozat vCPU korlátjának növekedése](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   c. Adja meg az előfizetéshez használni kívánt új kvóta-korlátot. 
 
   ![Az "új vCPU korlát" szövegmező](./media/resource-manager-core-quotas-request/3-9.png)

1. Ha egynél több helyre szeretne kvótát emelni, válasszon egy további helyet a legördülő listából, majd válasszon ki egy megfelelő virtuálisgép-típust. Ezután megadhat egy korlátot, amely a további helyre vonatkozik.

   ![További helyszínek a "kvóta részletei" panelen](./media/resource-manager-core-quotas-request/3-10.png)

1. Válassza a mentés lehetőséget, **és** folytassa a támogatási kérelem létrehozásával.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Kvóta korlátozásának kérése az "előfizetések" panelen

Ha az **előfizetések** ablaktáblán az összes virtuálisgép-sorozathoz a helyszíni kvóta korlátozását szeretné igénybe venni, tegye a következőket:

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

   ![A "kvóta részletei" panel](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a. A **telepítési modell** legördülő listában válassza ki a megfelelő modellt.

   b. A **helyek** legördülő listában válasszon ki egy helyet. 
   
   c. A kiválasztott hely **típus**területén, a **típus kiválasztása** mezőben adja meg a **spot**értéket.

   ![A "kvóta részletei" panel](./media/resource-manager-core-quotas-request/3-2-7.png)

   További információ [: standard kvóta: virtuálisgép-sorozat vCPU korlátjának növekedése](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   d. Adja meg az előfizetéshez használni kívánt új kvóta-korlátot.

   ![Az "új vCPU korlát" szövegmező](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. Ha egynél több helyre szeretne kvótát emelni, válasszon egy további helyet a legördülő listából, majd válasszon ki egy megfelelő virtuálisgép-típust. Ezután megadhat egy korlátot, amely a további helyre vonatkozik.

   ![További helyszínek a "kvóta részletei" panelen](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. Válassza a mentés lehetőséget, **és** folytassa a támogatási kérelem létrehozásával.


