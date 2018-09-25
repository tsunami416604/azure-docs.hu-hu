---
title: Hogyan kezelheti és kvóták az Azure Machine Learning szolgáltatás kérése
description: Ez az útmutató ismerteti a különböző a kvóták az erőforrásokat az Azure Machine Learning és a megtekintése, és további kvótát.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 9/24/2018
ms.openlocfilehash: 0ee0a29be6a313e09cd77051dc29bc0131b57a40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997811"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Kezelését és kérését kvóták az Azure-erőforrásokhoz

Mint Azure-szolgáltatásokat, korlátozva van bizonyos az Azure Machine Learning szolgáltatáshoz kapcsolódó erőforrások. Ezek a korlátok a korlát a tényleges számítási, amely lekérdezi a korlátok hozhat létre a munkaterületek számának terjedhet használt betanítási vagy következtetési modelljeit. Ez a cikk áttekintést nyújt a további részleteket az előfizetéshez tartozó különböző Azure-erőforrások előre konfigurált korlátait, valamint kérelem kvóta fejlesztései erőforrás minden típusú hasznos hivatkozásokat is tartalmaz.

Kvóta ne feledje, tervezési és vertikális felskálázási az Azure Machine Learning-erőforrások. Például ha a fürt nem éri el a csomópontok megadott célszámát, majd előfordulhat, hogy elérte egy Batch AI magos korlát az előfizetéshez. Ha azt tervezi, éles számítási feladatok futtatása a Batch AI, szükség lehet egy vagy több az alapértelmezettnél eggyel a kvóták növelése érdekében. Ha szeretné a határértéket vagy meghaladja az alapértelmezett kvóta emelése, nyissa meg egy online támogatási kérést díjmentesen. A korlátok nem léptethető fent a maximális határértéket, az alábbi táblázatban látható. Ha egyetlen oszlop sincs maximális korlátot, az erőforrás állítható korlátok nem rendelkezik. 

## <a name="special-considerations"></a>Különleges szempontok

+ Kvóta kreditjét, nem kapacitás garantálja. Ha nagyméretű kapacitásigények, forduljon az Azure ügyfélszolgálatához.

+ A kvóta közösen használja az összes szolgáltatás, a Batch AI, kivéve az előfizetésében, az Azure Machine Learning egyik folyamatban. Ügyeljen arra, hogy a kvótahasználat kiszámítása az összes szolgáltatásban a kapacitásigények kiértékelése során.

+ Alapértelmezett korlátok régiónként eltérő ajánlat kategóriája, például az ingyenes próba, használatalapú fizetés és sorozat, mint a Dv2, F, G, és így tovább.

## <a name="default-resource-quotas"></a>Alapértelmezett erőforráskvóták

Íme egy bontása a kvótát, az Azure-előfizetésen belül a különböző erőforrástípusok szerint. 

> [!Important]
> Korlátok változhatnak. A legújabb mindig találhatók a szolgáltatásiszint-kvóta [dokumentum](https://docs.microsoft.com/azure/azure-subscription-service-limits/) az összes Azure.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek) 
A szolgáltatások között, vagy olyan egyedülálló módon üzembe helyezése az Azure-előfizetéssel, a virtuális gépek száma korlátozva van. A régió szinten, mind a magok száma összesen és is alapon kiszolgálónként család el ezt a korlátot.

Fontos kiemelni, hogy virtuálisgép-magokra egy régió szerinti teljes korlát és a egy régió szerinti külön-külön kényszerített sorozat (Dv2, F stb.) maximális mérete. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés üzembe helyezhet 30 A1 virtuális gépet, 30 D1 virtuális gépet, vagy a kettő valamilyen kombinációját, amely nem haladja meg az összesen 30 magot (pl. 10 A1 virtuális gép és 20 D1 virtuális gép).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Magkvóta korlátozásának még részletesebb és naprakészebb listája, ellenőrizze az Azure kiterjedő kvóta cikk [Itt](https://docs.microsoft.com/azure/azure-subscription-service-limits#subscription-limits-1).

### <a name="batch-ai-clusters"></a>Batch AI-fürtök
A Batch AI a magok számát és a egy előfizetésben régiónként engedélyezett a fürtök számának alapértelmezett kvóta korlátozva van. Batch AI kvóta a Virtuálisgép-magkvóta fenti elkülönül, és a core korlátok nem megosztott jelenleg két erőforrástípusok.

Rendelkezésre álló erőforrások:
+ Dedikált magok száma a régióban van egy alapértelmezett korlát a 10-24.  A Batch AI előfizetésenként dedikált magok száma növelhető. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

+ Alacsony prioritású magok régiónként legfeljebb alapértelmezett 10 – 24.  Batch AI előfizetésenként alacsony prioritású magok száma növelhető. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

+ Fürtök régiónként legfeljebb 200 és a egy alapértelmezett korlát a 20 rendelkezik. Ha meghaladja ezt a korlátot növelni szeretné, lépjen kapcsolatba az Azure-támogatás.

Magkvóta korlátozásának még részletesebb és naprakészebb listája, ellenőrizze az Azure kiterjedő kvóta cikk [Itt](https://docs.microsoft.com/azure/azure-subscription-service-limits#batch-ai-limits).

### <a name="container-instances"></a>Tárolópéldányok

Emellett van egy a container Instances szolgáltatásban, regisztrálhat egy adott időszakban (óránként hatóköre) vagy a teljes előfizetés számára vonatkozó határértéket.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Magkvóta korlátozásának még részletesebb és naprakészebb listája, ellenőrizze az Azure kiterjedő kvóta cikk [Itt](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Storage-fiókok, valamint az adott előfizetésben régiónként száma korlátozva van. Az alapértelmezett korlátja 200, és tartalmazza a Standard és prémium szintű Storage-fiókok. Ha egy adott régióban több mint 200 tárfiókra van szüksége, győződjön meg a kérést [Azure-támogatási](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Az Azure Storage csapata áttekinti az üzleti esetekhez, és jóváhagyhat legfeljebb 250 tárfiókot az adott régió.


## <a name="find-your-quotas"></a>Keresse meg a kvóták

A különböző erőforrások, például a virtuális gépek, tárolási, hálózati, a kvóta megtekintése is egyszerűen az Azure Portalon keresztül.

1. A bal oldali panelén válassza **minden szolgáltatás** majd **előfizetések** az általános kategória alapján.

1. Előfizetések listájából válassza ki az előfizetést, amelynek Ön által keresett kvótát.

   **Van egy kikötést**, kifejezetten a Batch AI-kvóta megtekintése a. Ahogy említettük, a kvóta elkülönül az előfizetéshez, a számítási kvótával. 
   Batch AI kiválasztása után **minden szolgáltatás**, keresse meg a Batch AI és a szolgáltatás megnyitásához.

1. Alatt **beállítások**válassza **használat + kvóták** a jelenlegi kvóta korlátozásának emelése és használati megtekintéséhez.

1. Válassza ki az előfizetését, a kvótakorlát megtekintéséhez. Ne feledje, a szolgáltatás és az Önt érdeklő régió szűréséhez.


## <a name="request-quota-increases"></a>Kérelem kvótanövelésre

Ha azt szeretné, a korlát vagy az alapértelmezett korlát feletti kvóta emelése [nyisson meg egy támogatási kérést online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) díjmentesen. 

A korlátok nem léptethető a fenti táblázatokban látható a maximális határértéket. Ha nincs maximális korlát, az erőforrás állítható korlátok nem rendelkezik. [Ez](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) a cikk ismerteti a kvóta növelését folyamat részletesebben.

Kvótanövelést kérésekor kell válassza ki a kérelmet a kvóta növeléséhez, szemben a szolgáltatást, amely lehet a szolgáltatások, mint az AzureML-kvóta, a Batch AI-kvóta vagy a tárolási kvótát. 

> [!NOTE]
> [Az ingyenes próbaverziós előfizetésekben](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak korlátozása vagy a kvóta növekszik. Ha rendelkezik egy [ingyenes próba-előfizetésre](https://azure.microsoft.com/offers/ms-azr-0044p), frissíthet egy [használatalapú](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetés. További információkért lásd: [Azure ingyenes próbaverzió frissítése használatalapú fizetésre](../../billing/billing-upgrade-azure-subscription.md) és [ingyenes próba-előfizetéssel kapcsolatos gyakori kérdések](https://azure.microsoft.com/free/free-account-faq).
