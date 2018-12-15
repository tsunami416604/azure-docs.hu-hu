---
title: Frissített az Azure Batch AI API migrálása |} A Microsoft Docs
description: Az Azure Batch AI programkódok és parancsfájlok munkaterületet használja, és kísérletezzen az erőforrások frissítése
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
ROBOTS: NOINDEX
ms.openlocfilehash: 75a9a5e9bafd62b320397c00ef6574b7536d9e09
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407780"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>A Batch AI frissített API áttelepítése

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

A Batch AI – REST API verzió 2018-05-01-es és a kapcsolódó Batch AI SDK-k és eszközök jelentős változásokat és az új funkciók jelentek.

Ha a Batch AI API korábbi verzióját már használta, ez a cikk azt ismerteti, hogyan módosíthatja a kódot és az új API-szkriptek. 

## <a name="whats-changing"></a>Mi változik?

Az ügyfelek visszajelzéseire válaszolva azt szeretne eltávolítani a feladatokat, és könnyebbé teszi a Batch AI-erőforrások kezeléséhez vonatkozó korlátok. Az új API mutatja be két új erőforrást, *munkaterület* és *kísérletezhet*. Egy kísérlet keretében kapcsolódó feladatok gyűjtése, és minden kapcsolódó Batch AI-erőforrások rendszerezése (fürtök, a fájlkiszolgálók, kísérletek, feladatok) alatt egy munkaterületet.  

* **Munkaterület** – legfelső szintű gyűjtemény összes különböző típusú Batch AI erőforrás. Fürtök és a fájlkiszolgálók munkaterület tartalmazza. Munkaterületek a különböző csoportok vagy projektekhez tartozó munkahelyi általában külön. Előfordulhat például, hogy a fejlesztési és tesztelési munkaterület. Valószínűleg szüksége lesz a munkaterületek száma előfizetésenként csak korlátozott számú. 

* **Kísérlet** – egy gyűjtemény kapcsolódó feladatok, amelyek lekérdezhetők és felügyelete együtt. Például egy kísérlet használatával csoport összes feladatot, amely a hyper-finomhangolási paraméteres részeként történik. 

Az alábbi képen egy példa erőforrás-hierarchia. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Figyelheti és kezelheti a meglévő erőforrások
Az egyes erőforráscsoportokban, ahol már létrehozta a Batch AI-fürtök, feladatok, vagy fájlkiszolgálók, a Batch AI szolgáltatás létrehoz egy nevű munkaterület `migrated-<region>` (például `migrated-eastus`) és a egy kísérlet nevű `migrated`. Hozzáférhet a korábban létrehozott feladatokat, a fürtök vagy a fájlkiszolgálókat, meg kell az áttelepített munkaterületet használja, és kísérletezzen. 

### <a name="portal"></a>Portál 
A portál használatával korábban létrehozott feladatokat, a fürtök vagy a fájlkiszolgálók eléréséhez először válassza ki a `migrated-<region>` munkaterületen. Miután kiválasztotta a munkaterületet, vagy egyéb műveleteket átméretezése vagy a fürtök törlésével, és a feladat állapotát és kimeneteinek megtekintése. 

### <a name="sdks"></a>SDK-k 
Hozzáférhet a feladatok, a fürtök vagy a korábban létrehozott keresztül a Batch AI SDK-k fájlkiszolgálókat, adja meg a munkaterület nevét, és kísérletezzen a név paraméterekhez. 

Ha a Python SDK-t használ, megfelelő módosításokat az alábbi példák láthatók. Módosítások esetén is hasonlók a többi Batch AI SDK-t. 


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
 
Ha korábban létrehozott hozzáférési feladatok, a fürtök vagy a fájlkiszolgálók az Azure CLI-vel, a `-w` és `-e` paramétereket adja meg a munkaterületet, és kísérletezzen a neveket. 


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

## <a name="create-batch-ai-resources"></a>Batch AI-erőforrások létrehozása 
 
A már meglévő Batch AI SDK-t használ, ha továbbra is létrehozhatja a Batch AI-erőforrások (a feladatok, a fürtök vagy a fájlkiszolgálók) anélkül, hogy a kódon módosítani kellene. Azonban az új SDK frissítése után kell a következő módosításokat.
 
### <a name="create-workspace"></a>Munkaterület létrehozása 
A forgatókönyvtől függően egy munkaterületet hozhat létre, manuálisan egyszeri a portálon vagy a parancssori felületen keresztül. Ha a parancssori Felületet használ, hozzon létre egy munkaterületet, a következő paranccsal: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Kísérlet létrehozása 


A forgatókönyvtől függően egy kísérletet hozhat létre, manuálisan egyszeri a portálon vagy a parancssori felületen keresztül. Ha a parancssori felület használata esetén hozzon létre egy kísérlet a következő paranccsal: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Fürtök, fájlkiszolgálókon és feladatok létrehozása
 
Ha a portál használatával hozzon létre feladatokat, a fürtök vagy a fájlkiszolgálókat, a portál végigvezeti adja meg a munkaterület nevét, és kísérletezzen a name paraméterek a létrehozása során.

Feladatok létrehozására, fürtök vagy fájlkiszolgálók használatával a frissített SDK-t, adja meg a munkaterület neve paramétert. Ha a Python SDK-t használ, megfelelő módosításokat az alábbi példák láthatók. Cserélje le *workspace_name* és *experiment_name* a munkaterület és a korábban létrehozott kísérlet. Módosítások esetén is hasonlók a többi Batch AI SDK-t. 


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

* Tekintse meg a Batch AI API-referencia: [Parancssori felület](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai), és [REST](/rest/api/batchai)