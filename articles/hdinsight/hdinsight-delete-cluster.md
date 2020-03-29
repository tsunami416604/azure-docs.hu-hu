---
title: HDInsight-fürt törlése - Azure
description: Információ az Azure HDInsight-fürtök törlésének különböző módjairól
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807136"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával

A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percenkénti arányos, ezért mindig törölje a fürtöt, ha már nincs használatban. Ebben a dokumentumban megtudhatja, hogyan törölhet egy fürtöt az [Azure Portal](https://portal.azure.com), az [Azure PowerShell Az modul](https://docs.microsoft.com/powershell/azure/overview)és az Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használatával.

> [!IMPORTANT]  
> HdInsight-fürt törlése nem törli az Azure Storage-fiókok vagy a fürthöz társított Data Lake Storage. A jövőben újra felhasználhatja az ezekben a szolgáltatásokban tárolt adatokat.

## <a name="azure-portal"></a>Azure portál

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. A bal oldali menüben keresse meg **az Összes szolgáltatás** > **Analytics** > **HDInsight-fürtjét,** és válassza ki a fürtöt.

3. Az alapértelmezett nézetben válassza a **Törlés** ikont. A fürt törléséhez kövesse a gombot.

    ![A HDInsight fürttörlése gomb](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Cserélje `CLUSTERNAME` le a HDInsight-fürt nevét az alábbi kódban. A PowerShell-parancssorból írja be a következő parancsot a fürt törléséhez:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Cserélje `CLUSTERNAME` le a HDInsight-fürt `RESOURCEGROUP` nevét, és az erőforráscsoport nevét az alábbi kódban.  A fürt törléséhez írja be a parancssorból az alábbi parancsot:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
