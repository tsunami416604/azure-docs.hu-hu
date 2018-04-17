---
title: Hibakeresés és elemzése a Hadoop-szolgáltatás a halommemória memóriaképek - Azure |} Microsoft Docs
description: Automatikusan Hadoop szolgáltatások halommemória memóriaképek összegyűjtése és az Azure Blob storage-fiók Hibakeresés és elemzési belül helyezhető el.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 0721d20987008e5cc6370c6e853440ce93edcfa9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>A gyűjtés halommemória kiírja a Blob Storage tárolóban hibakeresését és elemzése a Hadoop-szolgáltatás
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Halommemória memóriaképek tartalmazza az alkalmazás memória, beleértve a változók értékeit a biztonsági másolat létrehozásakor pillanatképet. Ezért futás közben felmerülő problémák diagnosztizálásához. Halommemória memóriaképek automatikusan Hadoop-szolgáltatás által gyűjtött és az Azure Blob storage-fiók alatt HDInsightHeapDumps felhasználói belül elhelyezett /.

Szolgáltatások egyes fürtökön engedélyezni kell a különböző szolgáltatások halommemória memóriaképek gyűjteménye. Ez a funkció alapértelmezés szerint a fürt ki lehet. Ezek halommemória memóriaképek nagy, lehet, így célszerű figyelni a Blob storage-fiók ahol azokat a rendszer mentette a gyűjtemény engedélyezése után.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). A cikkben szereplő információk csak a Windows-alapú HDInsight vonatkozik. Információ a Linux-alapú HDInsight: [engedélyezése halommemória a Linux-alapú HDInsight Hadoop-szolgáltatások listázása](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>A halommemória memóriaképek jogosult szolgáltatások
A következő szolgáltatások halommemória memóriaképek engedélyezéséhez:

* **hcatalog** -tempelton
* **Hive** -hiveserver2-n, metaadattárhoz, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resourcemanager, nodemanager, timelineserver
* **hdfs** -datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Konfigurációs elemek, amelyek lehetővé teszik a halommemória memóriaképek
Kapcsolja be a halommemória memóriaképek egy szolgáltatás, állítsa be a megfelelő konfigurációs elemek, hogy a szolgáltatás, amely megadja a szakasz kell **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Értékének **service_name** is lehet a szolgáltatások az itt felsorolt: tempelton, hiveserver2-n metaadattárhoz, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, vagy a namenode.

## <a name="enable-using-azure-powershell"></a>Engedélyezze az Azure PowerShell használatával
Például bekapcsolása halommemória memóriaképek jobhistoryserver az Azure PowerShell használatával, használhatja a következő parancsfájlt:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Engedélyezze a .NET SDK használatával
Például bekapcsolásához halommemória memóriaképek jobhistoryserver az Azure HDInsight .NET SDK használatával a következő kódot használhatja:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
