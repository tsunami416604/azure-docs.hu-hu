---
title: Apache Ambari-használat az Azure HDInsight
description: Megtudhatja, hogyan használja az Apache Ambari az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: cfb83c4ec9972fda3813d414583bc73edeef3229
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285967"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Apache Ambari-használat az Azure HDInsight

A HDInsight az Apache Ambari-t használja a fürtök üzembe helyezéséhez és felügyeletéhez. A Ambari-ügynökök minden csomóponton futnak (átjárócsomóponthoz, Worker node, Zookeeper és élcsomópontok, ha létezik). A Ambari-kiszolgáló csak a átjárócsomóponthoz fut (hn0 vagy hn1). Egyszerre csak egy példányban fut a Ambari-kiszolgáló. Ezt a HDInsight feladatátvételi vezérlő vezérli. Ha az egyik átjárócsomópontokkal újraindításra vagy karbantartásra van lebontva, a másik átjárócsomóponthoz lesz aktív, és a második átjárócsomóponthoz a Ambari-kiszolgáló lesz elindítva.

Az összes fürtkonfiguráció a [Ambari felhasználói felületén](./hdinsight-hadoop-manage-ambari.md)keresztül történik, a csomópont újraindításakor a rendszer felülírja a helyi módosításokat.

## <a name="failover-controller-services"></a>Feladatátvételi vezérlő szolgáltatásai

A HDInsight feladatátvételi vezérlő a átjárócsomóponthoz gazdagép IP-címének frissítéséhez is felelős, amely az aktuális aktív fő csomópontra mutat. Az összes Ambari-ügynök úgy van konfigurálva, hogy az állapotát és szívverését a átjárócsomóponthoz gazdagépnek jelentse. A feladatátvételi vezérlő a fürt minden csomópontján futó szolgáltatások készlete, ha nem futnak, előfordulhat, hogy a átjárócsomóponthoz feladatátvétel nem működik megfelelően, és a HTTP 502-es verzióval próbálkozik a Ambari-kiszolgáló elérésére.

A átjárócsomóponthoz aktív állapotának vizsgálatához az egyik módszer az SSH használata a fürt egyik csomópontján, majd az `ping headnodehost` IP-cím és a két átjárócsomópontokkal összevetése.

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

A HDInsight létrehoz egy adatbázist SQL Database a motorháztető alatt, hogy az adatbázisként szolgáljon a Ambari-kiszolgálóhoz. Az alapértelmezett [szolgáltatási szintet a S0](../azure-sql/database/elastic-pool-scale.md).

Ha a fürt létrehozásakor a munkavégző csomópontok száma nagyobb, mint 16, az S2 az adatbázis-szolgáltatási réteg.

## <a name="takeaway-points"></a>Elvihető pontok

Soha ne indítsa el vagy állítsa le a ambari-kiszolgáló vagy a ambari-ügynök szolgáltatásait, hacsak nem próbálja újraindítani a szolgáltatást a probléma megkerülése érdekében. A feladatátvétel kényszerítéséhez újra lehet indítani az aktív átjárócsomóponthoz.

Soha ne módosítsa manuálisan a konfigurációs fájlokat bármelyik fürtcsomóponton, hogy a Ambari felhasználói felülete elvégezze a feladatot.

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes felületével](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-fürtök kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md)

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]