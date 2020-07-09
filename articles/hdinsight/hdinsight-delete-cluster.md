---
title: HDInsight-fürt törlése – Azure
description: Információk az Azure HDInsight-fürtök törlésének különféle módjairól
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 60f3ce0ad4f6ee6b1a38130867e0bcd1420620ef
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086364"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával

A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázást percenként értékeljük, ezért mindig törölje a fürtöt, ha már nincs használatban. Ebből a dokumentumból megtudhatja, hogyan törölhet egy fürtöt a [Azure Portal](https://portal.azure.com), [Azure PowerShell az modul](https://docs.microsoft.com/powershell/azure/overview)és az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használatával.

> [!IMPORTANT]  
> Egy HDInsight-fürt törlése nem törli a fürthöz társított Azure Storage-fiókokat vagy Data Lake Storage. A jövőben az ezekben a szolgáltatásokban tárolt adatkészleteket is felhasználhatja.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali menüben navigáljon az **összes szolgáltatás**  >  **Analytics**  >  **HDInsight** -fürthöz, és válassza ki a fürtöt.

3. Az alapértelmezett nézetben válassza a **Törlés** ikont. A fürt törléséhez kövesse a parancssort.

    ![HDInsight-fürt törlése gomb](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Az `CLUSTERNAME` alábbi kódban cserélje le a nevet a HDInsight-fürt nevére. A PowerShell-parancssorból írja be a következő parancsot a fürt törléséhez:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Cserélje le a `CLUSTERNAME` nevet a HDInsight-fürt nevére, az `RESOURCEGROUP` alábbi kódban pedig az erőforráscsoport nevére.  A parancssorból írja be a következőt a fürt törléséhez:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
