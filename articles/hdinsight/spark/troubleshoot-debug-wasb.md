---
title: WASB-fájlhibák hibakeresése az Azure HDInsightban
description: Az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470717"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>WASB-fájlhibák hibakeresése az Azure HDInsightban

Vannak esetek, amikor érdemes lehet megérteni, hogy milyen műveleteket a WASB-illesztőprogram indult az Azure Storage.There times when you may want to understand what operations the WASB driver started with Azure Storage. Az ügyféloldalon a WASB illesztőprogram naplókat hoz létre a **DEBUG** szintjén végzett minden egyes fájlrendszer-művelethez. A WASB illesztőprogram log4j-t használ a naplózási szint szabályozására, az alapértelmezett pedig az **INFO** szint. Az Azure Storage kiszolgálóoldali elemzési naplóiról az [Azure Storage-elemzésnaplózás című témakörben](../../storage/common/storage-analytics-logging.md)található.

Az előállított napló a következőhöz hasonlóan fog kinézni:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>A WASB hibakeresési naplójának bekapcsolása fájlműveletekhez

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`meg `CLUSTERNAME` a , ahol a Spark-fürt neve.

1. Keresse meg a **speciális spark2-log4j-tulajdonságokat.**

    1. Módosítás `log4j.appender.console.Threshold=INFO` `log4j.appender.console.Threshold=DEBUG`a következőre.

    1. Adja `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`hozzá a bővítményt.

1. Keresse meg **az Advanced livy2-log4j-properties (Speciális livy2-log4j- tulajdonságok ) tulajdonságait.**

    Adja `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`hozzá a bővítményt.

1. A módosítások mentése.

## <a name="additional-logging"></a>További naplózási lehetőségek

A fenti naplóknak magas szintű ismereteket kell nyújtaniuk a fájlrendszer működéséről. Ha a fenti naplók továbbra sem nyújtanak hasznos információkat, vagy ha meg `fs.azure.storage.client.logging=true` szeretné `core-site`vizsgálni a blob storage API-hívásokat, adja hozzá a hoz. Ez a beállítás lehetővé teszi a java SDK naplók wasb tároló illesztőprogram, és kinyomtatja az egyes hívás blob storage server. A vizsgálat után távolítsa el a beállítást, mert az gyorsan feltöltheti a lemezt, és lelassíthatja a folyamatot.

Ha a háttérrendszer Azure Data Lake alapú, majd használja a következő log4j beállítást az összetevőhöz (például spark/tez/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Keresse meg a `/var/log/adl/adl.log` naplókat a naplókat.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
