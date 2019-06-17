---
title: Azure CLI használatával Azure HDInsight-fürtök kezelése
description: Ismerje meg, hogyan Azure HDInsight-fürtök kezelése az Azure CLI használatával. Fürttípusok Apache Hadoop, Spark, HBase, Storm, Kafka, interaktív lekérdezési és gépi Tanulási szolgáltatások közé tartozik.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 7c12831c43762ddc776e8d5701f002be97992cbc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65859962"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure CLI használatával Azure HDInsight-fürtök kezelése

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Ismerje meg, hogyan használható [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) Azure HDInsight-fürtök kezeléséhez. Az Azure parancssori felülete (CLI) a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure CLI. Ha még nem telepítette az Azure CLI-vel, tekintse meg [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) lépéseit.

* Egy HDInsight az Apache Hadoop-fürtöt. Lásd: [HDInsight Linux első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Jelentkezzen be az Azure-előfizetéshez. Ha azt tervezi, hogy az Azure Cloud Shell használatához, majd egyszerűen válassza **kipróbálás** a kódblokk jobb felső sarkában. Más esetben adja meg az alábbi parancsot:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Fürtök listázása

Használat [az hdinsight lista](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) a fürtök listázása. Az alábbi parancsokat szerkesztése lecserélésével `RESOURCE_GROUP_NAME` az erőforráscsoport nevét, majd adja meg a parancsok:

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

Használat [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) megjelennek az adott fürt számára. Az alábbi parancsot szerkesztése lecserélésével `RESOURCE_GROUP_NAME`, és `CLUSTER_NAME` a megfelelő adatokat, majd adja meg a parancsot:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Fürtök törlése

Használat [az hdinsight törlése](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) megadott fürt törlése. Az alábbi parancsot szerkesztése lecserélésével `RESOURCE_GROUP_NAME`, és `CLUSTER_NAME` a megfelelő adatokat, majd adja meg a parancsot:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

A fürt törölheti az erőforráscsoportot, amely tartalmazza a fürt törlésével is. Vegye figyelembe, hogy ez a művelet törli a csoportot, beleértve az alapértelmezett tárfiók található összes erőforrást.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Fürtök méretezése

Használat [az hdinsight átméretezése](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) méretezze át a megadott HDInsight-fürt a megadott mérethez. Az alábbi parancsot szerkesztése lecserélésével `RESOURCE_GROUP_NAME`, és `CLUSTER_NAME` a megfelelő adatokat. Cserélje le `TARGET_INSTANCE_COUNT` az a fürt munkavégző csomópontok kívánt száma. Fürtök méretezésével kapcsolatos további információkért lásd: [méretezési HDInsight-fürtök](./hdinsight-scaling-best-practices.md). Adja meg a parancsot:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulhatta, hogyan hajthat végre különböző HDInsight fürt felügyeleti feladatokat. További tudnivalókért tekintse meg a következő cikkeket:

* [A HDInsight Apache Hadoop-fürtök kezelése az Azure portal használatával](hdinsight-administer-use-portal-linux.md)
* [HDInsight felügyelete az Azure PowerShell használatával](hdinsight-administer-use-powershell.md)
* [Az Azure HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure CLI használatának első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
