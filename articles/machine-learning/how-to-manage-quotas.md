---
title: Erőforrások és kvóták kezelése
titleSuffix: Azure Machine Learning
description: Ismerje meg a Azure Machine Learning erőforrásokra vonatkozó kvótákat, valamint a kvóta növelésének módját.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: a8a04d9e1b17c7e65be946cb51bfc41019e0706d
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233992"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Erőforrások kvótáinak kezelése és növelése Azure Machine Learning

Az Azure korlátozza a korlátokat és a kvótákat, hogy megakadályozza a költségvetés túllépését csalás miatt, valamint az Azure-kapacitás korlátozásait. Ezeket a korlátokat az éles számítási feladatokra való skálázás során érdemes figyelembe venni. Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
> + Az [Azure Machine Learninghoz](overview-what-is-azure-ml.md)kapcsolódó Azure-erőforrásokra vonatkozó alapértelmezett korlátok.
> + Munkaterület-szintű kvóták létrehozása.
> + Megtekintheti a kvótákat és a korlátozásokat.
> + A kvóta kérése növekszik.
> + Magánhálózati végpontok és DNS-kvóták.

A kvóták kezelése mellett megismerheti a [Azure Machine learning költségeinek megtervezését és kezelését](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Különleges szempontok

+ A kvóta egy hitelkeret, nem pedig a kapacitási garancia. Ha nagyobb kapacitásra van szüksége, [forduljon az Azure támogatási szolgálatához, és növelje a kvótát](#request-quota-increases).

+ A kvóta az előfizetések összes szolgáltatásában meg van osztva, beleértve a Azure Machine Learningt is. Kiszámítja a használatot az összes szolgáltatásban a kapacitás kiértékelése során.
 
  Azure Machine Learning számítás kivétel. Az alapszintű számítási kvóta külön kvótával rendelkezik. 

+ Az alapértelmezett korlátok az ajánlati kategória típusa szerint változnak, például az ingyenes próbaverzió, az utólagos elszámolású és a virtuális gép (VM) adatsorozata (például Dv2, F és G).

## <a name="default-resource-quotas"></a>Alapértelmezett erőforrás-kvóták

Ebben a szakaszban az alábbi erőforrásokra vonatkozó alapértelmezett és maximális kvóta-korlátozásokat ismerheti meg:

+ Virtual machines (Virtuális gépek)
+ Azure Machine Learning számítás
+ Azure Machine Learning folyamatok
+ Azure Container Instances
+ Azure Storage

> [!IMPORTANT]
> A korlátozások változhatnak. A legfrissebb információkért tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) az Azure-ban.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Minden Azure-előfizetés korlátozza a virtuális gépek számát az összes szolgáltatáson belül. A virtuálisgép-magok méretének regionális korlátja és a regionális korlátok száma. Mindkét korlátot külön kényszeríti a rendszer.

Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés 30 a1-es virtuális gép vagy 30 D1 virtuális gép üzembe helyezését, illetve a kettő olyan kombinációját teszi lehetővé, amely nem haladja meg az összesen 30 magot.

A virtuális gépekre vonatkozó korlátok az alábbi táblázatban látható értékek fölé nem állíthatók be.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning számítás
[Azure Machine learning a számítás](concept-compute-target.md#azure-machine-learning-compute-managed) alapértelmezett kvótája a magok száma, valamint az előfizetésben régiónként engedélyezett egyedi számítási erőforrások száma. Ez a kvóta elkülönül az előző szakasz VM Core-kvótájának.

Az ebben a szakaszban megjelenő korlátokat a táblázatban megjelenő maximális korlátra vonatkozóan a [kvóta növelésére kéri](#request-quota-increases) fel.

Rendelkezésre álló erőforrások:
+ A **dedikált magok régiónként** alapértelmezett korlátja 24 – 300, az előfizetési ajánlat típusától függően. A dedikált magok száma minden egyes virtuálisgép-család esetében növelhető. A speciális virtuálisgép-családok (például a NCv2, a NCv3 vagy az ND sorozat) alapértelmezés szerint nullával kezdődnek.

+ Az **alacsony prioritású magok régiónként** alapértelmezett korlátja 100 – 3 000, az előfizetési ajánlat típusától függően. Az előfizetések alacsony prioritású magok száma növelhető, és egyetlen érték a virtuálisgép-családokon belül.

+ A **fürtök régiónként** alapértelmezett korlátja 200. Ezek egy képzési fürt és egy számítási példány között vannak megosztva. (A számítási példányok egy egycsomópontos fürtnek számítanak kvóta szempontjából.)

A következő táblázat további korlátozásokat mutat be, amelyeket nem lehet túllépni.

| **Erőforrás** | **Maximális korlát** |
| --- | --- |
| Munkaterületek erőforráscsoport szerint | 800 |
| Egyetlen Azure Machine Learning számítási (AmlCompute) erőforrás csomópontjai | 100 csomópont |
| GPU MPI folyamatok/csomópont | 1-4 |
| GPU-feldolgozók/csomópont | 1-4 |
| Feladatok élettartama | 21 nap<sup>1</sup> |
| Feladat élettartama alacsony prioritású csomóponton | 7 nap<sup>2</sup> |
| Paraméter-kiszolgálók/csomópont | 1 |

<sup>1</sup> a maximális élettartam az időtartam, amikor a Futtatás elindul, és amikor a folyamat befejeződik. A befejezett futtatások határozatlan ideig maradnak. Nem érhető el a maximális élettartamon belül nem befejezett futtatások adatkészletei.
<sup>2</sup> az alacsony prioritású csomóponton lévő feladatok akkor előzik, ha van kapacitási korlátozás. Javasoljuk, hogy az ellenőrzőpontokat a feladatokban implementálja.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning folyamatok
[Azure Machine learning folyamatok](concept-ml-pipelines.md) a következő korlátokkal rendelkeznek.

| **Erőforrás** | **Korlát** |
| --- | --- |
| A folyamat lépései | 30 000 |
| Munkaterületek erőforráscsoport szerint | 800 |

### <a name="container-instances"></a>Tárolópéldányok

További információ: [Container instances korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Az Azure Storage-ban legfeljebb 250 Storage-fiók lehet, előfizetése pedig régiónként. Ez a korlát a standard és a Premium Storage-fiókokat is tartalmazza.

A korlát növeléséhez kérjen egy kérést az [Azure-támogatáson](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)keresztül. Az Azure Storage csapata áttekinti az esetet, és legfeljebb 250 Storage-fiókot tud jóváhagyni egy adott régióban.


## <a name="workspace-level-quotas"></a>Munkaterület-szintű kvóták

Munkaterület-szintű kvóták használatával kezelheti Azure Machine Learning számítási cél kiosztását az azonos előfizetésben található több [munkaterület](concept-workspace.md) között.

Alapértelmezés szerint az összes munkaterület ugyanazt a kvótát használja, mint a virtuálisgép-családok előfizetési szintjének kvótája. Egy előfizetéshez tartozó munkaterületeken azonban maximális kvótát is beállíthat az egyes virtuálisgép-családokhoz. Így megoszthatja a kapacitást, és elkerülheti az erőforrás-tartalommal kapcsolatos problémákat.

1. Lépjen az előfizetés bármely munkaterületére.
1. A bal oldali ablaktáblán válassza a **kihasználtságok + kvóták** lehetőséget.
1. A kvóták megtekintéséhez válassza a **kvóták konfigurálása** lapot.
1. Bontsa ki a virtuálisgép-családokat.
1. A virtuális gép családjában felsorolt összes munkaterületre vonatkozóan állítson be kvóta-korlátot.

Nem állítható be negatív érték, vagy az előfizetés szintű kvótánál magasabb érték.

[![A Azure Machine Learning munkaterület kvótáját megjelenítő képernyőkép.](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Az előfizetés szintű engedélyekre van szükség a kvóta beállításához a munkaterület szintjén.

## <a name="view-your-usage-and-quotas"></a>A használat és a kvóták megtekintése

Ha szeretné megtekinteni a kvótát különböző Azure-erőforrások, például virtuális gépek, tárolók vagy hálózatok számára, használja a Azure Portal:

1. A bal oldali ablaktáblán válassza a **minden szolgáltatás** lehetőséget, majd az **általános** kategóriában válassza az **előfizetések** lehetőséget.

2. Az előfizetések listájából válassza ki azt az előfizetést, amelynek a kvótáját keresi.

3. Válassza a **használat + kvóták** lehetőséget a jelenlegi kvóták korlátainak és használatának megtekintéséhez. A szűrők használatával válassza ki a szolgáltatót és a helyet. 

Az előfizetéshez tartozó Azure Machine Learning számítási kvótát a többi Azure-kvótától függetlenül kezelheti: 

1. Lépjen a Azure Portal **Azure Machine learning** munkaterületére.

2. A bal oldali ablaktábla **támogatás + hibaelhárítás** területén válassza a **használat + kvóták** lehetőséget a jelenlegi kvóta-korlátok és-használat megtekintéséhez.

3. Válasszon egy előfizetést a kvóta korlátainak megtekintéséhez. Szűrje az Önt érdeklő régiót.

4. Válthat az előfizetés szintű nézet és a munkaterület szintű nézet között.

## <a name="request-quota-increases"></a>Kvótanövelések kérése

Ha a korlátot vagy kvótát az alapértelmezett korlát fölé kívánja emelni, [Nyisson meg egy online ügyfélszolgálati kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) díjmentesen.

Az előző táblázatokban megjelenő maximális értékek felett nem lehet határértékeket emelni. Ha nincs maximális korlát, az erőforrásra vonatkozó korlát nem módosítható.

Ha a kvóta növelését kéri, válassza ki azt a szolgáltatást, amelyet figyelembe kíván venni. Válassza például a Azure Machine Learning, a Container Instances vagy a Storage lehetőséget. Azure Machine Learning számításhoz a kvóta **megkeresése** gombra kattintva megtekintheti a kvótát az előző lépésekben.

> [!NOTE]
> Az [ingyenes próbaverziós előfizetések](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak a korlát vagy a kvóta növelésére. Ha ingyenes próbaverziós előfizetéssel rendelkezik, [az utólagos](https://azure.microsoft.com/offers/ms-azr-0003p/) elszámolású előfizetésre válthat. További információ: az [Azure ingyenes próbaverziójának frissítése az](https://docs.microsoft.com/azure/cost-management-billing/manage/upgrade-azure-subscription) utólagos [elszámolású és az ingyenes Azure-fiókra vonatkozó gyakori kérdések](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Privát végpont és saját DNS kvótájának növelése

Az előfizetésben létrehozható privát végpontok és saját DNS-zónák száma korlátozott.

A Azure Machine Learning erőforrásokat hoz létre az (ügyfél-) előfizetésben, de egyes esetekben erőforrásokat hoz létre egy Microsoft tulajdonú előfizetésben.

 A következő esetekben előfordulhat, hogy kvótát kell igényelnie a Microsoft tulajdonában lévő előfizetésben:

* Azure Private-hivatkozás engedélyezett munkaterülete ügyfél által felügyelt kulccsal (CMK)
* Azure Container Registry a virtuális hálózat mögötti munkaterülethez
* Private Link-kompatibilis Azure Kubernetes Service-fürt csatolása a munkaterülethez

Ezen forgatókönyvek esetében a következő lépések végrehajtásával igényelhet támogatást:

1. [Hozzon létre egy Azure-támogatási kérést](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) , és válassza ki az alábbi beállításokat az __alapok__ szakaszban:

    | Mező | Kiválasztás |
    | ----- | ----- |
    | Probléma típusa | **Műszaki** |
    | Szolgáltatás | **Saját szolgáltatások** . Ezután válassza a __Machine learning__ lehetőséget a legördülő listában. |
    | Probléma típusa | **Munkaterület konfigurációja és biztonság** |
    | Probléma altípusa | **A privát végpont és a saját DNS zóna juttatására vonatkozó kérelem** |

2. A __részletek__ szakaszban a __Leírás__ mező segítségével adja meg az Azure-régiót és a használni kívánt forgatókönyvet. Ha több előfizetés esetében is kvótát kell megnövelni, sorolja fel az előfizetési azonosítókat ebben a mezőben.

3. A kérelem létrehozásához válassza a __Létrehozás__ lehetőséget.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Képernyőkép a privát végpontról és a saját DNS-kvóta növeléséről.":::

## <a name="next-steps"></a>Következő lépések

+ [A Azure Machine Learning költségeinek megtervezése és kezelése](concept-plan-manage-cost.md)
