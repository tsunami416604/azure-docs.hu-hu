---
title: Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight
description: Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 7091e638743fb8cd1488fe7e332378bf89304af1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087069"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Apache ZooKeeper kiszolgáló állapota nem megfelelő, a tünetek lehetnek például a következők: mindkét Resource Manager/Name csomópont készenléti módban van, az egyszerű HDFS műveletek nem `zkFailoverController` működnek, leáll, és nem indítható el, a fonal/Spark/Livy feladatok ZooKeeper hibák miatt meghiúsulnak. A következőhöz hasonló hibaüzenet jelenhet meg:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

## <a name="cause"></a>Ok

Ha a pillanatkép-fájlok mennyisége nagy, vagy a pillanatképek sérültek, a ZooKeeper-kiszolgáló nem fogja megalkotni a kvórumot, ami miatt a ZooKeeper kapcsolódó szolgáltatások állapota nem megfelelő. A ZooKeeper-kiszolgáló nem távolítja el a régi pillanatkép-fájlokat az adatkönyvtárból, hanem rendszeres feladatot végez a felhasználók számára a ZooKeeper egészséges állapotának fenntartásához. További információ: [ZooKeeper erősségek és korlátozások](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Megoldás:

Ellenőrizze a ZooKeeper- `/hadoop/zookeeper/version-2` adatkönyvtárat, és annak megállapításához, hogy a pillanatképek fájlmérete nagyméretű- `/hadoop/hdinsight-zookeepe/version-2` e. Ha nagyméretű Pillanatképek léteznek, hajtsa végre a következő lépéseket:

1. Pillanatképek biztonsági mentése `/hadoop/zookeeper/version-2` a `/hadoop/hdinsight-zookeepe/version-2`és a alkalmazásban.

1. Pillanatképek törlése a `/hadoop/zookeeper/version-2` és `/hadoop/hdinsight-zookeepe/version-2`a alkalmazásban.

1. Indítsa újra az összes ZooKeeper-kiszolgálót az Apache Ambari felhasználói felületéről.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
