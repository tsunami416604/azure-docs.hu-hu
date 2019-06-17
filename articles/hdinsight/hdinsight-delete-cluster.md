---
title: Egy HDInsight-fürt – Azure törlése
description: Információk a különböző módszereket, hogy egy HDInsight-fürtöt is törli.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721016"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>A böngészőben, PowerShell vagy az Azure CLI használatával egy HDInsight-fürt törlése

A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. Ebből a dokumentumból megismerheti, hogyan törölni egy fürt használatával a [az Azure portal](https://portal.azure.com), [Az Azure PowerShell modul](https://docs.microsoft.com/powershell/azure/overview), és a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Egy HDInsight-fürt törlése nem törli az Azure Storage-fiókokat, vagy a fürthöz tartozó Data Lake Storage. Újból felhasználhatja ezeket a szolgáltatásokat a jövőben tárolt adatokat.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Lépjen a bal oldali menüből **minden szolgáltatás** > **Analytics** > **HDInsight-fürtök** , és válassza ki a fürtöt.

3. Az alapértelmezett nézetet, válassza ki a **törlése** ikonra. Törölje a fürtöt a felszólítást követve.
   
    ![Törlés ikon](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell-Az-modul

Cserélje le `CLUSTERNAME` az alábbi kódot a HDInsight-fürt nevére. Egy PowerShell-parancssort adja meg a következő parancsot a fürt törléséhez:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Cserélje le `CLUSTERNAME` a HDInsight-fürt nevére, és `RESOURCEGROUP` az alábbi kód az erőforráscsoport nevét.  Egy parancssorból írja be a következő, a fürt törléséhez:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
