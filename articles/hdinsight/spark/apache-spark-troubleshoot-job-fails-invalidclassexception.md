---
title: InvalidClassException hiba a Apache Spark-Azure HDInsight
description: Apache Spark a feladatok meghiúsulnak a InvalidClassException, az osztály verziószáma nem egyezik az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: be50f8716835b0842f854842e5340b0bb8594136
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894372"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark a feladatok meghiúsulnak a InvalidClassException, az osztály verziószáma nem egyezik az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Megpróbál létrehozni egy Apache Spark feladatot egy Spark 2. x fürtben. A művelet a következőhöz hasonló hibával meghiúsul:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Ok

Ezt a hibát okozhatja, ha hozzáad egy további jar-t a `spark.yarn.jars` konfigurációhoz, amely kifejezetten egy olyan árnyékolt jar, amely a csomag egy másik verzióját tartalmazza, `commons-lang3` és egy osztály-eltérést mutat be. Alapértelmezés szerint a Spark 2.1/2/3 verziója a 3,5-es verzióját használja `commons-lang3` .

> [!TIP]
> Egy könyvtár árnyékolásához a saját tartalmait a saját jar-ba helyezheti, és megváltoztathatja a csomagját. Ez különbözik a könyvtár csomagolásával, amely a könyvtárat a saját Jarba helyezi újra csomagolás nélkül.

## <a name="resolution"></a>Megoldás:

Távolítsa el a jar-t, vagy fordítsa újra a testreszabott jar-t (AzureLogAppender), és használja a [Maven-Shade-beépülő modult](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) az osztályok áthelyezéséhez.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
