---
title: Egy HDInsight-fürt – Azure törlése
description: Információk a különböző módszereket, hogy egy HDInsight-fürtöt is törli.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 737cc120877a9d0f06a1f6d209bcf9a233aa7d19
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091309"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>A böngészőben, PowerShell vagy az Azure CLI használatával egy HDInsight-fürt törlése

A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. Ebből a dokumentumból megismerheti, hogyan törölni egy fürtöt az Azure Portalon, az Azure PowerShell és az Azure CLI 1.0 használatával.

> [!IMPORTANT]
> Egy HDInsight-fürt törlése nem törli az Azure Storage-fiókokat, vagy a fürthöz tartozó Data Lake Store. Újból felhasználhatja ezeket a szolgáltatásokat a jövőben tárolt adatokat.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és válassza ki a HDInsight-fürtöt. A HDInsight-fürt nincs rögzítve az irányítópulton, ha Ön is megkeresheti azt a keresőmező használatával név szerint.
   
    ![portál keresési](./media/hdinsight-delete-cluster/navbar.png)

2. A fürt beállításait, válassza ki a **törlése** ikonra. Amikor a rendszer kéri, válassza ki a **Igen** törölje a fürtöt.
   
    ![Törlés ikon](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell parancssorból használja a következő parancsot a fürt törléséhez:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

## <a name="azure-cli-10"></a>Azure CLI 1.0

A parancssorba a következő használatával a fürt törléséhez:

    azure hdinsight cluster delete CLUSTERNAME

Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

> [!NOTE]
> Az Azure CLI 2.0 nem támogatja a HDInsight-fürtök törlése jelenleg (2017. október 23).
