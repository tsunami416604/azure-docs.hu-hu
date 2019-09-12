---
title: HDInsight-fürt törlése – Azure
description: Információk az Azure HDInsight-fürtök törlésének különféle módjairól
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 08bfdcab0b7fbb8e533cb8d2d6a74d315ad4074c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885219"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával

A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. Ebből a dokumentumból megtudhatja, hogyan törölhet egy fürtöt a [Azure Portal](https://portal.azure.com), [Azure PowerShell az modul](https://docs.microsoft.com/powershell/azure/overview)és az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használatával.

> [!IMPORTANT]  
> Egy HDInsight-fürt törlése nem törli a fürthöz társított Azure Storage-fiókokat vagy Data Lake Storage. A jövőben az ezekben a szolgáltatásokban tárolt adatkészleteket is felhasználhatja.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali menüben navigáljon az **összes szolgáltatás** > **Analytics** > **HDInsight** -fürthöz, és válassza ki a fürtöt.

3. Az alapértelmezett nézetben válassza a **Törlés** ikont. A fürt törléséhez kövesse a parancssort.
   
    ![Törlés ikon](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell az Module

Az `CLUSTERNAME` alábbi kódban cserélje le a nevet a HDInsight-fürt nevére. A PowerShell-parancssorból írja be a következő parancsot a fürt törléséhez:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Cserélje `CLUSTERNAME` le a nevet a HDInsight-fürt nevére `RESOURCEGROUP` , az alábbi kódban pedig az erőforráscsoport nevére.  A parancssorból írja be a következőt a fürt törléséhez:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
