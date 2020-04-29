---
title: Azure Machine Learning események felhasználása
titleSuffix: Azure Machine Learning
description: Ebből a cikkből megtudhatja, hogyan használhatja a Azure Event Grid a Azure Machine Learning által generált eseményekre való előfizetésre, reagálásra és leiratkozásra.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79139045"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Azure Machine Learning események felhasználása (előzetes verzió)

A Azure Machine Learning a gépi tanulási folyamat teljes életciklusát kezeli, beleértve a modellek betanítását, a modell üzembe helyezését és a figyelést. Azure Machine Learning események lehetővé teszik az alkalmazások számára, hogy a gépi tanulási életciklus során reagálnak az eseményekre, mint például a képzések befejezése, a modellek regisztrálása és üzembe helyezése, valamint az adateltolódás észlelése a modern kiszolgáló nélküli architektúrák használatával. 

Ezek az események [Azure Event Gridon](https://azure.microsoft.com/services/event-grid/)keresztül jelennek meg. A Azure Portal, a PowerShell vagy az Azure CLI használatával az ügyfelek egyszerűen előfizethetnek eseményeket [egy vagy több eseménytípus megadásával, valamint szűrési feltételek megadásával](/azure/event-grid/event-filtering). Az ügyfelek az eseménykezelők széles körét is kihasználhatják, például Azure Functions, Azure Logic Apps vagy általános webhookot. A Azure Machine Learning a Azure Event Grid mellett magas rendelkezésre állású, megbízható és hibatűrő esemény-kézbesítési platformot biztosít az Event vezérelt alkalmazások létrehozásához.

A Azure Machine Learning és a Event Grid használatával kapcsolatos további információkért lásd: [Event Driven-alapú Machine learning munkafolyamatok létrehozása (előzetes verzió)](how-to-use-event-grid.md).

## <a name="the-event-model"></a>Az esemény modellje 

Azure Event Grid a forrásokból, például Azure Machine Learningokból és egyéb Azure-szolgáltatásokból származó eseményeket olvas be. Ezeket az eseményeket ezután az eseménykezelők küldik, például az Azure Event Hubs, a Azure Functions, a Logic Apps és egyebek. Az alábbi ábra azt mutatja be, hogy Event Grid hogyan kapcsolódik a forrásokhoz és a kezelőhöz, de nem a támogatott integrációk átfogó listája.

![Azure Event Grid funkcionális modell](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

További információ az eseményforrás és az eseménykezelők használatáról: [Mi az Event Grid?](/azure/event-grid/overview)

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning eseménytípus

A Azure Machine Learning a gépi tanulási életciklus különböző pontjain biztosít eseményeket: 

| Eseménytípus | Leírás |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Gépi tanulási kísérlet futtatásának befejeződése után következik be |
| `Microsoft.MachineLearningServices.ModelRegistered` | Akkor következik be, amikor egy Machine learning-modell van regisztrálva a munkaterületen |
| `Microsoft.MachineLearningServices.ModelDeployed` | Akkor következik be, amikor egy vagy több modellel rendelkező következtetési szolgáltatás üzembe helyezése befejeződött |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Akkor következik be, amikor két adatkészlet adateltolódás-észlelési feladata befejeződött |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Futási állapot megváltozásakor következik be, amely jelenleg csak akkor jelenik meg, ha a futtatási állapot "sikertelen". |

## <a name="subscribe-to-machine-learning-events"></a>Előfizetés Machine Learning eseményekre

A Azure Machine Learning eseményekre vonatkozó előfizetéseket szerepköralapú hozzáférés-vezérlés (RBAC) védi. A munkaterületek csak [közreműködői vagy tulajdonosai](how-to-assign-roles.md#default-roles) hozhatnak létre, frissíthetnek és törölhetnek esemény-előfizetéseket.

Az esemény-előfizetések különböző feltételek alapján szűrhetők. A szűrők az esemény-előfizetés [létrehozásakor](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) vagy [egy későbbi időpontban](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)is alkalmazhatók az események előfizetésére. 

### <a name="filter-by-event-type"></a>Szűrés eseménytípus szerint
Az esemény-előfizetés egy vagy több Azure Machine Learning eseménytípus megadására használható.

### <a name="filter-by-event-subject"></a>Szűrés esemény tárgya szerint
A Azure Event Grid a-től __kezdődően__ __a és a egyezéseknek megfelelően támogatja__ a tulajdonosi szűrőket, így a megfelelő tárgyú események az előfizetőnek lesznek továbbítva. A különböző gépi tanulási események formátuma eltérő.

| Eseménytípus | Tulajdonos formátuma | Minta tárgya |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Speciális szűrés

A Azure Event Grid a közzétett esemény sémája alapján is támogatja a speciális szűrést. Azure Machine Learning az esemény-séma részletei megtalálhatók a [Azure Machine Learning Azure Event Grid esemény sémájában](../event-grid/event-schema-machine-learning.md).

Néhány példaként használható speciális szűrés:

* `Microsoft.MachineLearningServices.ModelRegistered` Esemény esetén a modell címke értékének szűréséhez:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

A szűrők alkalmazásával kapcsolatos további információkért lásd: [Események szűrése Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Machine Learning események felhasználása

Az Machine Learning eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot követniük kell:

> [!div class="checklist"]
> * Mivel több előfizetést is konfigurálhat az események ugyanahhoz az eseménykezelőhöz való továbbításához, fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak, de az üzenet témakörének ellenőrzésével győződjön meg róla, hogy a várt Machine learning-munkaterületről származik.
> * Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
> * Mivel az üzenetek nem érkeznek meg a sorrendbe, és némi késés után a ETAG mezőkből megtudhatja, hogy az objektumokkal kapcsolatos információk továbbra is naprakészek-e.  Emellett a Sequencer mezőket is használhatja az események sorrendjének megismeréséhez egy adott objektumra vonatkozóan.
> * Figyelmen kívül hagyhatja a nem értelmezhető mezőket. Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
> * Sikertelen vagy megszakított Azure Machine Learning művelet nem indít eseményt. Ha például a modell központi telepítése sikertelen, a Microsoft. MachineLearningServices. ModelDeployed nem aktiválódik. Az alkalmazások tervezésekor vegye figyelembe az ilyen meghibásodási módot. Azure Machine Learning SDK-val, parancssori felülettel vagy portálral bármikor megtekintheti egy művelet állapotát, és megismerheti a hibák részletes okát.

Azure Event Grid lehetővé teszi, hogy az ügyfelek olyan, de egymással összekapcsolt üzenetkezelőket hozzanak létre, amelyeket Azure Machine Learning események indíthatnak el. Az üzenetkezelő néhány jelentős példája a következő:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory folyamat
* Általános webhookok, amelyek az Azure platformon vagy máshol is üzemeltethető

## <a name="next-steps"></a>További lépések

További információ a Event Gridről és a Azure Machine Learning események megadásáról:

- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
- [Azure Machine Learning Azure Event Gridi esemény sémája](../event-grid/event-schema-machine-learning.md)
- [Eseményvezérelt munkafolyamatok létrehozása Azure Machine Learning](how-to-use-event-grid.md)
