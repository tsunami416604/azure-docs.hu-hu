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
ms.openlocfilehash: 4df4fa29722dd3ad33cf1ce123877f04f9f4b4c1
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360388"
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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell parancssorból használja a következő parancsot a fürt törléséhez:

    Remove-AzHDInsightCluster -ClusterName CLUSTERNAME

Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

## <a name="azure-classic-cli"></a>Az Azure klasszikus parancssori felület

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

A parancssorba a következő használatával a fürt törléséhez:

    azure hdinsight cluster delete CLUSTERNAME

Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.
