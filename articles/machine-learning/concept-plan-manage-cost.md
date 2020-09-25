---
title: Költségtervezés és a költségek kezelése
titleSuffix: Azure Machine Learning
description: Megtervezheti és kezelheti a költségek elemzésével Azure Machine Learning a Azure Portalban. Gépi tanulási modellek készítésekor további költségmegtakarítási tippeket tudhat meg, hogy csökkentse költségeit.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 872775df82d609a640346ddef2f77381c2160fcf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276067"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>A Azure Machine Learning költségeinek megtervezése és kezelése

Ez a cikk a Azure Machine Learning költségeinek megtervezését és kezelését ismerteti. Első lépésként az Azure díjszabási kalkulátor segítségével megtervezheti a költségeket, mielőtt erőforrásokat hozna létre. Ezután adja hozzá az Azure-erőforrásokat, és tekintse át a becsült költségeket. Végül a költséghatékony tippeket használhatja a modell felügyelt Azure Machine Learning számítási fürtökkel való betanításához.

Azure Machine Learning erőforrások használatának elkezdése után a Cost Management szolgáltatással állíthatja be a költségvetéseket, és figyelheti a költségeket. Tekintse át az előre jelzett költségeket, és azonosítsa a kiadási trendeket, hogy azonosítsa azokat a területeket, ahol érdemes lehet eljárni.

Ismerje meg, hogy a Azure Machine Learning költségei csak a havi költségek egy részét jelentik az Azure-számlán. Ha más Azure-szolgáltatásokat használ, akkor az Azure-előfizetésében használt összes Azure-szolgáltatás és erőforrás után, beleértve a harmadik féltől származó szolgáltatásokat is. Ez a cikk a Azure Machine Learning költségeinek tervezését és kezelését ismerteti. Miután megismerte Azure Machine Learning költségeinek kezelését, hasonló módszerekkel kezelheti az előfizetésében használt összes Azure-szolgáltatás költségeit.

A gépi tanulási modellek betanításakor a felügyelt Azure Machine Learning számítási fürtök segítségével kihasználhatja a költségmegtakarítással járó tippeket:

* A betanítási fürtök konfigurálása automatikus skálázáshoz
* Kvóták beállítása az előfizetéshez és a munkaterületekhez
* Leállítási szabályzatok beállítása a betanítási futtatáshoz
* Alacsony prioritású virtuális gépek használata (VM)
* Azure-beli fenntartott VM-példány használata

## <a name="prerequisites"></a>Előfeltételek

A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához. 

További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs"></a>A költségek megbecslése

Az [Azure díjszabási számológépével](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a költségeket, mielőtt létrehozza az erőforrásokat egy Azure Machine learning-fiókban. A bal oldalon válassza az **AI + Machine learning**lehetőséget, majd a kezdéshez válassza a **Azure Machine learning** lehetőséget.  

A következő képernyőkép a kalkulátor használatával mutatja be a költségbecslést:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Költségbecslés az Azure kalkulátorban":::

Amikor új erőforrásokat ad hozzá a munkaterülethez, térjen vissza ehhez a kalkulátorhoz, és adja hozzá ugyanezt az erőforrást a költségbecslés frissítéséhez.

További információ: [Azure Machine learning díjszabása](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="get-cost-alerts"></a>Cost-riasztások beszerzése

Hozzon létre [költségvetéseket](../cost-management/tutorial-acm-create-budgets.md) a költségek kezeléséhez, és hozzon létre [riasztásokat](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) , amelyek automatikusan értesítik az érintetteket a kiesések elköltéséről A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. A költségvetések és a riasztások azonban korlátozott funkcionalitással rendelkezhetnek az egyes Azure-szolgáltatások költségeinek kezeléséhez, mert a költségek magasabb szinten való nyomon követésére szolgálnak.

## <a name="monitor-costs"></a>Költségek figyelése

A Azure Machine Learningekkel rendelkező erőforrások használatakor költségek merülnek fel. Az Azure Erőforrás-használati egység költségei az időintervallumok (másodperc, perc, óra és nap) vagy a kérési egység használata szerint változnak. Amint a Azure Machine Learning használata megkezdődik, a költségek felmerülnek. Ezeket a költségeket a Azure Portal [Cost Analysis](../cost-management/quick-acm-cost-analysis.md) paneljén tekintheti meg.

A költségeket diagramokban és táblákban is megtekintheti a különböző időintervallumokhoz. A költségeket a költségvetések és az előre jelzett költségek között is megtekintheti. Ha a hosszabb nézetekre vált, az idő múlásával azonosíthatja a kiadási trendeket, és megtekintheti, hogy hol történt a túltöltés. Ha költségvetéseket hozott létre, tekintse meg a hol túlléptéket.  

Nem jelenik meg külön szolgáltatási rész a Machine Learning számára.  Ehelyett a Machine Learning-munkaterületekhez hozzáadott különböző erőforrásokat fogja látni.

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning számítási fürt (AmlCompute) használata

A folyamatosan változó adatváltozások esetén gyors és gördülékeny modell-képzésre és-képzésre van szükség a pontos modellek fenntartása érdekében. A folyamatos képzés azonban a GPU-k mélyreható tanulási modelljeire is kikerül. 

Azure Machine Learning felhasználók használhatják a felügyelt Azure Machine Learning számítási fürtöt, más néven AmlCompute. A AmlCompute számos GPU-és CPU-beállítást támogat. A AmlCompute az előfizetése nevében, Azure Machine Learning alapján üzemelteti. Ugyanezt a vállalati szintű biztonsági, megfelelőségi és irányítási funkciókat biztosítja az Azure IaaS Cloud Scale-ben.

Mivel ezek a számítási készletek az Azure IaaS-infrastruktúráján belül vannak, a képzést az infrastruktúra többi részével megegyező biztonsági és megfelelőségi követelményekkel helyezheti üzembe, méretezheti és kezelheti.  Ezek az üzembe helyezések az előfizetésében történnek, és engedelmeskednek az irányítás szabályainak. További információ a [Azure Machine learning számítási](how-to-create-attach-compute-sdk.md#amlcompute)feladatokról.

## <a name="configure-training-clusters-for-autoscaling"></a>Betanítási fürtök konfigurálása automatikus skálázáshoz

A fürtök automatikus skálázása a munkaterhelés követelményei alapján csökkenti a költségeket, így csak a szükséges igényeket használhatja.

A AmlCompute-fürtök úgy vannak kialakítva, hogy a számítási feladatok alapján dinamikusan méretezhetőek legyenek. A fürt akár a konfigurált csomópontok maximális számáig is méretezhető. Ahogy az egyes futtatások befejeződik, a fürt felszabadítja a csomópontokat, és a beállított minimális csomópontok számának megfelelően méretezhető.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Azt is beállíthatja, hogy a csomópont mennyi ideig tétlen legyen a skálázás előtt. Alapértelmezés szerint a leskálázás előtti üresjárati idő 120 másodpercre van állítva.

+ Ha kevesebb iterációs kísérletezést végez, csökkentse ezt az időt a költségek megtakarítása érdekében.
+ Ha nagy mértékben ismétlődő fejlesztési/tesztelési kísérleteket hajt végre, akkor előfordulhat, hogy meg kell nőnie az időt, hogy a képzési parancsfájl vagy környezet minden módosítása után ne fizessen az állandó skálázásra.

A AmlCompute-fürtök konfigurálhatók a munkaterhelési követelmények Azure Portalban való módosításához a [AMLCOMPUTE SDK osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true), a [AmlCompute CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)és a [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)-k használatával.

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Erőforrások kvótáinak beállítása

A AmlCompute [kvóta (vagy korlát) konfigurációt](how-to-manage-quotas.md#azure-machine-learning-compute)tartalmaz. Ez a kvóta virtuálisgép-család (például Dv2 sorozat, NCv3 sorozat), és az egyes előfizetések régiónként eltérőek. Az előfizetések kis alapértékekkel kezdődnek, és ezzel a beállítással szabályozható, hogy az előfizetésében milyen Amlcompute-erőforrások legyenek elérhetők. 

Konfigurálja a [munkaterület-szintű kvótát](how-to-manage-quotas.md#workspace-level-quota)is a virtuálisgép-család szerint az előfizetésben található minden egyes munkaterülethez. Így részletesebben szabályozhatja az egyes munkaterületek által felmerülhető költségeket, és korlátozhatja bizonyos virtuálisgép-családokat. 

Ha a kvótákat a munkaterület szintjén szeretné beállítani, kezdje a [Azure Portal](https://portal.azure.com).  Válasszon ki egy munkaterületet az előfizetésben, majd a bal oldali ablaktáblán válassza a **kihasználtságok és kvóták** lehetőséget. Ezután válassza a **kvóták konfigurálása** lapot a kvóták megtekintéséhez. A kvóta beállításához jogosultságok szükségesek az előfizetési hatókörben, mivel ez egy olyan beállítás, amely több munkaterületet érint.

## <a name="set-run-autotermination-policies"></a>Az automegszakítási szabályzatok futtatásának beállítása 

Bizonyos esetekben konfigurálnia kell a betanítási időszakokat, hogy azok az időtartamra korlátozzák, vagy korán megszakítsák őket. Ha például Azure Machine Learning beépített hiperparaméter-hangolást vagy automatizált gépi tanulást használ.

Íme néhány lehetőség:
* Definiáljon egy nevű paramétert a `max_run_duration_seconds` RunConfiguration annak a maximális időtartamnak a szabályozására, ameddig a Futtatás kiterjeszthető a kiválasztott számítási lehetőségre (helyi vagy távoli Felhőbeli számítás).
* A [hiperparaméter-hangoláshoz](how-to-tune-hyperparameters.md#early-termination)adjon meg egy korai megszakítási szabályzatot egy bandita-házirendből, egy középértékes leállítási házirendből vagy egy csonkolt kiválasztási szabályzatból. A hiperparaméter-lóversenyfogadások további szabályozásához használja a következő paramétereket: `max_total_runs` vagy `max_duration_minutes` .
* Az [automatizált gépi tanuláshoz](how-to-configure-auto-train.md#exit)állítson be hasonló megszakítási házirendeket a  `enable_early_stopping` jelző használatával. Olyan tulajdonságokat is használhat, mint a és a a `iteration_timeout_minutes` `experiment_timeout_minutes` Futtatás maximális időtartamának szabályozására, vagy a teljes kísérletre.

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Alacsony prioritású virtuális gépek használata

Az Azure-ban a virtuálisgép-méretezési csoportok, a Batch és a Machine Learning szolgáltatás által biztosított alacsony prioritású virtuális gépek nem használhatnak feleslegesen kihasználatlan kapacitást. Ezek a foglalások előre emptible, de kedvezményes áron érhetők el a dedikált virtuális gépekhez képest. Általánosságban ajánlott alacsony prioritású virtuális gépeket használni a Batch-munkaterhelésekhez. Azokat is érdemes használni, ahol a megszakítások az Újraküldés (a Batch-következtetések esetében) vagy az újraindítások (az ellenőrzőpontokkal folytatott mély tanulási képzések esetén) helyreállítására használhatók.

Az alacsony prioritású virtuális gépek egyetlen kvótával rendelkeznek a dedikált kvóta értéktől, amely a VM-család. [További információ a AmlCompute-kvótákkal kapcsolatban](how-to-manage-quotas.md).

 Az alacsony prioritású virtuális gépek nem működnek a számítási példányok esetében, mivel az interaktív notebook-élmények támogatásához szükségesek.

## <a name="use-reserved-instances"></a>Fenntartott példányok használata

A számítási erőforrások pénzének megtakarításának egy másik módja az Azure Reserved VM instance. Ennek az ajánlatnak a keretében egy-vagy hároméves szerződéssel kell eljutnia. Ezek az árengedmények az utólagos elszámolású díjak 72%-ában érvényesek, és közvetlenül a havi Azure-számlára vonatkoznak.

Azure Machine Learning a számítás a fenntartott példányokat is támogatja. Ha egy vagy három éves fenntartott példányt vásárol, a rendszer automatikusan kedvezményt alkalmaz a Azure Machine Learning felügyelt számítási feladatokra.


## <a name="next-steps"></a>Következő lépések

További információk:
* [Erőforrás-kvóták kezelése és növelése](how-to-manage-quotas.md)
* [Költségek kezelése a Cost Analysis szolgáltatással](../cost-management-billing/costs/quick-acm-cost-analysis.md).
* Hozzon létre Azure Machine Learning számítást az [SDK](how-to-create-attach-compute-sdk.md#amlcompute) -val vagy a [Studio](how-to-create-attach-compute-studio.md#amlcompute)-ban.
