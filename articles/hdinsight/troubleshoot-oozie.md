---
title: Az Apache Oozie hibáinak megoldása az Azure HDInsight
description: Bizonyos Apache Oozie hibák elhárítása az Azure HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 18831832f82cdbc8cec69e368f006f7acd4836c1
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205260"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Az Apache Oozie hibáinak megoldása az Azure HDInsight

Az Apache Oozie felhasználói felületén megtekintheti a Oozie naplókat. A Oozie felhasználói felülete a munkafolyamat által elindított MapReduce-feladatok JobTracker-naplóira mutató hivatkozásokat is tartalmaz. A hibaelhárítási minta a következő lehet:

1. Tekintse meg a feladatot a Oozie webes felhasználói felületén.

2. Ha egy adott művelet hibával vagy hibával rendelkezik, válassza ki a műveletet, és ellenőrizze, hogy a **hibaüzenet** mezője tartalmaz-e további információt a hibáról.

3. Ha elérhető, a művelet URL-címével megtekintheti a művelethez tartozó további részleteket, például a JobTracker naplókat.

A következő konkrét hibák merülhetnek fel, és elháríthatja azokat.

## <a name="ja009-cant-initialize-cluster"></a>JA009: nem lehet inicializálni a fürtöt

### <a name="issue"></a>Probléma

A feladatok állapota **felfüggesztve**értékre változik. A feladathoz tartozó részletek `RunHiveScript` az állapotot **START_MANUALként**jelenítik meg. A művelet kiválasztásakor a következő hibaüzenet jelenik meg:

    JA009: Cannot initialize Cluster. Please check your configuration for map

### <a name="cause"></a>Ok

A **job. XML** fájlban használt Azure Blob Storage-címek nem tartalmazzák a Storage-tárolót vagy a Storage-fiók nevét. A blob Storage-címnek a formátumnak kell lennie `wasbs://containername@storageaccountname.blob.core.windows.net`.

### <a name="resolution"></a>Megoldás:

Módosítsa a feladatok által használt BLOB Storage-címeket.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: a Oozie nem jogosult megszemélyesíteni &lt;a felhasználót&gt;

### <a name="issue"></a>Probléma

A feladatok állapota **felfüggesztve**értékre változik. A feladathoz tartozó részletek `RunHiveScript` az állapotot **START_MANUALként**jelenítik meg. Ha a műveletet választja, a következő hibaüzenet jelenik meg:

    JA002: User: oozie is not allowed to impersonate <USER>

### <a name="cause"></a>Ok

Az aktuális engedélyezési beállítások nem teszik lehetővé, hogy a Oozie megszemélyesítse a megadott felhasználói fiókot.

### <a name="resolution"></a>Megoldás:

A Oozie megszemélyesítheti a **`users`** csoport felhasználóit. A használatával `groups USERNAME` megtekintheti azokat a csoportokat, amelyeknek a felhasználói fiók tagja. Ha a felhasználó nem tagja a **`users`** csoportnak, akkor a következő paranccsal adhatja hozzá a felhasználót a csoporthoz:

    sudo adduser USERNAME users

> [!NOTE]  
> Több percet is igénybe vehet, mielőtt a HDInsight észleli, hogy a felhasználó hozzá lett adva a csoporthoz.

---

## <a name="launcher-error-sqoop"></a>Indító hiba (Sqoop)

### <a name="issue"></a>Probléma

A feladatok állapotának **leölése**megtörtént. A feladathoz tartozó részletek `RunSqoopExport` az állapotot **hibaként**jelenítik meg. Ha a műveletet választja, a következő hibaüzenet jelenik meg:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

### <a name="cause"></a>Ok

A Sqoop nem tudja betölteni az adatbázis eléréséhez szükséges adatbázis-illesztőt.

### <a name="resolution"></a>Megoldás:

Ha a Sqoop-t egy Oozie-feladatokból használja, az adatbázis-illesztőprogramot a többi erőforrással együtt kell szerepeltetni, például a Workflow. xml fájlt, amelyet a rendszer a feladatokhoz használ. Továbbá hivatkozzon arra az archívumra, amely az adatbázis-illesztőprogramot `<sqoop>...</sqoop>` tartalmazza a Workflow. xml fájl szakaszában.

Például a [Hadoop Oozie-munkafolyamatok használata](hdinsight-use-oozie-linux-mac.md)a következő lépésekkel végezhető el:

1. Másolja a `mssql-jdbc-7.0.0.jre8.jar` fájlt a **/tutorials/useoozie** könyvtárba:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Módosítsa a `workflow.xml` t a következő XML-kód hozzáadásához a fenti `</sqoop>`új sorban:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
