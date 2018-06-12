---
title: Telepítse át a frissített Azure Batch AI API |} Microsoft Docs
description: Azure Batch AI programkódok és parancsfájlok munkaterületen, és erőforrások kísérletezhet frissítése
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: b59173259aa86a429b9f926a8e5ffbfd046451a1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294877"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Telepítse át a frissített kötegelt AI API

A jövőbeli kötegelt AI REST API verziója 2018-05-01 és a kapcsolódó Batch AI SDK-k és eszközök, a jelentős változtatások és új funkciók éppen be.

Ha már használta a kötegelt AI API egy korábbi verziója, ez a cikk ismerteti hogyan lehet módosítani a programkódok és parancsfájlok használata az új API-t. Ezek a módosítások csak azután érhető el az új API-t.

## <a name="whats-changing"></a>Mi változik?

Az ügyfelek visszajelzéseire válaszolva azt a számot a feladatokról és megkönnyíti a kötegelt AI erőforrások kezelésére vonatkozó korlátozások szeretne eltávolítani. Az új API-t vezet be két új erőforrásokat, *munkaterület* és *kísérletezhet*. Kapcsolódó képzési feladatok egy kísérlet keretében gyűjt, és minden kapcsolódó kötegelt AI (fürtök, a fájlkiszolgálók, kísérleteket, feladatok) alatt lévő erőforrások munkaterület rendszerezéséhez.  

* **Munkaterület** -kötegelt AI erőforrások minden típusú legfelső szintű gyűjteménye. Fürtök és a fájlkiszolgálók a munkaterület tartalmazza. A munkaterületek között a különböző csoportokhoz vagy projektekhez tartozó munkahelyi általában külön. Például lehetséges, hogy a fejlesztési és tesztelési munkaterület. Valószínűleg szüksége munkaterületek előfizetésenként csak korlátozott számú. 

* **Kísérlet** -lekérdezett és felügyelete együtt, kapcsolódó feladatok gyűjteménye. Például egy kísérlet segítségével a hyper-paraméter hangolási ismétlés részeként elvégzett összes csoportosítva. 

Az alábbi ábrán egy példa erőforrás-hierarchiában. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Figyelheti és kezelheti a meglévő erőforrásokat
Minden erőforráscsoportban, amelyben már létrehozta kötegelt AI fürtök, feladatokat vagy fájlkiszolgálók, a kötegelt AI szolgáltatás létrehoz egy munkaterület nevű `migrated-<region>` (például `migrated-eastus`) és a kísérlet nevű `migrated`. A korábban létrehozott feladatokat, a fürtök vagy a fájlkiszolgálók eléréséhez szeretné használni az áttelepített munkaterületet, és próbálja. 

### <a name="portal"></a>Portál 
A portál használatával korábban létrehozott feladatok, fürtökhöz vagy fájlkiszolgálók eléréséhez először válassza ki azt a `migrated-<region>` munkaterületen. Miután kiválasztotta a munkaterület, például átméretezése vagy a fürtök törlésével és a feladat állapotának és kimenetének megtekintése műveleteket. 

### <a name="sdks"></a>SDK-k 
Feladatok, fürtökhöz vagy a kötegelt AI SDK-k segítségével korábban létrehozott fájlkiszolgálók eléréséhez adja meg a munkaterület neve, és name paraméterei kipróbálásához. 

A Python SDK használatakor az alábbi példák láthatók megfelelő módosításokat. A kötegelt AI Csomagjától a hasonlóak a módosításokat. 


#### <a name="get-old-cluster"></a>Régi fürt beolvasása 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>Törölje a régi fürtből 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>Régi fájlkiszolgáló beolvasása 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>Törölje a régi fájlkiszolgáló  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>Régi feladat beolvasása 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>Régi feladat törlése

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Az Azure parancssori felület korábban létrehozott access feladatok, fürtök és fájlkiszolgálók használatakor használjon a `-w` és `-e` paramétereit, és adja meg a munkaterületet, és próbálja nevét. 


#### <a name="get-old-cluster"></a>Régi fürt beolvasása

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>Törölje a régi fürtből 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>Régi fájlkiszolgáló beolvasása

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>Törölje a régi fájlkiszolgáló 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>Régi feladat beolvasása

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>Régi feladat törlése 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Kötegelt AI erőforrások létrehozása 
 
Használata a meglévő kötegelt AI SDK-k közül, továbbra is létrehozhatja a kötegelt AI erőforrások (feladatok, fürtökhöz vagy fájlkiszolgálók) kód módosítása nélkül. Azonban az új SDK frissítése után kell a következő módosításokat.
 
### <a name="create-workspace"></a>Munkaterület létrehozása 
A forgatókönyvtől függően létrehozhat egy munkaterület manuálisan egyszeri a portál vagy a parancssori felületen keresztül. A parancssori felület használata, hozzon létre egy munkaterület a következő parancsot: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Kísérlet létrehozása 


A forgatókönyvtől függően létrehozhat egy kísérlet manuálisan egyszeri a portál vagy a parancssori felületen keresztül. A parancssori felület használata, hozzon létre egy kísérlet a következő parancsot: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Fürtök, a fájlkiszolgálók és a feladatok létrehozása
 
Ha a portál használatával hozzon létre feladatokat, fürtökhöz vagy fájlkiszolgálókon, a portál végigvezeti adja meg a munkaterület neve és a name paraméterek kísérletezhet az létrehozása során.

Feladatok létrehozására, fürtökhöz vagy fájlkiszolgálókon keresztül a frissített SDK-t, adja meg a munkaterület neve paramétert. A Python SDK használatakor az alábbi példák láthatók megfelelő módosításokat. Cserélje le *workspace_name* és *experiment_name* a munkaterület és a korábban létrehozott kísérlet. A kötegelt AI Csomagjától a hasonlóak a módosításokat. 


#### <a name="create-cluster"></a>Fürt létrehozása 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>Fájlkiszolgáló létrehozása 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>Feladat létrehozása 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>További lépések

* Tekintse meg a kötegelt AI API-referencia: [CLI](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai), és [REST](/rest/api/batchai)