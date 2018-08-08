---
title: Hibakeresés és elemzése a Hadoop-szolgáltatásokhoz az halomürítések – Azure
description: Automatikus gyűjtése halomürítések a Hadoop-szolgáltatásokhoz, és helyezze el az Azure Blob storage-fiók a hibakereséshez és elemzéshez belül.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 484cdcb45a835a0a3b76e2c3aeca7500af7c9b7b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597004"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Halommemória-képek összegyűjtése a Blob storage-hibakeresést és elemzése a Hadoop-szolgáltatásokhoz
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Halomürítések az alkalmazás memória, beleértve a változók értékeit, a memóriakép létrehozásakor pillanatképet tartalmaz. Ezért hasznosak lehetnek, amelyet a futásidejű kapcsolatos problémák diagnosztizálásához. Halomürítések automatikusan gyűjti a Hadoop-szolgáltatásokhoz és az Azure Blob storage-fiók alatt HDInsightHeapDumps felhasználó belül elhelyezett /.

Halomürítések a különböző szolgáltatások gyűjteménye engedélyezni kell a szolgáltatások az egyes fürtökön. Ez a funkció alapértelmezés szerint a fürt számára ki lehet. Ezek halomürítések nagy, lehet, így célszerű a Blob storage-fiók figyelése, azok változtatásainak mentése után a gyűjtemény engedélyezve van.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). Ebben a cikkben található információk csak a Windows-alapú HDInsight vonatkozik. A Linux-alapú HDInsight további információkért lásd: [engedélyezése halommemória-képek a Linux-alapú HDInsight a Hadoop-szolgáltatásokhoz](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>Halomürítések a jogosult szolgáltatások
A következő szolgáltatásokat halomürítések engedélyezheti:

* **hcatalog** -tempelton
* **Hive** -hiveserver2-n, metaadattár, derbyserver
* **a mapreduce** -jobhistoryserver
* **yarn** -resourcemanager, nodemanager, timelineserver
* **hdfs** -datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Halomürítések engedélyezése konfigurációs elemek
Egy szolgáltatás halomürítések bekapcsolásához állítsa be a megfelelő konfigurációs elemek a szakasz az adott szolgáltatáshoz, amely által meghatározott kell **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Értékét **service_name** szolgáltatások sorolható fel itt: tempelton, hiveserver2-n keresztül, metaadattár, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, vagy namenode.

## <a name="enable-using-azure-powershell"></a>Engedélyezze az Azure PowerShell-lel
Például bekapcsolása halomürítések jobhistoryserver az Azure PowerShell használatával, használhatja az alábbi parancsfájlt:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Engedélyezze a .NET SDK használatával
Ha például bekapcsolása halomürítések jobhistoryserver az Azure HDInsight .NET SDK használatával, használhatja a következő kódot:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
