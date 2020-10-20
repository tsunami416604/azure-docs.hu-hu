---
title: Apache Hive a naplófájlok kitöltésére szolgáló naplók – Azure HDInsight
description: A Apache Hive naplók kitöltik a lemezterületet az Azure HDInsight lévő fő csomópontokon.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: a102c9f375b37579cf6f92b08d67f762d3dfd26a
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220890"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Forgatókönyv: Apache Hive naplók kitöltik a lemezterületet az Azure HDInsight lévő fő csomópontokon

Ez a cikk az Azure HDInsight-fürtökön található főcsomópontokon nem elegendő lemezterülettel kapcsolatos problémák megoldását és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy Apache Hive-vagy LLAP-fürtön a nem kívánt naplók a teljes lemezterületet veszik fel a fő csomópontokon. Ennek következtében a következő problémák észlelhetők.

1. Az SSH-hozzáférés meghiúsul, mert nincs szóköz a főcsomóponton.
2. A Ambari *http-hibát ad: a 503 szolgáltatás nem érhető el*.
3. A HiveServer2 Interactive nem tud újraindulni.

A naplók a probléma előfordulásakor az `ambari-agent` alábbiakat mutatják be.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Ok

A speciális kaptár-log4j konfigurációk esetében az aktuális alapértelmezett törlési ütemterv az utolsó módosítás dátuma alapján 30 napnál régebbi fájlokra van beállítva.

## <a name="resolution"></a>Feloldás

1. A Ambari-portálon navigáljon a kaptár összetevő összefoglalásához, és kattintson a `Configs` Tab gombra.

2. Ugrás a `Advanced hive-log4j` Speciális beállítások területen található szakaszra.

3. Állítsa a `appender.RFA.strategy.action.condition.age` paramétert egy tetszőleges korra. Példa 14 napra: `appender.RFA.strategy.action.condition.age = 14D`

4. Ha nem látja a kapcsolódó beállításokat, adja hozzá a következő beállításokat.
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
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

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
