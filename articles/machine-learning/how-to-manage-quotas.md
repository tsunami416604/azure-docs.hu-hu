---
title: Erőforrások kezelése & kvóták
titleSuffix: Azure Machine Learning
description: Ismerje meg a Azure Machine Learning erőforrásokra vonatkozó kvótákat, valamint a további kvóták igénylését.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: 4b072257d49011819fe19d6e2901560df43b26dc
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275563"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Erőforrások kezelése & az erőforrásokra vonatkozó kvóták növelése Azure Machine Learning

Az Azure korlátozza a korlátozásokat és a kvótákat, így megakadályozva, hogy csalás miatt fusson a költségvetés, valamint az Azure-kapacitás korlátozásait. Ezeket a korlátokat az éles számítási feladatokra való skálázás során érdemes figyelembe venni. Ebből a cikkből megismerheti a következőket:

> [!div class="checklist"]
> + Az [Azure Machine Learninghoz](overview-what-is-azure-ml.md)kapcsolódó Azure-erőforrásokra vonatkozó alapértelmezett korlátok.
> + Megtekintheti a kvótákat és a korlátozásokat.
> + Munkaterület-szintű kvóták létrehozása
> + A kérelem kvótája növekszik.
> + Magánhálózati végpontok és DNS-kvóták.

A kvóták kezelése mellett azt is megtudhatja, hogyan [tervezheti meg a Azure Machine learning költségeinek & kezelését](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Különleges szempontok

+ A kvóta egy hitelkeret, nem pedig a kapacitási garancia. Ha nagyobb kapacitásra van szüksége, [forduljon az Azure támogatási szolgálatához, és növelje a kvótát](#request-quota-increases).

+ A kvóta az előfizetések összes szolgáltatása között meg van osztva, beleértve a Azure Machine Learning. Kiszámítja az összes szolgáltatás kihasználtságát a kapacitás kiértékelése során.
    + Azure Machine Learning a számítás kivételt jelent, és az alapvető számítási kvóta külön kvótával rendelkezik. 

+ Az alapértelmezett korlátok az ajánlati kategória típusa szerint változnak, például az ingyenes próbaverzió, az utólagos elszámolású és a virtuális gép (VM) sorozata, például Dv2, F, G stb.

## <a name="default-resource-quotas"></a>Alapértelmezett erőforrás-kvóták

Ebben a szakaszban az alábbi erőforrásokra vonatkozó alapértelmezett és maximális kvóta-korlátozásokat ismerheti meg:

+ Virtual machines (Virtuális gépek)
+ Azure Machine Learning Compute
+ Azure Machine Learning folyamatok
+ Tárolópéldányok
+ Storage

> [!IMPORTANT]
> A korlátozások változhatnak. A legújabbak mindig az összes Azure szolgáltatási szintű kvóta [dokumentumában](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) találhatók.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Minden Azure-előfizetés korlátozza a virtuális gépek számát az összes szolgáltatáson belül. A virtuális gépek magjai a regionális teljes korláttal és a méretnél (Dv2, F stb.) regionális korláttal rendelkeznek. Mindkét korlátot külön kényszeríti a rendszer.

Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés 30 a1-es virtuális gép vagy 30 D1 virtuális gép üzembe helyezését, illetve a kettő olyan kombinációját teszi lehetővé, amely nem haladja meg az összesen 30 magot.

A virtuális gépekre vonatkozó korlátok nem állíthatók be az alábbi táblázatban látható érték fölé.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
[Azure Machine learning a számítás](concept-compute-target.md#azure-machine-learning-compute-managed) alapértelmezett kvótája a magok száma, valamint az előfizetésben régiónként engedélyezett egyedi számítási erőforrások száma. Ez a kvóta elkülönül az előző szakasz VM Core-kvótájának.

Az ebben a szakaszban megjelenő korlátokat a táblázatban megjelenő **maximális korlátra** vonatkozóan a [kvóta növelésére kéri](#request-quota-increases) fel.

Rendelkezésre álló erőforrások:
+ A **dedikált magok régiónként** alapértelmezett korlátja 24-300, az előfizetési ajánlat típusától függően.  A dedikált magok száma az egyes VM-családokhoz növelhető. A speciális virtuálisgép-családok (például a NCv2, a NCv3 vagy az ND sorozat) alapértelmezés szerint nullával kezdődnek.

+ Az **alacsony prioritású magok régiónként** alapértelmezett korlátja a 100 – 3000, az előfizetési ajánlat típusától függően. Az előfizetések alacsony prioritású magok száma növelhető, és egyetlen érték a virtuálisgép-családokon belül.

+ A **fürtök régiónként** alapértelmezett korlátja 200. Ezeket megosztják egy képzési fürt és egy számítási példány között (amely a kvóta szempontjából egyetlen csomópontos fürtnek számít).

A következő táblázat a nem megengedett további korlátozásokat mutatja be.

| **Erőforrás** | **Maximális korlát** |
| --- | --- |
| Munkaterületek erőforráscsoport szerint | 800 |
| Egyetlen Azure Machine Learning számítási (AmlCompute) erőforrás csomópontjai | 100 csomópont |
| GPU MPI folyamatok/csomópont | 1-4 |
| GPU-feldolgozók/csomópont | 1-4 |
| Feladatok élettartama | 21 nap<sup>1</sup> |
| Low-Priority csomóponton futó feladatok élettartama | 7 nap<sup>2</sup> |
| Paraméter-kiszolgálók/csomópont | 1 |

<sup>1</sup> a maximális élettartam a Futtatás kezdete és befejeződése közötti időtartamra utal. A befejezett futtatások határozatlan ideig maradnak. Nem érhető el a maximális élettartamon belül nem befejezett futtatások adatkészletei.
<sup>2</sup> Low-Priority csomóponton lévő feladatok bármikor előzik, ha van kapacitási korlátozás. Javasoljuk, hogy hajtsa végre a további ellenőrzési pontokat a feladatokban.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning folyamatok
[Azure Machine learning folyamatok](concept-ml-pipelines.md) a következő korlátokkal rendelkeznek.

| **Erőforrás** | **Korlát** |
| --- | --- |
| A folyamat lépései | 30 000 |
| Munkaterületek erőforráscsoport szerint | 800 |

### <a name="container-instances"></a>Tárolópéldányok

További információ: [Container instances korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Az Azure Storage-fiókok régiónként legfeljebb 250 Storage-fiókkal rendelkezhetnek, előfizetések szerint. Ez magában foglalja a standard és a Premium Storage fiókot is.

A korlát növeléséhez kérjen egy kérést az [Azure-támogatáson](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)keresztül. Az Azure Storage csapata áttekinti az esetet, és akár 250 Storage-fiókot is jóváhagyhat egy adott régióban.


## <a name="workspace-level-quota"></a>Munkaterület-szintű kvóta

Munkaterület-szintű kvóták használatával kezelheti Azure Machine Learning számítási cél kiosztását ugyanazon előfizetés több [munkaterülete](concept-workspace.md) között.

Alapértelmezés szerint az összes munkaterület ugyanazt a kvótát használja, mint a VM-családokra vonatkozó előfizetési szint kvóta. Egy előfizetéshez tartozó munkaterületeken azonban maximális kvótát is beállíthat az egyes virtuálisgép-családokhoz. Így megoszthatja a kapacitást, és elkerülheti az erőforrás-tartalommal kapcsolatos problémákat:

1. Navigáljon az előfizetés bármely munkaterületére.
1. A bal oldali ablaktáblán válassza a **kihasználtságok + kvóták**lehetőséget.
1. A kvóták megtekintéséhez válassza a **kvóták konfigurálása** lapot.
1. Bontsa ki a virtuálisgép-családokat.
1. A virtuális gép családjában felsorolt összes munkaterületre vonatkozóan állítson be kvóta-korlátot.

Nem állíthat be negatív értéket vagy magasabb értéket az előfizetés szintjének kvótáján.

[![Azure Machine Learning munkaterület-szintű kvóta](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Az előfizetési szintű engedélyekre van szükség a kvóta beállításához a munkaterület szintjén.

## <a name="view-your-usage-and-quotas"></a>A használat és a kvóták megtekintése

Ha szeretné megtekinteni a kvótát a különböző Azure-erőforrások, például a Virtual Machines, a tárolás és a hálózat között, használja a Azure Portal:

1. A bal oldali ablaktáblán válassza a **minden szolgáltatás** lehetőséget, majd az általános kategóriában válassza az **előfizetések** lehetőséget.

2. Az előfizetések listájából válassza ki azt az előfizetést, amelynek a kvótáját keresi.

3. Válassza a **használat + kvóták** lehetőséget a jelenlegi kvóták korlátainak és használatának megtekintéséhez. A szűrők használatával válassza ki a szolgáltatót és a helyet. 

Az előfizetéshez tartozó Azure Machine Learning számítási kvótát a többi Azure-kvótától függetlenül kezeljük. 

1. Navigáljon a Azure Portal **Azure Machine learning** munkaterületére.

2. A bal oldali ablaktábla **támogatás + hibaelhárítás területén** válassza a **használat + kvóták** lehetőséget a jelenlegi kvóta-korlátok és-használat megtekintéséhez.

3. Válasszon egy előfizetést a kvóta korlátainak megtekintéséhez. Ne felejtse el szűrni az Önt érdeklő régiót.

4. Válthat az előfizetési szint nézet és a munkaterület szintű nézetek között.

## <a name="request-quota-increases"></a>Kvótanövelések kérése

Ha a korlátot vagy kvótát az alapértelmezett korlát fölé kívánja emelni, [Nyisson meg egy online ügyfélszolgálati kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) díjmentesen.

A határértékek nem állíthatók be a fenti táblázatokban látható maximális határérték fölé. Ha nincs maximális korlát, akkor az erőforrásra vonatkozó korlát nem módosítható.

Kvóta növelésének kérelmezése esetén válassza ki azt a szolgáltatást, amelyre a kvótát emelni kívánja. Például Azure Machine Learning, Container Instances, tárterület stb. Azure Machine Learning számítás esetén **a kvóta megtekintése gombra kattintva** tekintheti meg a kvótát a fenti lépéseket követve.

> [!NOTE]
> Az [ingyenes próbaverziós előfizetések](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak a korlát vagy a kvóta növelésére. Ha [ingyenes próbaverziós előfizetéssel](https://azure.microsoft.com/offers/ms-azr-0044p)rendelkezik, [az utólagos](https://azure.microsoft.com/offers/ms-azr-0003p/) elszámolású előfizetésre válthat. További információ: az [Azure ingyenes próbaverziójának frissítése az](../billing/billing-upgrade-azure-subscription.md) utólagos elszámolású és az  [ingyenes próbaverziós előfizetésre vonatkozó gyakori kérdések](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>A magánhálózati végpont és a magánhálózati DNS-kvóta növekszik

Az előfizetésben létrehozható privát végpontok és saját DNS-zónák száma korlátozott.

Míg a Azure Machine Learning erőforrásokat hoz létre az (ügyfél-) előfizetésben, bizonyos esetekben létrehozhat erőforrásokat egy Microsoft tulajdonú előfizetésben.

 A következő esetekben előfordulhat, hogy kvótát kell igényelnie a Microsoft tulajdonában lévő előfizetésben:

* __Privát hivatkozás engedélyezve munkaterülete ügyfél által felügyelt kulccsal (CMK)__
* __Azure Container Registry a virtuális hálózat mögötti munkaterülethez__
* Az __Azure Kubernetes Service-fürt saját munkaterülethez való csatolása__.

Ezen forgatókönyvek esetében a következő lépések végrehajtásával igényelhet támogatást:

1. [Hozzon létre egy Azure-támogatási kérést](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) , és válassza ki az alábbi beállításokat az __alapok__ szakaszban:

    | Mező | Kiválasztás |
    | ----- | ----- |
    | Probléma típusa | Műszaki |
    | Szolgáltatás | Saját szolgáltatások. A legördülő listában válassza a __Machine learning__ lehetőséget. |
    | Probléma típusa | Munkaterület konfigurációja és biztonság |
    | Probléma altípusa | A privát végpont és a saját DNS zóna juttatására vonatkozó kérelem |

2. A __részletek__ szakaszban a __Leírás__ mező használatával adja meg a használni kívánt Azure-régiót és a használni kívánt forgatókönyvet. Ha több előfizetés esetében is kvótát kell megnövelnie, akkor ebben a mezőben az előfizetési azonosítók is szerepelnek.

3. A kérelem létrehozásához válassza a __Létrehozás__ lehetőséget.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Képernyőkép a privát végpontról és a saját DNS-kvóta növelésére vonatkozó kérésről":::

## <a name="next-steps"></a>Következő lépések

+ [A Azure Machine Learning költségeinek megtervezése & kezeléséhez](concept-plan-manage-cost.md)
