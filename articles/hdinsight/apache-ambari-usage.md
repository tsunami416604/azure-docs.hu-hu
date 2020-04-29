---
title: Apache Ambari-használat az Azure HDInsight
description: Megtudhatja, hogyan használja az Apache Ambari az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77067395"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Apache Ambari-használat az Azure HDInsight

A HDInsight az Apache Ambari-t használja a fürtök üzembe helyezéséhez és felügyeletéhez. A Ambari-ügynökök minden csomóponton futnak (átjárócsomóponthoz, Worker node, Zookeeper és élcsomópontok, ha létezik). A Ambari-kiszolgáló csak a átjárócsomóponthoz fut (hn0 vagy hn1). Egyszerre csak egy példányban fut a Ambari-kiszolgáló. Ezt a HDInsight feladatátvételi vezérlő vezérli. Ha az egyik átjárócsomópontokkal újraindításra vagy karbantartásra van lebontva, a másik átjárócsomóponthoz lesz aktív, és a második átjárócsomóponthoz a Ambari-kiszolgáló lesz elindítva.

Az összes fürtkonfiguráció a [Ambari felhasználói felületén](./hdinsight-hadoop-manage-ambari.md)keresztül történik, a csomópont újraindításakor a rendszer felülírja a helyi módosításokat.

## <a name="failover-controller-services"></a>Feladatátvételi vezérlő szolgáltatásai

A HDInsight feladatátvételi vezérlő a átjárócsomóponthoz gazdagép IP-címének frissítéséhez is felelős, amely az aktuális aktív fő csomópontra mutat. Az összes Ambari-ügynök úgy van konfigurálva, hogy az állapotát és szívverését a átjárócsomóponthoz gazdagépnek jelentse. A feladatátvételi vezérlő a fürt minden csomópontján futó szolgáltatások készlete, ha nem futnak, előfordulhat, hogy a átjárócsomóponthoz feladatátvétel nem működik megfelelően, és a HTTP 502-es verzióval próbálkozik a Ambari-kiszolgáló elérésére.

A átjárócsomóponthoz aktív állapotának vizsgálatához az egyik módszer az SSH használata a fürt egyik csomópontján, majd az IP- `ping headnodehost` cím és a két átjárócsomópontokkal összevetése.

Ha a feladatátvételi vezérlő szolgáltatásai nem futnak, előfordulhat, hogy a átjárócsomóponthoz feladatátvétel nem fog megfelelően működni, ami végül nem fut a Ambari-kiszolgálóval. A feladatátvételi vezérlő szolgáltatásainak futtatásához futtassa a következőt:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Naplók

Az aktív átjárócsomóponthoz a Ambari-kiszolgáló naplóit a következő helyen tekintheti meg:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

A fürt bármely csomópontján megtekintheti a Ambari-ügynök naplóit a következő helyen:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Szolgáltatás indítási lépései

A szolgáltatás indítási ideje a rendszerindításkor:

1. A Hdinsight-Agent elindítja a feladatátvételi vezérlő szolgáltatásait.
1. A feladatátvételi vezérlő szolgáltatás minden csomóponton és Ambari-kiszolgálón elindítja a Ambari-ügynököt az aktív átjárócsomóponthoz.

## <a name="ambari-database"></a>Ambari-adatbázis

A HDInsight SQL Azure adatbázist hoz létre a motorháztető alatt, hogy a Ambari-kiszolgáló adatbázisának szolgáljon. Az alapértelmezett [szolgáltatási szintet a S0](../sql-database/sql-database-elastic-pool-scale.md).

Ha a fürt létrehozásakor a munkavégző csomópontok száma nagyobb, mint 16, az S2 az adatbázis-szolgáltatási réteg.

## <a name="takeaway-points"></a>Elvihető pontok

Soha ne indítsa el vagy állítsa le a ambari-kiszolgáló vagy a ambari-ügynök szolgáltatásait, hacsak nem próbálja újraindítani a szolgáltatást a probléma megkerülése érdekében. A feladatátvétel kényszerítéséhez újra lehet indítani az aktív átjárócsomóponthoz.

Soha ne módosítsa manuálisan a konfigurációs fájlokat bármelyik fürtcsomóponton, hogy a Ambari felhasználói felülete elvégezze a feladatot.

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes felületével](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-fürtök kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md)

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
