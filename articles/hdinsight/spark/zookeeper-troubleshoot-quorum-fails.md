---
title: Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight
description: Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 41ac109e5c5379e6085dd57a3fcd8119915558fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133275"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Apache ZooKeeper kiszolgáló állapota nem megfelelő, a tünetek lehetnek például a következők: mindkét Resource Manager/Name csomópont készenléti módban van, az egyszerű HDFS műveletek nem `zkFailoverController` működnek, leáll, és nem indítható el, a fonal/Spark/Livy feladatok ZooKeeper hibák miatt meghiúsulnak. A LLAP démonok a biztonságos Spark-vagy interaktív struktúra-fürtökön is sikertelenek lehetnek. A következőhöz hasonló hibaüzenet jelenhet meg:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

A Zookeeper-kiszolgáló a/var/log/Zookeeper/Zookeeper-Zookeeper-Server-\*. out címen található bármely Zookeeper-gazdagépen naplózza a következő hibát:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Ok

Ha a pillanatkép-fájlok mennyisége nagy, vagy a pillanatképek sérültek, a ZooKeeper-kiszolgáló nem fogja megalkotni a kvórumot, ami miatt a ZooKeeper kapcsolódó szolgáltatások állapota nem megfelelő. A ZooKeeper-kiszolgáló nem távolítja el a régi pillanatkép-fájlokat az adatkönyvtárból, hanem rendszeres feladatot végez a felhasználók számára a ZooKeeper egészséges állapotának fenntartásához. További információ: [ZooKeeper erősségek és korlátozások](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Megoldás:

Ellenőrizze a ZooKeeper- `/hadoop/zookeeper/version-2` adatkönyvtárat, és annak megállapításához, hogy a pillanatképek fájlmérete nagyméretű- `/hadoop/hdinsight-zookeeper/version-2` e. Ha nagyméretű Pillanatképek léteznek, hajtsa végre a következő lépéseket:

1. Ellenőrizze az ugyanabban a kvórumban található más ZooKeeper-kiszolgálók állapotát, és győződjön meg arról, hogy megfelelően működnek-`echo stat | nc {zk_host_ip} 2181 (or 2182)`e a paranccsal.  

1. Jelentkezzen be a problémás ZooKeeper gazdagép, a biztonsági mentési `/hadoop/zookeeper/version-2` Pillanatképek és a tranzakciós naplókba a és `/hadoop/hdinsight-zookeeper/version-2`a rendszerbe, majd törölje ezeket a fájlokat a két könyvtárban 

1. Indítsa újra a problémát okozó ZooKeeper-kiszolgálót a Ambari vagy a ZooKeeper-gazdagépen. Ezután indítsa újra a szolgáltatást, amely problémákkal rendelkezik.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
