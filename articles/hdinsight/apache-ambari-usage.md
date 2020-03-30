---
title: Az Apache Ambari használata az Azure HDInsightban
description: Vita arról, hogyan használják az Apache Ambari-t az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067395"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Az Apache Ambari használata az Azure HDInsightban

A HDInsight az Apache Ambari-t használja a fürt telepítéséhez és kezeléséhez. Ambari ügynökök futnak minden csomóponton (headnode, feldolgozó csomópont, zookeeper, és edgenode, ha létezik). Az Ambari kiszolgáló csak a headnode (hn0 vagy hn1) rendszeren fut. Egyszerre csak egy Ambari kiszolgálópéldány fuszik. Ezt a HDInsight feladatátvételi vezérlője vezérli. Ha az egyik headnodes le újraindítás vagy karbantartás, a másik headnode aktívvá válik, és Ambari szerver a második headnode indul.

Az összes fürtkonfigurációt az [Ambari felhasználói felületén](./hdinsight-hadoop-manage-ambari.md)keresztül kell elvégezni, minden helyi módosítást felül kell írni a csomópont újraindításakor.

## <a name="failover-controller-services"></a>Feladatátvétel-vezérlő szolgáltatásai

A HDInsight feladatátvételi vezérlő is felelős a headnode állomás IP-címének frissítéséért, amely az aktuális aktív főcsomópontra mutat. Minden Ambari ügynökök vannak beállítva, hogy jelentse az állapotát és szívverését a headnode gazdagép. A feladatátvevő vezérlő a fürt minden csomópontján futó szolgáltatások készlete, ha nem futnak, előfordulhat, hogy a csomópont feladatátvételnem működik megfelelően, és az Ambari-kiszolgáló elérésekor http 502-es lesz a vége.

Annak ellenőrzéséhez, hogy melyik headnode aktív, az egyik módja az, hogy `ping headnodehost` ssh a fürt egyik csomópontjára, majd futtassa, és hasonlítsa össze az IP-t a két headnodes ip-címével.

Ha a feladatátvevő vezérlő szolgáltatások nem futnak, előfordulhat, hogy a headnode feladatátvétel nem történik meg megfelelően, ami végül nem fut Ambari-kiszolgáló. Annak ellenőrzéséhez, hogy futnak-e a feladatátvevő vezérlőszolgáltatások, hajtsa végre a következőket:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Naplók

Az aktív headnode, akkor ellenőrizze az Ambari szerver naplók:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

A fürt bármely csomópontján ellenőrizheti az Ambari ügynök naplókat a következő helyen:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Szolgáltatás indítási sorozatai

Ez a szolgáltatás indítási sorrendje a rendszerindításkor:

1. A Hdinsight-agent elindítja a feladatátvételi vezérlő szolgáltatásokat.
1. A feladatátvevő vezérlő szolgáltatások az Ambari-ügynököt minden csomóponton és Ambari-kiszolgálón aktív csomóponton indítják el.

## <a name="ambari-database"></a>Ambari adatbázis

A HDInsight az SQL Azure-adatbázist az Ambari-kiszolgáló adatbázisaként hozza létre. Az alapértelmezett [szolgáltatási szint az S0](../sql-database/sql-database-elastic-pool-scale.md).

Minden fürt munkavégző csomópont száma nagyobb, mint 16 a fürt létrehozásakor, S2 az adatbázis szolgáltatási szint.

## <a name="takeaway-points"></a>Elvihető pontok

Soha ne indítson el/állítson le manuálisan ambari-server vagy ambari-agent szolgáltatásokat, hacsak nem próbálja újraindítani a szolgáltatást a probléma megkerülése érdekében. Feladatátvétel kényszerítéséhez újraindíthatja az aktív csomópontot.

Soha ne módosítsa manuálisan a konfigurációs fájlokat egyetlen fürtcsomóponton sem, hagyja, hogy az Ambari felhasználói felülete végezze el a feladatot.

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes felületével](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-fürtök kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md)

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
