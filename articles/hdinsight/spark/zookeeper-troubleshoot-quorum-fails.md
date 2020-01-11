---
title: Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight
description: Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: a0874826529b5c9ca5d6d4107fe820cd522d81d0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894036"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Apache ZooKeeper kiszolgáló állapota nem megfelelő, a tünetek lehetnek például a következők: mindkét Resource Manager/Name csomópont készenléti módban van, az egyszerű HDFS műveletek nem működnek, `zkFailoverController` leáll, és nem indítható el, a fonal/Spark/Livy feladatok Zookeeper hibák miatt meghiúsulnak. A következőhöz hasonló hibaüzenet jelenhet meg:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

## <a name="cause"></a>Ok

Ha a pillanatkép-fájlok mennyisége nagy, vagy a pillanatképek sérültek, a ZooKeeper-kiszolgáló nem fogja megalkotni a kvórumot, ami miatt a ZooKeeper kapcsolódó szolgáltatások állapota nem megfelelő. A ZooKeeper-kiszolgáló nem távolítja el a régi pillanatkép-fájlokat az adatkönyvtárból, hanem rendszeres feladatot végez a felhasználók számára a ZooKeeper egészséges állapotának fenntartásához. További információ: [ZooKeeper erősségek és korlátozások](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Felbontás

Ellenőrizze, hogy a ZooKeeper adatkönyvtára `/hadoop/zookeeper/version-2` és `/hadoop/hdinsight-zookeepe/version-2` annak megállapítására, hogy a pillanatképek fájlmérete nagyméretű-e. Ha nagyméretű Pillanatképek léteznek, hajtsa végre a következő lépéseket:

1. A pillanatképek biztonsági mentése `/hadoop/zookeeper/version-2` és `/hadoop/hdinsight-zookeepe/version-2`.

1. A pillanatképek tisztítása `/hadoop/zookeeper/version-2` és `/hadoop/hdinsight-zookeepe/version-2`.

1. Indítsa újra az összes ZooKeeper-kiszolgálót az Apache Ambari felhasználói felületéről.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
