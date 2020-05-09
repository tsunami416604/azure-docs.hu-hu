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
ms.openlocfilehash: 2161a9e4460526113aaf89609b72250a09fc6af3
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891214"
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

## <a name="estimate-costs"></a>Becsült költségek

Az [Azure díjszabási számológépével](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a költségeket, mielőtt létrehozza az erőforrásokat egy Azure Machine learning-fiókban. A bal oldalon válassza az **AI + Machine learning**lehetőséget, majd a kezdéshez válassza a **Azure Machine learning** lehetőséget.  

A következő képernyőkép a kalkulátor használatával mutatja be a költségbecslést:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Költségbecslés az Azure kalkulátorban":::

Amikor új erőforrásokat ad hozzá a munkaterülethez, térjen vissza ehhez a kalkulátorhoz, és adja hozzá ugyanezt az erőforrást a költségbecslés frissítéséhez.

Míg a nagyvállalati kiadás előzetes verzióban érhető el, nem áll rendelkezésre ML-díj. Ha a nagyvállalati kiadás általánosan elérhetővé válik, a gépi tanulási pótdíj (képzés és következtetés) lesz.  További részletekért [Azure Machine learning díjszabást](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="get-cost-alerts"></a>Cost-riasztások beszerzése

Hozzon létre [költségvetéseket](../cost-management/tutorial-acm-create-budgets.md) a költségek kezeléséhez, és hozzon létre [riasztásokat](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) , amelyek automatikusan értesítik az érintetteket a kiesések elköltéséről A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. A költségvetések és a riasztások azonban korlátozott funkcionalitással rendelkezhetnek az egyes Azure-szolgáltatások költségeinek kezeléséhez, mert a költségek magasabb szinten való nyomon követésére szolgálnak.

## <a name="monitor-costs"></a>Költségek figyelése

A Azure Machine Learningekkel rendelkező erőforrások használatakor költségek merülnek fel. Az Azure Erőforrás-használati egység költségei az időintervallumok (másodperc, perc, óra és nap) vagy a kérési egység használata szerint változnak. Amint a Azure Machine Learning használata megkezdődik, a költségek felmerülnek. Ezeket a költségeket a Azure Portal [Cost Analysis](../cost-management/quick-acm-cost-analysis.md) paneljén tekintheti meg.

A különböző időintervallumokhoz tartozó grafikonok és táblák költségeinek megtekintése. Néhány példa: nap, aktuális, előző hónap és év. A költségeket és az előre jelzett költségeket is megtekintheti. Ha a hosszabb nézetekre vált, az idő múlásával azonosíthatja a kiadási trendeket, és megtekintheti, hogy hol történt a túltöltés. Ha költségvetéseket hozott létre, tekintse meg a hol túlléptéket.  

Nem jelenik meg külön szolgáltatási rész a Machine Learning számára.  Ehelyett a Machine Learning-munkaterületekhez hozzáadott különböző erőforrásokat fogja látni.

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning számítási fürt (AmlCompute) használata

A folyamatosan változó adatváltozások esetén gyors és gördülékeny modell-képzésre és-képzésre van szükség a pontos modellek fenntartása érdekében. A folyamatos képzés azonban a GPU-k mélyreható tanulási modelljeire is kikerül. 

Azure Machine Learning felhasználók használhatják a felügyelt Azure Machine Learning számítási fürtöt, más néven AmlCompute. A AmlCompute számos GPU-és CPU-beállítást támogat. A AmlCompute az előfizetése nevében Azure Machine Learning, de a vállalati szintű biztonsági, megfelelőségi és irányítási funkciókat biztosítja az Azure IaaS Cloud Scale-ben.

Mivel ezek a számítási készletek az Azure IaaS-infrastruktúráján belül vannak, a képzést az infrastruktúra többi részével megegyező biztonsági és megfelelőségi követelményekkel helyezheti üzembe, méretezheti és kezelheti.  Ezek az üzembe helyezések az előfizetésében történnek, és engedelmeskednek az irányítás szabályainak. További információ a [Azure Machine learning számítási](how-to-set-up-training-targets.md#amlcompute)feladatokról.

## <a name="configure-training-clusters-for-autoscaling"></a>Betanítási fürtök konfigurálása automatikus skálázáshoz

A fürtök automatikus skálázása a munkaterhelés követelményei alapján csökkenti a költségeket, így csak a szükséges igényeket használhatja. A AmlCompute-fürtök úgy vannak kialakítva, hogy a számítási feladatok követelményei alapján dinamikusan méretezhetők legyenek. A fürt felskálázása az előfizetéshez kijelölt csomópontok maximális számáig és az előfizetés számára kijelölt kvótán belül is lehetséges. Ahogy az egyes futtatások befejeződik, a fürt felszabadítja a csomópontokat, és automatikusan átméretezi a kijelölt csomópontok minimális darabszámát.

A csomópontok minimális és maximális számának beállítása mellett a méretezés előtt állítsa be azt az időtartamot, ameddig a csomópont üresjáratban van. Alapértelmezés szerint a leskálázás előtti üresjárati idő 120 másodpercre van állítva.

+ Ha kevesebb iterációs kísérletezést végez, csökkentse ezt az időt a költségek megtakarítása érdekében. 
+ Ha nagy mértékben ismétlődő fejlesztési/tesztelési kísérleteket hajt végre, akkor előfordulhat, hogy ezt úgy kell megnövelni, hogy a betanítási szkript vagy a környezet minden módosítása után ne fizessen a folyamatos skálázásra.

A AmlCompute-fürtök konfigurálhatók a munkaterhelési követelmények Azure Portalban való módosításához a [AMLCOMPUTE SDK osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py), a [AmlCompute CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)és a [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)-k használatával.

```azure cli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Erőforrások kvótáinak beállítása

A többi Azure számítási erőforráshoz hasonlóan a AmlCompute is tartalmaz egy benne rejlő [kvóta (vagy korlát) konfigurációt](how-to-manage-quotas.md#azure-machine-learning-compute). Ez a kvóta virtuálisgép-család (például Dv2 sorozat, NCv3 sorozat), és az egyes előfizetések régiónként eltérőek. Az előfizetések kis alapértékekkel kezdődnek, és ezzel a beállítással szabályozható, hogy az előfizetésében milyen Amlcompute-erőforrások legyenek elérhetők. 

Konfigurálja a [munkaterület-szintű kvótát](how-to-manage-quotas.md#workspace-level-quota)is a virtuálisgép-család szerint az előfizetésben található minden egyes munkaterülethez. Így részletesebben szabályozhatja az egyes munkaterületek által felmerülhető költségeket, és korlátozhatja bizonyos virtuálisgép-családokat. 

Ha a kvótákat a munkaterület szintjén szeretné beállítani, kezdje a [Azure Portal](https://portal.azure.com).  Válasszon ki egy munkaterületet az előfizetésben, majd a bal oldali ablaktáblán válassza a **kihasználtságok és kvóták** lehetőséget. Ezután válassza a **kvóták konfigurálása** lapot a kvóták megtekintéséhez. A kvóta beállításához jogosultságokra van szüksége az előfizetési hatókörben, mivel ez egy olyan beállítás, amely több munkaterületet érint.

## <a name="set-run-auto-termination-policies"></a>Automatikus megszakítási szabályzatok futtatásának beállítása 

Konfigurálhatja a betanítást, hogy korlátozza az időtartamát, vagy ha bizonyos feltételeknél korán leáll, különösen akkor, ha a Azure Machine Learning beépített Hiperparaméter-hangolási vagy automatizált Machine Learning képességeit használja. 

Íme néhány lehetőség:
* Definiáljon egy nevű `max_run_duration_seconds` paramétert a RunConfiguration annak a maximális időtartamnak a szabályozására, ameddig a Futtatás kiterjeszthető a kiválasztott számítási lehetőségre (helyi vagy távoli Felhőbeli számítás).
* A [hiperparaméter hangoláshoz](how-to-tune-hyperparameters.md#early-termination)adjon meg egy korai leállítási szabályzatot egy Bandit-szabályzatból, egy középértéket leállító házirendből vagy egy csonkolt kiválasztási szabályzatból. Emellett olyan paramétereket is használhatnak, mint `max_total_runs` a `max_duration_minutes` vagy a a különböző hiperparaméter-lóversenyfogadások további szabályozására.
* Az [automatizált gépi tanuláshoz](how-to-configure-auto-train.md#exit)állítson be hasonló megszakítási házirendeket a `enable_early_stopping` jelző használatával. Olyan tulajdonságokat is használhat, `iteration_timeout_minutes` mint `experiment_timeout_minutes` a és a a Futtatás maximális időtartamának szabályozására, vagy a teljes kísérletre.

## <a name="use-low-priority-vms"></a>Alacsony prioritású virtuális gépek használata

Az Azure-ban a virtuálisgép-méretezési csoportok, a Batch és a Machine Learning szolgáltatás által biztosított alacsony prioritású virtuális gépek nem használhatnak feleslegesen kihasználatlan kapacitást. Ezek a foglalások előre emptible, de kedvezményes áron érhetők el a dedikált virtuális gépekhez képest. Általánosságban azt javasoljuk, hogy alacsony prioritású virtuális gépeket használjon a Batch számítási feladatokhoz, vagy ha a megszakítások a Visszaküldések (a Batch-következtetések esetében) vagy az újraindítások (az ellenőrzőpont-készítéssel folytatott mély tanulási képzések) segítségével helyreállítható.

Az alacsony prioritású virtuális gépek egyetlen kvótával rendelkeznek a dedikált kvóta értéktől, amely a VM-család. [További információ a AmlCompute-kvótákkal kapcsolatban](how-to-manage-quotas.md).

Állítsa be a virtuális gép prioritását az alábbi módokon:

* A Studióban válassza az **alacsony prioritású** virtuális gép létrehozása lehetőséget.

* A Python SDK-val állítsa be `vm_priority` az attribútumot a létesítési konfigurációban.  

    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                               vm_priority='lowpriority',
                                                               max_nodes=4)
    ```

* A CLI használatával állítsa be a `vm-priority`következőket:

    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 Az alacsony prioritású virtuális gépek nem működnek a számítási példányok esetében, mivel az interaktív notebook-élmények támogatásához szükségesek. 

## <a name="use-reserved-instances"></a>Fenntartott példányok használata

Az Azure-beli fenntartott VM-példány egy másik módszert kínál a számítási erőforrások hatalmas megtakarítására egy évig vagy hároméves feltételek betartása mellett. Ezek az árengedmények az utólagos elszámolású díjak 72%-ában érvényesek, és közvetlenül a havi Azure-számlára vonatkoznak.

Azure Machine Learning a számítás a fenntartott példányokat is támogatja. Ha tehát egy-vagy hároméves fenntartott példányt vásárolt, a rendszer automatikusan alkalmazza a fenntartott példány kedvezményét a Azure Machine Learningon belül használt felügyelt számítási feladatokra, anélkül, hogy további telepítést kellene megadnia a végétől.


## <a name="next-steps"></a>További lépések

További információk:
* [Erőforrás-kvóták kezelése és növelése](how-to-manage-quotas.md)
* [Költségek kezelése a [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md)szolgáltatással.
* [Azure Machine learning a számítást](how-to-set-up-training-targets.md#amlcompute).
