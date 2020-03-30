---
title: Azure HDInsight-fürtök kezelése az Azure CLI használatával
description: Ismerje meg, hogyan használhatja az Azure CLI-t az Azure HDInsight-fürtök kezelésére. A fürttípusok közé tartozik az Apache Hadoop, a Spark, a HBase, a Storm, a Kafka, az Interaktív lekérdezés és az ML-szolgáltatások.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272772"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure HDInsight-fürtök kezelése az Azure CLI használatával

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Ismerje meg, hogyan kezelheti az Azure HDInsight-fürtöket az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) használatával. Az Azure parancssori felülete (CLI) a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Azure CLI. Ha még nem telepítette az Azure CLI-t, a lépések [telepítéséről az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) című témakörben található.

* Apache Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](hadoop/apache-hadoop-linux-tutorial-get-started.md)

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Jelentkezzen be Azure-előfizetésbe. Ha az Azure Cloud Shell használatát tervezi, válassza a **Próbálja ki** a kódblokk jobb felső sarkában. Máshol, belép a követel alul:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Fürtök listázása

Az [hdinsight-lista használatával](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) listázza a fürtöket. Szerkessze az `RESOURCE_GROUP_NAME` alábbi parancsokat az erőforráscsoport nevének cseréjével, majd írja be a parancsokat:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Fürt megjelenítése

Az [hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) használatával egy adott fürt adatait jelenítheti meg. Az alábbi parancs szerkesztése `CLUSTER_NAME` a lehetőség és a megfelelő információk lecserélésével, `RESOURCE_GROUP_NAME`majd írja be a parancsot:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Fürtök törlése

Az [hdinsight delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) használatával törölhet idáig egy adott fürtöt. Az alábbi parancs szerkesztése `CLUSTER_NAME` a lehetőség és a megfelelő információk lecserélésével, `RESOURCE_GROUP_NAME`majd írja be a parancsot:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Fürt törléséhez törölje a fürtöt tartalmazó erőforráscsoportot is. Megjegyzés: ezzel törli a csoport összes erőforrását, beleértve az alapértelmezett tárfiókot is.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Fürtök méretezése

Az [hdinsight átméretezése](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) a megadott HDInsight-fürt a megadott méretre. Az alábbi parancs szerkesztése `CLUSTER_NAME` a lehetőség és a vonatkozó információk lecserélésével. `RESOURCE_GROUP_NAME` Cserélje `WORKERNODE_COUNT` le a fürt kívánt munkavégző csomópontjainak helyére. A fürtök méretezéséről a [HDInsight-fürtök méretezése](./hdinsight-scaling-best-practices.md)című témakörben talál további információt. Írja be a parancsot:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hajthatja végre a különböző HDInsight-fürtfelügyeleti feladatokat. További információ: a következő cikkek:

* [Apache Hadoop-fürtök kezelése a HDInsightban az Azure Portal használatával](hdinsight-administer-use-portal-linux.md)
* [A HDInsight felügyelete az Azure PowerShell használatával](hdinsight-administer-use-powershell.md)
* [Ismerkedés az Azure HDInsight-mal](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Az Azure CLI használatának első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
