---
title: Kezelését és kérését erőforráskvóták
titleSuffix: Azure Machine Learning service
description: Ez az útmutató ismerteti a különböző a kvóták az erőforrásokat az Azure Machine Learning és a megtekintése, és további kvótát.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b735df4e245d6e3ceebd847e91ed7dfdb255c267
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245990"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Kezelését és kérését kvóták az Azure-erőforrásokhoz

Mint Azure-szolgáltatásokat, korlátozva van bizonyos az Azure Machine Learning szolgáltatáshoz kapcsolódó erőforrások. Ezek a korlátok a korlát a tényleges számítási, amely lekérdezi a korlátok hozhat létre a munkaterületek számának terjedhet használt betanítási vagy következtetési modelljeit. Ez a cikk áttekintést nyújt a további részleteket az előfizetéshez tartozó különböző Azure-erőforrások előre konfigurált korlátait, valamint kérelem kvóta fejlesztései erőforrás minden típusú hasznos hivatkozásokat is tartalmaz. Ezek a korlátok a költségvetés túlzott futtatások miatt csalás megelőzése érdekében, és tartsa tiszteletben az Azure kapacitásbeli megszorításokon kerüljenek.

Kvóta ne feledje, tervezési és vertikális felskálázási az Azure Machine Learning-szolgáltatások erőforrásai a termelési számítási feladatokhoz. Például ha a fürt nem éri el a csomópontok megadott célszámát, majd előfordulhat, hogy elérte az Azure Machine Learning COMPUTE számítási magok korlát az előfizetéshez tartozó. Ha szeretné a határértéket vagy meghaladja az alapértelmezett kvóta emelése, nyissa meg egy online támogatási kérést díjmentesen. A korlátok nem léptethető feletti Azure-kapacitásokhoz megkötések miatt az alábbi táblázatban látható a maximális határértéket. Ha egyetlen oszlop sincs maximális korlátot, az erőforrás állítható korlátok nem rendelkezik.

## <a name="special-considerations"></a>Különleges szempontok

+ Kvóta kreditjét, nem kapacitás garantálja. Ha nagyméretű kapacitásigények, forduljon az Azure ügyfélszolgálatához.

+ A kvóta az előfizetésekben, beleértve az Azure Machine Learning szolgáltatás közösen használja az összes szolgáltatás. Az egyetlen kivétel, az Azure Machine Learning számítási, amely a számítási magkvóta a külön kvótával rendelkezik. Ügyeljen arra, hogy a kvótahasználat kiszámítása az összes szolgáltatásban a kapacitásigények kiértékelése során.

+ Alapértelmezett korlátok régiónként eltérő ajánlat kategóriája, például az ingyenes próba, használatalapú fizetés és sorozat, mint a Dv2, F, G, és így tovább.

## <a name="default-resource-quotas"></a>Alapértelmezett erőforráskvóták

Íme egy bontása a kvótát, az Azure-előfizetésen belül a különböző erőforrástípusok szerint.

> [!Important]
> Korlátok változhatnak. A legújabb mindig találhatók a szolgáltatásiszint-kvóta [dokumentum](https://docs.microsoft.com/azure/azure-subscription-service-limits/) az összes Azure.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
A szolgáltatások között, vagy olyan egyedülálló módon üzembe helyezése az Azure-előfizetéssel, a virtuális gépek száma korlátozva van. A régió szinten, mind a magok száma összesen és is alapon kiszolgálónként család el ezt a korlátot.

Fontos kiemelni, hogy virtuálisgép-magokra egy régió szerinti teljes korlát és a egy régió szerinti külön-külön kényszerített sorozat (Dv2, F stb.) maximális mérete. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés üzembe helyezhet 30 A1 virtuális gépet, 30 D1 virtuális gépet, vagy a kettő valamilyen kombinációját, amely nem haladja meg az összesen 30 magot (pl. 10 A1 virtuális gép és 20 D1 virtuális gép).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Magkvóta korlátozásának még részletesebb és naprakészebb listája, ellenőrizze az Azure kiterjedő kvóta cikk [Itt](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Az Azure Machine Learning Compute
Az Azure Machine Learning Compute a magok számát és a egy előfizetésben régiónként engedélyezett egyedi számítási erőforrások számát az alapértelmezett kvóta korlátozva van. Ez a kvóta a Virtuálisgép-magkvóta fenti elkülönül, és a core korlátok nem megosztott jelenleg két erőforrástípusok.

Rendelkezésre álló erőforrások:
+ Dedikált magok száma a régióban van egy alapértelmezett korlát a 10-24.  Az előfizetésenként dedikált magok száma növelhető. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

+ Alacsony prioritású magok régiónként legfeljebb alapértelmezett 10 – 24.  Előfizetésenként az alacsony prioritású Processzormagok száma növelhető. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

+ Fürt régiónként rendelkezik, egy alapértelmezett korlát 100 és legfeljebb 200. Ha meghaladja ezt a korlátot növelni szeretné, lépjen kapcsolatba az Azure-támogatás.

+ Nincsenek **más szigorú korlátok** amely nem lépik egyszer találati.

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| Az adott erőforráscsoport esetében maximális munkaterületek | 800 |
| Egyetlen Azure Machine Learning Compute (AmlCompute) erőforrást a csomópontok maximális száma | 100 csomópont |
| Maximális GPU MPI folyamatok száma csomópontonként | 1 – 4 |
| Csomópontonkénti maximális GPU-feldolgozók | 1 – 4 |
| Maximális élettartama | 7 nap<sup>1</sup> |
| Csomópontonkénti maximális paraméter-kiszolgálók | 1 |

<sup>1</sup> maximális élettartamát hivatkozik, amely egy Futtatás elindításához, és a Befejezés után. Befejezett futtatások korlátlan ideig megőrződnek; a maximális élettartamon belül nem befejezett futtatások adatait nem érhető el.

### <a name="container-instances"></a>Container Instances szolgáltatásban

Emellett van egy a container Instances szolgáltatásban, regisztrálhat egy adott időszakban (óránként hatóköre) vagy a teljes előfizetés számára vonatkozó határértéket.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Magkvóta korlátozásának még részletesebb és naprakészebb listája, ellenőrizze az Azure kiterjedő kvóta cikk [Itt](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Storage-fiókok, valamint az adott előfizetésben régiónként száma korlátozva van. Az alapértelmezett korlátja 200, és tartalmazza a Standard és prémium szintű Storage-fiókok. Ha egy adott régióban több mint 200 tárfiókra van szüksége, győződjön meg a kérést [Azure-támogatási](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Az Azure Storage csapata áttekinti az üzleti esetekhez, és jóváhagyhat legfeljebb 250 tárfiókot az adott régió.


## <a name="find-your-quotas"></a>Keresse meg a kvóták

A különböző erőforrások, például a virtuális gépek, tárolási, hálózati, a kvóta megtekintése is egyszerűen az Azure Portalon keresztül.

1. A bal oldali panelén válassza **minden szolgáltatás** majd **előfizetések** az általános kategória alapján.

1. Előfizetések listájából válassza ki az előfizetést, amelynek Ön által keresett kvótát.

   **Van egy kikötést**, kifejezetten a megtekintése az Azure Machine Learning Compute kvótát. Ahogy említettük, a kvóta elkülönül az előfizetéshez, a számítási kvótával.

1. A bal oldali panelen válassza ki a **Machine Learning szolgáltatás** , majd a megjelenő listában minden munkaterület

1. A következő panelen alatt a **támogatás és hibaelhárítás szakaszhoz** kiválasztása **használat + kvóták** a jelenlegi kvóta korlátozásának emelése és használati megtekintéséhez.

1. Válasszon egy előfizetést a kvótakorlát megtekintéséhez. Ne felejtse el szűrése az Önt érdeklő régióba.


## <a name="request-quota-increases"></a>Kérelem kvótanövelésre

Ha azt szeretné, a korlát vagy az alapértelmezett korlát feletti kvóta emelése [nyisson meg egy támogatási kérést online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) díjmentesen.

A korlátok nem léptethető a fenti táblázatokban látható a maximális határértéket. Ha nincs maximális korlát, az erőforrás állítható korlátok nem rendelkezik. [Ez](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) a cikk ismerteti a kvóta növelését folyamat részletesebben.

Kvótanövelést kérésekor kell válassza ki a kérelmet a kvóta növeléséhez, szemben a szolgáltatást, amely lehet a szolgáltatások, mint a Machine Learning szolgáltatásra vonatkozó kvótáról, a Container Instances szolgáltatásban vagy a tárolási kvótát. Emellett az Azure Machine Learning Compute, egyszerűen kattintson a a **kvóta igénylése** gombra a fenti lépések kvóta megtekintése közben.

> [!NOTE]
> [Az ingyenes próbaverziós előfizetésekben](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak korlátozása vagy a kvóta növekszik. Ha rendelkezik egy [ingyenes próba-előfizetésre](https://azure.microsoft.com/offers/ms-azr-0044p), frissíthet egy [használatalapú](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetés. További információkért lásd: [Azure ingyenes próbaverzió frissítése használatalapú fizetésre](../../billing/billing-upgrade-azure-subscription.md) és [ingyenes próba-előfizetéssel kapcsolatos gyakori kérdések](https://azure.microsoft.com/free/free-account-faq).
