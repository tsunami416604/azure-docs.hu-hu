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
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: bc0455b34ebdcb080e0cd702e58d8163228b60ff
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278069"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Kezelését és kérését kvóták az Azure-erőforrásokhoz

Mint Azure-szolgáltatásokat, korlátozva van bizonyos az Azure Machine Learning szolgáltatáshoz kapcsolódó erőforrások. Ezek a korlátok az olyan munkaterületek számától függenek, amelyekkel a modell képzéséhez vagy következtetéséhez vagy pontozásához használt tényleges számítási kapacitás korlátaira korlátozható. 

Ez a cikk áttekintést nyújt a további részleteket az előfizetéshez tartozó különböző Azure-erőforrások előre konfigurált korlátait, valamint kérelem kvóta fejlesztései erőforrás minden típusú hasznos hivatkozásokat is tartalmaz. Ezek a korlátok a költségvetés túlzott futtatások miatt csalás megelőzése érdekében, és tartsa tiszteletben az Azure kapacitásbeli megszorításokon kerüljenek.

Tartsa szem előtt ezeket a kvótákat, amikor tervezési és vertikális Felskálázási Azure Machine Learning szolgáltatási erőforrásait az éles számítási feladatokhoz. Ha például a fürt nem éri el a megadott számú csomópontot, akkor lehet, hogy elérte az előfizetéséhez Azure Machine Learning számítási magokra vonatkozó korlátot. Ha szeretné a határértéket vagy meghaladja az alapértelmezett kvóta emelése, nyissa meg egy online támogatási kérést díjmentesen. A korlátok nem léptethető feletti Azure-kapacitásokhoz megkötések miatt az alábbi táblázatban látható a maximális határértéket. Ha egyetlen oszlop sincs maximális korlátot, az erőforrás állítható korlátok nem rendelkezik.

## <a name="special-considerations"></a>Különleges szempontok

+ Kvóta kreditjét, nem kapacitás garantálja. Ha nagyméretű kapacitásigények, forduljon az Azure ügyfélszolgálatához.

+ A kvóta az előfizetésekben, beleértve az Azure Machine Learning szolgáltatás közösen használja az összes szolgáltatás. Az egyetlen kivétel Azure Machine Learning számítás, amely az alapszintű számítási kvótától eltérő kvótával rendelkezik. Ügyeljen arra, hogy a kvótahasználat kiszámítása az összes szolgáltatásban a kapacitásigények kiértékelése során.

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
+ A dedikált magok régiónként alapértelmezett korlátja 24-300, az előfizetési ajánlat típusától függően.  Az előfizetésenként dedikált magok száma növelhető. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

+ Az alacsony prioritású magok régiónként alapértelmezett korlátja 24-300, az előfizetési ajánlat típusától függően.  Előfizetésenként az alacsony prioritású Processzormagok száma növelhető. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

+ Fürt régiónként rendelkezik, egy alapértelmezett korlát 100 és legfeljebb 200. Ha meghaladja ezt a korlátot növelni szeretné, lépjen kapcsolatba az Azure-támogatás.

+ Nincsenek **más szigorú korlátok** amely nem lépik egyszer találati.

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| Az adott erőforráscsoport esetében maximális munkaterületek | 800 |
| Egyetlen Azure Machine Learning Compute (AmlCompute) erőforrást a csomópontok maximális száma | 100 csomópont |
| Maximális GPU MPI folyamatok száma csomópontonként | 1 – 4 |
| Csomópontonkénti maximális GPU-feldolgozók | 1 – 4 |
| Maximális élettartama | 90 nap<sup>1</sup> |
| Maximális feladat élettartama alacsony prioritású csomóponton | 1 nap<sup>2</sup> |
| Csomópontonkénti maximális paraméter-kiszolgálók | 1 |

<sup>1</sup> maximális élettartamát hivatkozik, amely egy Futtatás elindításához, és a Befejezés után. Befejezett futtatások korlátlan ideig megőrződnek; a maximális élettartamon belül nem befejezett futtatások adatait nem érhető el.
<sup>2</sup> az alacsony prioritású csomóponton lévő feladatok bármikor kiszolgálása kimarad, ha van kapacitási korlátozás. Javasoljuk, hogy az ellenőrzőpontot implementálja a feladatokban.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning folyamatok
Azure Machine Learning folyamatok esetében a folyamat lépéseinek száma, valamint az előfizetésben régiónként megjelenő közzétett folyamatok ütemezett futtatásának száma.
- A folyamatokban engedélyezett lépések maximális száma 30 000
- Az ütemezett futtatások és a blob-lekérések összegének maximális száma az előfizetéshez tartozó közzétett folyamatokra vonatkozóan, havonta 100 000

> [!NOTE]
> Ha szeretné megnövelni ezt a korlátot, forduljon a [Microsoft ügyfélszolgálatahoz](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Container Instances szolgáltatásban

Emellett van egy a container Instances szolgáltatásban, regisztrálhat egy adott időszakban (óránként hatóköre) vagy a teljes előfizetés számára vonatkozó határértéket.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Magkvóta korlátozásának még részletesebb és naprakészebb listája, ellenőrizze az Azure kiterjedő kvóta cikk [Itt](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Storage-fiókok, valamint az adott előfizetésben régiónként száma korlátozva van. Az alapértelmezett korlátja 200, és tartalmazza a Standard és prémium szintű Storage-fiókok. Ha egy adott régióban több mint 200 Storage-fiókra van szüksége, az [Azure támogatási szolgálatán](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)keresztül teheti meg a kérést. Az Azure Storage csapata áttekinti az üzleti esetekhez, és jóváhagyhat legfeljebb 250 tárfiókot az adott régió.


## <a name="find-your-quotas"></a>Keresse meg a kvóták

A különböző erőforrások, például a virtuális gépek, tárolási, hálózati, a kvóta megtekintése is egyszerűen az Azure Portalon keresztül.

1. A bal oldali ablaktáblán válassza a **minden szolgáltatás** lehetőséget, majd az általános kategóriában válassza az **előfizetések** lehetőséget.

1. Előfizetések listájából válassza ki az előfizetést, amelynek Ön által keresett kvótát.

   **Van egy kikötést**, kifejezetten a megtekintése az Azure Machine Learning Compute kvótát. Ahogy említettük, a kvóta elkülönül az előfizetéshez, a számítási kvótával.

1. A bal oldali ablaktáblán válassza a **Machine learning szolgáltatás** lehetőséget, majd válasszon ki egy munkaterületet a listából.

1. A következő panelen alatt a **támogatás és hibaelhárítás szakaszhoz** kiválasztása **használat + kvóták** a jelenlegi kvóta korlátozásának emelése és használati megtekintéséhez.

1. Válasszon egy előfizetést a kvótakorlát megtekintéséhez. Ne felejtse el szűrése az Önt érdeklő régióba.


## <a name="request-quota-increases"></a>Kérelem kvótanövelésre

Ha az alapértelmezett korlátnál magasabb korlátot vagy kvótát szeretne növelni, [Nyisson meg egy online ügyfélszolgálati kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) díjmentesen.

A határértékek nem állíthatók be a táblákban megjelenő maximális határérték fölé. Ha nincs maximális korlát, az erőforrás nem rendelkezik állítható korlátokkal. [Ez](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) a cikk ismerteti a kvóta növelését folyamat részletesebben.

Kvótanövelést kérésekor kell válassza ki a kérelmet a kvóta növeléséhez, szemben a szolgáltatást, amely lehet a szolgáltatások, mint a Machine Learning szolgáltatásra vonatkozó kvótáról, a Container Instances szolgáltatásban vagy a tárolási kvótát. Emellett az Azure Machine Learning Compute, egyszerűen kattintson a a **kvóta igénylése** gombra a fenti lépések kvóta megtekintése közben.

> [!NOTE]
> [Az ingyenes próbaverziós előfizetésekben](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak korlátozása vagy a kvóta növekszik. Ha rendelkezik egy [ingyenes próba-előfizetésre](https://azure.microsoft.com/offers/ms-azr-0044p), frissíthet egy [használatalapú](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetés. További információkért lásd: [Azure ingyenes próbaverzió frissítése használatalapú fizetésre](../../billing/billing-upgrade-azure-subscription.md) és [ingyenes próba-előfizetéssel kapcsolatos gyakori kérdések](https://azure.microsoft.com/free/free-account-faq).
