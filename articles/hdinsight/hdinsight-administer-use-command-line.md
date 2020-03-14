---
title: Azure HDInsight-fürtök kezelése az Azure CLI-vel
description: Ismerje meg, hogyan kezelheti az Azure HDInsight-fürtöket az Azure CLI használatával. A fürtök típusai közé tartoznak a Apache Hadoop, a Spark, a HBase, a Storm, a Kafka, az interaktív lekérdezés és a ML-szolgáltatások.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272772"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure HDInsight-fürtök kezelése az Azure CLI-vel

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Ismerje meg, hogyan kezelheti az Azure HDInsight-fürtöket az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) használatával. Az Azure parancssori felülete (CLI) a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure CLI. Ha még nem telepítette az Azure CLI-t, olvassa el a következő témakört: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) .

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Jelentkezzen be az Azure-előfizetésbe. Ha azt tervezi, hogy Azure Cloud Shell használ, válassza a **kipróbálás** lehetőséget a kód blokk jobb felső sarkában. Máskülönben adja meg az alábbi parancsot:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Fürtök listázása

Fürtök listázása [az az hdinsight List](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) paranccsal. Szerkessze az alábbi parancsokat úgy, hogy lecseréli `RESOURCE_GROUP_NAME` az erőforráscsoport nevére, majd megírja a parancsokat:

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

Az [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) paranccsal jelenítheti meg egy adott fürt információit. Szerkessze az alábbi parancsot úgy, hogy lecseréli a `RESOURCE_GROUP_NAME`t, és `CLUSTER_NAME` a megfelelő információkkal, majd beírja a következő parancsot:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Fürtök törlése

A megadott fürt törléséhez használja [az az hdinsight delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) paranccsal. Szerkessze az alábbi parancsot úgy, hogy lecseréli a `RESOURCE_GROUP_NAME`t, és `CLUSTER_NAME` a megfelelő információkkal, majd beírja a következő parancsot:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

A fürtöt a fürtöt tartalmazó erőforráscsoport törlésével is törölheti. Vegye figyelembe, hogy ez a csoport összes erőforrását törli, beleértve az alapértelmezett Storage-fiókot is.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Fürtök méretezése

Az [az hdinsight Resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) paranccsal méretezze át a megadott hdinsight-fürtöt a megadott méretre. Az alábbi parancs szerkesztésével cserélje le a `RESOURCE_GROUP_NAME`t, és `CLUSTER_NAME` a megfelelő információkkal. Cserélje le a `WORKERNODE_COUNT`t a fürthöz tartozó munkavégző csomópontok kívánt számával. A fürtök méretezésével kapcsolatos további információkért lásd: [HDInsight-fürtök](./hdinsight-scaling-best-practices.md)méretezése. Adja meg a parancsot:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan végezheti el a különböző HDInsight-fürtök felügyeleti feladatait. További információt a következő cikkekben talál:

* [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](hdinsight-administer-use-portal-linux.md)
* [HDInsight felügyelete Azure PowerShell használatával](hdinsight-administer-use-powershell.md)
* [Ismerkedés az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Az Azure CLI használatának első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
