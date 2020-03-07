---
title: A régióbeli kiszolgálókkal kapcsolatos problémák az Azure HDInsight
description: A régióbeli kiszolgálókkal kapcsolatos problémák az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395156"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>A régióbeli kiszolgálókkal kapcsolatos problémák az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-unassigned-regions"></a>Forgatókönyv: nem hozzárendelt régiók

### <a name="issue"></a>Probléma

`hbase hbck` parancs futtatásakor a következőhöz hasonló hibaüzenet jelenik meg:

```
multiple regions being unassigned or holes in the chain of regions
```

Az Apache HBase Master felhasználói felületén megtekintheti az összes régió kiszolgálójának kiegyensúlyozatlan régiói számát. Ezután futtathatja `hbase hbck` parancsot, hogy láthassa a lyukakat a régió láncában.

### <a name="cause"></a>Ok

A lyukak az offline régiók eredményei lehetnek.

### <a name="resolution"></a>Megoldás:

Javítsa ki a hozzárendeléseket. Az alábbi lépések végrehajtásával visszahelyezheti a hozzá nem rendelt régiókat a normál állapotba:

1. Jelentkezzen be a HDInsight HBase-fürtbe SSH használatával.

1. Futtassa `hbase zkcli` parancsot a ZooKeeper-rendszerhéjhoz való kapcsolódáshoz.

1. Futtassa `rmr /hbase/regions-in-transition` vagy `rmr /hbase-unsecure/regions-in-transition` parancsot.

1. Lépjen ki a Zookeeper-rendszerhéjból `exit` parancs használatával.

1. Nyissa meg az Apache Ambari felhasználói felületét, majd indítsa újra az aktív HBase Master szolgáltatást.

1. Futtassa újra `hbase hbck` parancsot (további beállítások nélkül). Ellenőrizze a kimenetet, és győződjön meg arról, hogy az összes régió hozzá van rendelve.

---

## <a name="scenario-dead-region-servers"></a>Forgatókönyv: Holt régió kiszolgálók

### <a name="issue"></a>Probléma

A régió-kiszolgálók nem indulnak el.

### <a name="cause"></a>Ok

Több felosztó WAL-könyvtár.

1. Aktuális WALs listájának beolvasása: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Vizsgálja meg a `wals.out` fájlt. Ha túl sok felosztó könyvtár van (a *-felosztástól kezdve), akkor a régió-kiszolgáló valószínűleg sikertelen lesz a könyvtárak miatt.

### <a name="resolution"></a>Megoldás:

1. A HBase leállítása a Ambari-portálról.

1. `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` végrehajtása a WALs friss listájának lekéréséhez.

1. Helyezze át a *-felosztási könyvtárakat egy ideiglenes mappába, `splitWAL`, és törölje a *-felosztási könyvtárakat.

1. `hbase zkcli` parancs végrehajtása a Zookeeper-rendszerhéjhoz való kapcsolódáshoz.

1. `rmr /hbase-unsecure/splitWAL`végrehajtása.

1. Indítsa újra a HBase szolgáltatást.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
