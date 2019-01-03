---
title: Hibakeresés és elemezheti az Apache Hadoop-szolgáltatások használatával halomürítések – Azure
description: Automatikusan összegyűjtheti halomürítések Apache Hadoop-szolgáltatásokhoz, és helyezze el az Azure Blob storage-fiók a hibakereséshez és elemzéshez belül.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 698806bdedd9994f2c9de53118cb42c9df1c36cd
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724402"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-apache-hadoop-services"></a>Halommemória-képek összegyűjtése a Blob storage a hibakereséshez és az Apache Hadoop-szolgáltatásokhoz elemzése
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Halomürítések az alkalmazás memória, beleértve a változók értékeit, a memóriakép létrehozásakor pillanatképet tartalmaz. Ezért hasznosak lehetnek, amelyet a futásidejű kapcsolatos problémák diagnosztizálásához. Halomürítések automatikusan gyűjti a [Apache Hadoop](https://hadoop.apache.org/) -szolgáltatásokat és az Azure Blob storage-fiók alatt HDInsightHeapDumps felhasználó belül elhelyezett /.

Halomürítések a különböző szolgáltatások gyűjteménye engedélyezni kell a szolgáltatások az egyes fürtökön. Ez a funkció alapértelmezés szerint a fürt számára ki lehet. Ezek halomürítések nagy, lehet, így célszerű a Blob storage-fiók figyelése, azok változtatásainak mentése után a gyűjtemény engedélyezve van.

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). Ebben a cikkben található információk csak a Windows-alapú HDInsight vonatkozik. A Linux-alapú HDInsight további információkért lásd: [engedélyezése halommemória-képek a Linux-alapú HDInsight az Apache Hadoop-szolgáltatásokhoz](hdinsight-hadoop-collect-debug-heap-dump-linux.md).


## <a name="eligible-services-for-heap-dumps"></a>Halomürítések a jogosult szolgáltatások
A következő szolgáltatásokat halomürítések engedélyezheti:

* **Az Apache hcatalog** -tempelton
* **Az Apache hive** -hiveserver2-n, metaadattár, derbyserver
* **a mapreduce** -jobhistoryserver
* **Az Apache yarn** -resourcemanager, nodemanager, timelineserver
* **Az Apache hdfs** -datanode, secondarynamenode, namenode

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
