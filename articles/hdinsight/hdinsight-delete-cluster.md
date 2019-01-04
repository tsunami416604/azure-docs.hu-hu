---
title: Egy HDInsight-fürt – Azure törlése
description: Információk a különböző módszereket, hogy egy HDInsight-fürtöt is törli.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f5c2f6104a765c0e598e41234891c492686019dd
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718979"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Egy HDInsight-fürtön a böngészőben, a PowerShell vagy a klasszikus Azure-CLI törlése

A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. Ebből a dokumentumból megismerheti, hogyan törölni egy fürt használatával a [az Azure portal](https://portal.azure.com), [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/), és a klasszikus Azure-CLI.

> [!IMPORTANT]  
> Egy HDInsight-fürt törlése nem törli az Azure Storage-fiókokat, vagy a fürthöz tartozó Data Lake Storage. Újból felhasználhatja ezeket a szolgáltatásokat a jövőben tárolt adatokat.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és válassza ki a HDInsight-fürtöt. A HDInsight-fürt nincs rögzítve az irányítópulton, ha Ön is megkeresheti azt a keresőmező használatával név szerint.
   
    ![portál keresési](./media/hdinsight-delete-cluster/navbar.png)

2. A fürt beállításait, válassza ki a **törlése** ikonra. Amikor a rendszer kéri, válassza ki a **Igen** törölje a fürtöt.
   
    ![Törlés ikon](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell parancssorból használja a következő parancsot a fürt törléséhez:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

## <a name="azure-classic-cli"></a>Az Azure klasszikus parancssori felület

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

A parancssorba a következő használatával a fürt törléséhez:

    azure hdinsight cluster delete CLUSTERNAME

Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.
