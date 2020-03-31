---
title: Problémák az Azure HDInsight régiókiszolgálóival
description: Problémák az Azure HDInsight régiókiszolgálóival
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272759"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problémák az Azure HDInsight régiókiszolgálóival

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-unassigned-regions"></a>Eset: Nem hozzárendelt régiók

### <a name="issue"></a>Probléma

A `hbase hbck` parancs futtatásakor a következőhöz hasonló hibaüzenet jelenik meg:

```
multiple regions being unassigned or holes in the chain of regions
```

Az Apache HBase főfelhasználói felületén láthatja, hogy hány régió van kiegyensúlyozatlan az összes régiókiszolgálón. Ezután futtathatja `hbase hbck` a parancsot a régiólánc lyukaimegtekintéséhez.

### <a name="cause"></a>Ok

A lyukak offline régiók eredménye lehetnek.

### <a name="resolution"></a>Megoldás:

Javítsd meg a feladatokat. Az alábbi lépéseket követve hozza vissza a nem hozzárendelt régiókat normál állapotba:

1. Jelentkezzen be a HDInsight HBase fürtbe az SSH használatával.

1. Futtassa `hbase zkcli` a parancsot a ZooKeeper rendszerhéjhoz való csatlakozáshoz.

1. Futtatás `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` vagy parancs.

1. Kilépés az állattartó `exit` héjából a parancs használatával.

1. Nyissa meg az Apache Ambari felhasználói felületét, majd indítsa újra az Active HBase Master szolgáltatást.

1. Futtassa `hbase hbck` újra a parancsot (további lehetőségek nélkül). Ellenőrizze a kimenetet, és győződjön meg arról, hogy minden régió van hozzárendelve.

---

## <a name="scenario-dead-region-servers"></a>Forgatókönyv: Halott régiókiszolgálók

### <a name="issue"></a>Probléma

A régiókiszolgálók nem indulnak el.

### <a name="cause"></a>Ok

Többszörös felosztása WAL könyvtárakat.

1. Az aktuális WAL-ok `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`listájának beírása: .

1. Vizsgálja `wals.out` meg az aktát. Ha túl sok felosztási könyvtárak (kezdve a *-felosztás), a régió kiszolgáló valószínűleg nem, mert ezek a könyvtárak.

### <a name="resolution"></a>Megoldás:

1. Állítsa le a HBase-t az Ambari portálról.

1. Végrehajtás `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` a WAL-ok friss listájának beírásához.

1. Helyezze át a *-felosztási könyvtárakat egy ideiglenes mappába, `splitWAL`majd törölje a *-felosztási könyvtárakat.

1. Végrehajtási `hbase zkcli` parancs csatlakozni zookeeper shell.

1. Végrehajtás `rmr /hbase-unsecure/splitWAL`.

1. Indítsa újra a HBase szolgáltatást.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
