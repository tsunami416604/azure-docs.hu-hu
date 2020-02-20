---
title: WASB hibakeresése az Azure HDInsight
description: Leírja az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470717"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>WASB hibakeresése az Azure HDInsight

Előfordulhat, hogy meg szeretné ismerni, hogy milyen műveleteket végez a WASB-illesztőprogram az Azure Storage-ban. Az ügyféloldali WASB-illesztőprogram **hibakeresési** szinten létrehozza a naplófájlokat az egyes fájlrendszer-műveletekhez. A WASB-illesztőprogram a log4j használatával vezérli a naplózási szintet, és az alapértelmezett érték az **információs** szint. Az Azure Storage kiszolgálóoldali elemzési naplóival kapcsolatban lásd: az [Azure Storage Analytics naplózása](../../storage/common/storage-analytics-logging.md).

A létrehozott napló a következőhöz hasonlóan fog kinézni:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>A WASB hibakeresési naplójának bekapcsolása

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`, ahol a `CLUSTERNAME` a Spark-fürt neve.

1. Navigáljon a **speciális spark2-log4j-Properties**elemre.

    1. `log4j.appender.console.Threshold=INFO` módosítása `log4j.appender.console.Threshold=DEBUG`re.

    1. `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`hozzáadása.

1. Navigáljon a **speciális livy2-log4j-Properties**elemre.

    `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`hozzáadása.

1. Mentse a módosításokat.

## <a name="additional-logging"></a>További naplózás

A fenti naplóknak magas szintű ismeretekkel kell rendelkezniük a fájlrendszer műveleteiről. Ha a fenti naplók még nem biztosítanak hasznos információkat, vagy ha a blob Storage API-hívásokat szeretné kivizsgálni, vegyen fel `fs.azure.storage.client.logging=true` a `core-site`ba. Ezzel a beállítással engedélyezheti a wasb Java SDK-naplóit, és minden hívást a blob Storage-kiszolgálóra kell nyomtatnia. Távolítsa el a beállítást a vizsgálatok után, mert a lemez gyorsan kitölthető, és lelassíthatja a folyamatot.

Ha a háttér Azure Data Lake alapul, használja a következő log4j-beállítást az összetevőhöz (például Spark/TEZ/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Keresse meg `/var/log/adl/adl.log` naplókat a naplókhoz.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
