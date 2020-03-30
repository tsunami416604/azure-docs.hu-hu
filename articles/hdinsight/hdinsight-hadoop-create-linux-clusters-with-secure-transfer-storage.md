---
title: Az Apache Hadoop biztonságos átviteli tároló& - Azure HDInsight
description: Megtudhatja, hogyan hozhat létre biztonságos átvitelű Azure-tárfiókokkal rendelkező HDInsight-fürtöket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560082"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop-fürtök biztonságos átviteli tárfiókokkal az Azure HDInsightban

A [biztonságos átvitelt szükséges](../storage/common/storage-require-secure-transfer.md) funkció megnöveli az Azure Storage-fiók biztonságát azáltal, hogy minden, a fiókra beérkező kérést biztonságos kapcsolat használatára kényszerít. Ezt a funkciót és a wasbs sémát csak a HDInsight-fürt 3.6-os vagy újabb verziója támogatja.

> [!IMPORTANT]
> A fürt létrehozása utáni biztonságos tárolás engedélyezése hibákat okozhat a tárfiók használatával, és nem ajánlott. Jobb, ha egy új fürt segítségével egy tárfiókot biztonságos átvitel már engedélyezve van.

## <a name="storage-accounts"></a>Tárfiókok

### <a name="azure-portal"></a>Azure portál

Alapértelmezés szerint a biztonságos átadásszükséges tulajdonság engedélyezve van, amikor létrehoz egy tárfiókot az Azure Portalon.

Meglévő tárfiók frissítése az Azure Portalon, [lásd: Biztonságos átvitel megkövetelése az Azure Portalon.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)

### <a name="powershell"></a>PowerShell

A [PowerShell Új-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)parancsmag esetén `-EnableHttpsTrafficOnly` győződjön `1`meg arról, hogy a paraméter beállítása .

Meglévő tárfiók powershellnel való frissítéséhez olvassa [el a Biztonságos átvitel megkövetelése a PowerShell használatával.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI parancs [az a tárfiók létrehozása,](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)győződjön meg arról, paraméter `--https-only` van `true`beállítva.

Meglévő tárfiók frissítése az Azure CLI-vel, [lásd: Biztonságos átvitel megkövetelése az Azure CLI-vel.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)

## <a name="add-additional-storage-accounts"></a>További tárfiókok hozzáadása

További, biztonságos átvitel használatára képes tárfiókok hozzáadására számos lehetőség áll rendelkezésre:

* Az előző szakaszban ismertetett Azure Resource Manager-sablon módosítása.
* Egy fürt létrehozása az [Azure Portal](https://portal.azure.com) használatával és a kapcsolódó tárfiók megadása.
* Szkriptműveleteket használata további, biztonságos átvitel használatára képes tárfiókok meglévő HDInsight-fürthöz történő hozzáadásához. További információkért lásd: [Add additional storage accounts to HDInsight](hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="next-steps"></a>További lépések

* Az Azure Storage (WASB) használata az [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) helyett alapértelmezett adattárként
* További információt az Azure Storage HDInsight általi használatáról [az Azure Storage és a HDInsight együttes használatát](hdinsight-hadoop-use-blob-storage.md) ismertető cikkben talál.
* További információ a HDInsightba való adatfeltöltésről: [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md).
