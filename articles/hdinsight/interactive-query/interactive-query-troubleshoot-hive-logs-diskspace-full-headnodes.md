---
title: Apache Hive-naplók, amelyek feltöltik a lemezterületet - Azure HDInsight
description: Az Apache Hive-naplók az Azure HDInsight főcsomópontjainak lemezterületét töltik meg.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943966"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Eset: Az Apache Hive-naplók feltöltik a lemezterületet az Azure HDInsight head csomópontjain

Ez a cikk az Azure HDInsight-fürtök fő csomópontjain található lemezterülettel kapcsolatos problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy Apache Hive/LLAP-fürtön a nem kívánt naplók a teljes lemezterületet a fő csomópontokon foglalják el. Ami miatt a következő kérdések et lehetett látni.

1. Az SSH-hozzáférés sikertelen, mert a fejcsomóponton nincs hely.
2. Ambari ad *HTTP ERROR: 503 szolgáltatás nem érhető el*.

A `ambari-agent` naplók a következőt mutatják, amikor a probléma megtörténik.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Ok

A speciális Hive-log4j konfigurációkban a *log4j.appender.RFA.MaxBackupIndex* paraméter kimarad. Ez okozza a végtelen generációs naplófájlokat.

## <a name="resolution"></a>Megoldás:

1. Keresse meg a Hive-összetevő összegzését az `Configs` Ambari portálon, és kattintson a fülre.

2. Nyissa meg `Advanced hive-log4j` a Speciális beállítások szakaszt.

3. Paraméter `log4j.appender.RFA` beállítása RollingFileAppender paraméterként. 

4. Állítsa `log4j.appender.RFA.MaxFileSize` `log4j.appender.RFA.MaxBackupIndex` be, és az alábbiak szerint.

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. Állítsa `hive.root.logger` `INFO,RFA` a következőképpen. Az alapértelmezett beállítás a DEBUG, ami a naplókat nagyon nagyná teszi.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Mentse a konfigurációkat, és indítsa újra a szükséges összetevőket.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
