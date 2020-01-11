---
title: Az Azure Regional vCPU-kvóták korlátainak növelését kéri | Microsoft Docs
description: Regionális kvóta-növelési kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896739"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standard kvóta: korlátok növekedése régiónként 

A Azure Resource Manager két típusú vCPU-kvótát támogat a virtuális gépekhez:
* Az utólagos elszámolású *virtuális gépek* és a *fenntartott virtuálisgép-példányok* *standard vCPU-kvóta*alá esnek.
* A *helyszíni virtuális gépekre* a *helyszínen vCPU kvóta*vonatkozik. 

Az utólagos elszámolású és a fenntartott VM-példányokra vonatkozó standard vCPU-kvóta az egyes régiókban található minden egyes előfizetés esetében két szinten érvényes:
* Az első szint a *regionális vCPU teljes korlátja* (az összes virtuálisgép-sorozaton belül).
* A második réteg a virtuálisgép *-sorozat vCPU korlátja* (például a D sorozat vCPU).
 
Amikor új helyszíni virtuális gépet telepít, az adott virtuálisgép-sorozat összes új és meglévő vCPU-használata nem lépheti túl az adott virtuálisgép-sorozat jóváhagyott vCPU-kvótáját. Emellett az összes virtuálisgép-sorozatban üzembe helyezett új és meglévő vCPU teljes száma nem haladhatja meg az előfizetés teljes jóváhagyott regionális vCPU vonatkozó kvótáját. Ha túllépi a kvóták valamelyikét, a virtuális gép üzembe helyezése nem engedélyezett. 

A virtuálisgép-sorozat vCPU-kvótájának növekedését a Azure Portal használatával kérheti le. A virtuálisgép-sorozat kvótájának növekedése automatikusan megnöveli a teljes regionális vCPU-korlátot ugyanazzal az összeggel.

Ha új előfizetést hoz létre, akkor előfordulhat, hogy a regionális vCPU alapértelmezett száma nem egyezik meg az összes egyedi virtuálisgép-sorozat alapértelmezett vCPU-kvótájának összegével. Ez az eltérés olyan előfizetést eredményezhet, amely elegendő kvótával rendelkezik minden egyes telepíteni kívánt virtuálisgép-sorozathoz. Előfordulhat azonban, hogy nem áll rendelkezésre elegendő kvóta az összes üzemelő példány regionális vCPU befogadásához. Ebben az esetben kérelmet kell benyújtania, hogy explicit módon növelje a regionális vCPU teljes számának korlátját. A regionális vCPU teljes korlátja nem lépheti túl a régió összes virtuálisgép-sorozatának jóváhagyott teljes kvótáját.

A standard vCPU-kvótákkal kapcsolatos további információkért lásd a [virtuális gépek vCPU kvótáit](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és az [Azure-előfizetések és-szolgáltatások korlátozásait](https://aka.ms/quotalimits).

Ha többet szeretne megtudni a virtuális gépek vCPU korlátainak növeléséről, tekintse meg a következőt [: a helyszíni kvóta: az összes virtuálisgép-sorozat korlátainak növelése](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

A következő szakaszokban leírtak szerint a vCPU standard kvóta korlátozását két módon, régiónként is megnövelheti.

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>Kvóta növelésének kérése régiónként a "Súgó + támogatás" panelen

Ha a **Súgó + támogatás** ablaktáblán régió alapján szeretne vCPU-kvótát emelni, tegye a következőket: 

1. A [Azure Portal](https://portal.azure.com)bal oldali ablaktábláján kattintson a **Súgó és támogatás**elemre.

   ![A "Súgó + támogatás" hivatkozás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. A **Súgó + támogatás** ablaktáblán válassza az **új támogatási kérelem**lehetőséget. 

    ![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. A **probléma típusa** legördülő listában válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.

   ![A "probléma típusa" legördülő lista](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Az **előfizetés** legördülő listában válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Az "előfizetés" legördülő lista](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. A **kvóta típusa** legördülő listában válassza az **egyéb kérelmek**elemet.

   ![A "kvóta típusa" legördülő lista](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. A **probléma részletei** ablaktáblában, a **Leírás** mezőben adja meg a következő további információkat: 

    a. A **telepítési modellhez**a **Resource Managert**kell megadni.  
    b. A **régió**mezőben adja meg a szükséges régiót (például: **USA 2. keleti**régiója).  
    c. Az **új korlát**beállításnál a régióhoz új vCPU-korlátot kell megadni. Ez az érték nem haladhatja meg az előfizetéshez tartozó egyedi SKU-sorozatokra vonatkozó jóváhagyott kvóták összegét.

    ![A "probléma részletei" panel](./media/resource-manager-core-quotas-request/regional-details.png)

1. Válassza a mentés lehetőséget, **és** folytassa a támogatási kérelem létrehozásával.

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>Kvóta növelésének kérése régiónként az "előfizetések" panelen

Ha a vCPU kvóta növelését az **előfizetések** panelről származó régió alapján szeretné kérni, tegye a következőket: 

1. A [Azure Portal](https://portal.azure.com)bal oldali ablaktábláján válassza az **előfizetések**lehetőséget.

   ![Az "előfizetések" hivatkozás](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Az "előfizetések" panel](./media/resource-manager-core-quotas-request/select-subscription.png)

1. A **\<előfizetés neve >** oldal bal oldali ablaktábláján válassza a **használat + kvóták**lehetőséget.

   ![A "használat + kvóták" hivatkozás](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. A jobb felső sarokban válassza a **kérelem növekedés**lehetőséget.

   ![Kérések növekedése](./media/resource-manager-core-quotas-request/request-increase.png)

1. A **kvóta típusa** legördülő listában válassza az **egyéb kérelmek**elemet.

   ![A "kvóta típusa" legördülő lista](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. A **probléma részletei** ablaktáblában, a **Leírás** mezőben adja meg a következő további információkat: 

    a. A **telepítési modellhez**a **Resource Managert**kell megadni.  
    b. A **régió**mezőben adja meg a szükséges régiót (például: **USA 2. keleti**régiója).  
    c. Az **új korlát**beállításnál a régióhoz új vCPU-korlátot kell megadni. Ez az érték nem haladhatja meg az előfizetéshez tartozó egyedi SKU-sorozatokra vonatkozó jóváhagyott kvóták összegét.

    ![A "probléma részletei" panel](./media/resource-manager-core-quotas-request/regional-details.png)

1. Válassza a mentés lehetőséget, **és** folytassa a támogatási kérelem létrehozásával.

