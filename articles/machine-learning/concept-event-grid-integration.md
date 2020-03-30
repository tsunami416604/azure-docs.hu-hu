---
title: Azure Machine Learning-események felhasználása
titleSuffix: Azure Machine Learning
description: Ebből a cikkből megtudhatja, hogyan fizethet elő, reagálhat és iratkozhat le az Azure Machine Learning által létrehozott eseményekre az Azure Event Grid használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139045"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Azure Machine Learning-események felhasználása (előzetes verzió)

Az Azure Machine Learning kezeli a gépi tanulási folyamat teljes életciklusát, beleértve a modellbetanítást, a modellüzembe helyezést és a figyelést. Az Azure Machine Learning-események lehetővé teszik, hogy az alkalmazások reagáljanak a gépi tanulási életciklus során berendezkedő eseményekre, például a betanítási futtatások befejezésére, a modellek regisztrációjára és telepítésére, valamint az adateltolódás észlelésére a modern kiszolgáló nélküli Architektúrák. 

Ezek az események az [Azure Event Griden](https://azure.microsoft.com/services/event-grid/)keresztül jelennek meg. Az Azure Portal, a Powershell vagy az Azure CLI használatával az ügyfelek egyszerűen előfizethetnek [eseményeket egy vagy több eseménytípus megadásával és a szűrési feltételekkel.](/azure/event-grid/event-filtering) Az ügyfelek is választhatnak, hogy hozzon létre egy széles körű eseménykezelők, például az Azure Functions, Az Azure Logic Apps, vagy általános webhooks. Az Azure Machine Learning az Azure Event Griddel együtt magas rendelkezésre állású, megbízható és hibatűrő eseménykézbesítési platformot biztosít az eseményvezérelt alkalmazások létrehozásához.

Az Azure Machine Learning eseményrácsmal való használatáról az [Eseményvezérelt gépi tanulási munkafolyamatok létrehozása (előzetes verzió) című](how-to-use-event-grid.md)témakörben talál további információt.

## <a name="the-event-model"></a>Az eseménymodell 

Az Azure Event Grid beolvassa az eseményeket forrásokból, például az Azure Machine Learningből és más Azure-szolgáltatásokból. Ezeket az eseményeket ezután elküldi az eseménykezelők, például az Azure Event Hubs, az Azure Functions, a Logic Apps és mások. Az alábbi ábra bemutatja, hogy az Event Grid hogyan kapcsolja össze a forrásokat és a kezelőket, de nem a támogatott integrációk átfogó listája.

![Az Azure Event Grid funkcionális modellje](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Az eseményforrásokról és az eseménykezelőkről a [Mi az eseményrács?](/azure/event-grid/overview)

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning-eseménytípusok

Az Azure Machine Learning a gépi tanulási életciklus különböző pontjain biztosít eseményeket: 

| Eseménytípus | Leírás |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Gépi tanulási kísérlet futtatásakor |
| `Microsoft.MachineLearningServices.ModelRegistered` | Gépi tanulási modell regisztrálása a munkaterületen |
| `Microsoft.MachineLearningServices.ModelDeployed` | A következtetési szolgáltatás egy vagy több modellel történő telepítésének befejezésekor |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Két adatkészlet adatátlódás-észlelési feladatának befejezésekor |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Ha egy futtatási állapot megváltozott, jelenleg csak akkor merül fel, ha a futtatási állapot "sikertelen" |

## <a name="subscribe-to-machine-learning-events"></a>Feliratkozás a Machine Learning-eseményekre

Az Azure Machine Learning-események előfizetéseit szerepköralapú hozzáférés-vezérlés (RBAC) védi. Csak [a közreműködő vagy](how-to-assign-roles.md#default-roles) a munkaterület tulajdonosa hozhat létre, frissíthet és törölhet esemény-előfizetéseket.

Az esemény-előfizetések különböző feltételek alapján szűrhetők. A szűrők az esemény-előfizetések [létrehozása](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) során vagy [egy későbbi időpontban](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)alkalmazhatók az esemény-előfizetésekre. 

### <a name="filter-by-event-type"></a>Szűrés eseménytípus szerint
Egy esemény-előfizetés egy vagy több Azure Machine Learning-eseménytípust adhat meg.

### <a name="filter-by-event-subject"></a>Szűrés eseménytárgy szerint
Az Azure Event Grid támogatja a tárgyszűrőket az egyezésekkel __kezdődő__ és __végződő események__ alapján, így a megfelelő tantárgyú események et az előfizető nek kell kézbesíteni. A különböző gépi tanulási események különböző tárgyformátumúak.

| Eseménytípus | Tárgy formátuma | Minta tárgy |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Speciális szűrés

Az Azure Event Grid is támogatja a közzétett eseményséma alapú speciális szűrést. Az Azure Machine Learning eseménysémájának részletei az [Azure Event Grid eseménysémájában találhatók az Azure Machine Learninghez.](../event-grid/event-schema-machine-learning.md)

Néhány speciális szűrést végezhet:

* Esemény `Microsoft.MachineLearningServices.ModelRegistered` esetén a modell címkeértékének szűrése:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

A szűrők alkalmazásáról az [Eseményrács eseményeinek szűrése (Eseményrács) témakörben olvashat bővebben.](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)

## <a name="consume-machine-learning-events"></a>Gépi tanulási események felhasználása

A Machine Learning-eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot kell követniük:

> [!div class="checklist"]
> * Mivel több előfizetés is konfigurálható úgy, hogy az eseményeket ugyanarra az eseménykezelőre irányítsa, fontos, hogy ne feltételezze, hogy az események egy adott forrásból származnak, hanem ellenőrizze az üzenet témakörét, és győződjön meg arról, hogy az a várt gépi tanulási munkaterületről származik.
> * Hasonlóképpen ellenőrizze, hogy az eventType olyan, amelyet fel kell dolgoznia, és ne feltételezze, hogy az összes kapott esemény a várt típusú lesz.
> * Mivel az üzenetek nem sorrendben érkezhetnek, és némi késéssel, az etag mezők segítségével tisztában lehet azzal, hogy az objektumokkal kapcsolatos adatai még mindig naprakészek-e.  Emellett a szekvenszer mezők segítségével megismerheti az események sorrendjét egy adott objektumon.
> * Figyelmen kívül hagyja azolyan mezőket, amelyeket nem ért. Ez a gyakorlat segít megőrizni a rugalmas új funkciók, amelyek a jövőben hozzáadott.
> * A sikertelen vagy megszakított Azure Machine Learning-műveletek nem váltanak ki eseményt. Ha például egy modell központi telepítése sikertelen, a Microsoft.MachineLearningServices.ModelDeployed nem aktiválódik. Fontolja meg az ilyen hiba módot az alkalmazások tervezésekor. Az Azure Machine Learning SDK, CLI vagy portál használatával mindig ellenőrizheti egy művelet állapotát, és megismerheti a részletes hibaok.

Az Azure Event Grid lehetővé teszi az ügyfelek számára, hogy a kapcsolt üzenetkezelők, amelyek az Azure Machine Learning-események által kiváltott. Néhány említésre méltó példa az üzenetkezelőkre:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure data factory folyamat
* Általános webhookok, amelyek az Azure platformon vagy máshol is üzemeltethetők

## <a name="next-steps"></a>További lépések

Tudjon meg többet az Event Gridről, és próbálja ki az Azure Machine Learning-eseményeket:

- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
- [Azure Event Grid eseménysémája az Azure Machine Learninghez](../event-grid/event-schema-machine-learning.md)
- [Eseményvezérelt munkafolyamatok létrehozása az Azure Machine Learning segítségével](how-to-use-event-grid.md)
