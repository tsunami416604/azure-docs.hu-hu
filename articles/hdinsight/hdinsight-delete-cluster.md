---
title: "HDInsight-fürtök - Azure törlése |} Microsoft Docs"
description: "Információ a különböző módszereket, hogy egy HDInsight-fürt törlése."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 04b89b2cceb18a0e3c88d0d1deada1a05b8187f6
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>A böngésző, PowerShell vagy az Azure parancssori felület használatával HDInsight-fürtök törlése

A HDInsight fürt számlázás követően egy fürt jön létre, és leállítja a fürt törlésekor indul. Az elszámolás percalapú, ezért a fürtöt mindig törölje, ha az már nincs használatban. Ebből a dokumentumból megismerheti, hogyan törlése az Azure portál, Azure PowerShell és az Azure CLI 1.0 egy fürtöt.

> [!IMPORTANT]
> HDInsight-fürtök törlése nem érinti az Azure Storage-fiókokat, vagy a Data Lake Store a fürthöz rendelt. Ezek a szolgáltatások a jövőben tárolt adatokat is felhasználhatja.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) válassza ki a HDInsight-fürthöz. Ha a HDInsight-fürt nincs rögzítve az irányítópulton, kereshet, használja a keresőmezőt név szerint.
   
    ![portál keresési](./media/hdinsight-delete-cluster/navbar.png)

2. A fürtbeállítások, válassza ki a **törlése** ikonra. Amikor a rendszer kéri, válassza ki a **Igen** törölni a fürtöt.
   
    ![Törlés ikonja](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Egy PowerShell-parancssorból a következő parancs segítségével törölheti a fürtöt:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

## <a name="azure-cli-10"></a>Azure CLI 1.0

A parancssorba használja a következő törölni a fürtöt:

    azure hdinsight cluster delete CLUSTERNAME

Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

> [!NOTE]
> Az Azure CLI 2.0 nem támogatja a HDInsight-fürtök törlése most (2017. október 23.).