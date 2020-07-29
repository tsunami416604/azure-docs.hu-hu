---
title: Apache Hive a naplófájlok kitöltésére szolgáló naplók – Azure HDInsight
description: A Apache Hive naplók kitöltik a lemezterületet az Azure HDInsight lévő fő csomópontokon.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78943966"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Forgatókönyv: Apache Hive naplók kitöltik a lemezterületet az Azure HDInsight lévő fő csomópontokon

Ez a cikk az Azure HDInsight-fürtökön található főcsomópontokon nem elegendő lemezterülettel kapcsolatos problémák megoldását és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy Apache Hive-vagy LLAP-fürtön a nem kívánt naplók a teljes lemezterületet veszik fel a fő csomópontokon. Ennek következtében a következő problémák észlelhetők.

1. Az SSH-hozzáférés meghiúsul, mert nincs szóköz a főcsomóponton.
2. A Ambari *http-hibát ad: a 503 szolgáltatás nem érhető el*.

A naplók a probléma előfordulásakor az `ambari-agent` alábbiakat mutatják be.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Ok

A speciális kaptár-log4j konfigurációk esetében a *log4j. Append. RFA. MaxBackupIndex* paraméter nincs megadva. A naplófájlok végtelen generációját okozza.

## <a name="resolution"></a>Megoldás:

1. A Ambari-portálon navigáljon a kaptár összetevő összefoglalásához, és kattintson a `Configs` Tab gombra.

2. Ugrás a `Advanced hive-log4j` Speciális beállítások területen található szakaszra.

3. Állítsa `log4j.appender.RFA` a paramétert RollingFileAppender értékre. 

4. Állítsa be `log4j.appender.RFA.MaxFileSize` és `log4j.appender.RFA.MaxBackupIndex` az alábbiak szerint.

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
5. Állítsa `hive.root.logger` a `INFO,RFA` következőre: Az alapértelmezett beállítás a hibakeresés, ami nagyon nagy méretűvé teszi a naplókat.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Mentse a konfigurációkat, és indítsa újra a szükséges összetevőket.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
