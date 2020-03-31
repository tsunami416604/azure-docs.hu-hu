---
title: Az Apache ZooKeeper kiszolgáló nem tud kvórumot létrehozni az Azure HDInsightban
description: Az Apache ZooKeeper kiszolgáló nem tud kvórumot létrehozni az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250238"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Az Apache ZooKeeper kiszolgáló nem tud kvórumot létrehozni az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache ZooKeeper kiszolgáló nem kifogástalan, a tünetek a következők lehetnek: mindkét erőforrás-kezelő/névcsomópont készenléti állapotban van, az egyszerű HDFS-műveletek nem működnek, leáll, `zkFailoverController` és nem indítható el, a Fonal/Spark/Livy feladatok zookeeper hibák miatt sikertelenek. LLAP démonok is előfordulhat, hogy nem indul el a Secure Spark vagy interaktív Hive-fürtök. A következőhöz hasonló hibaüzenet jelenhet meg:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

A Zookeeper Server bejelentkezik minden Zookeeper fogadó /var/log/zookeeper/zookeeper-zookeeper-server-\*.out, akkor is megjelenik a következő hiba:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Ok

Ha a pillanatképfájlok nagy vagy a pillanatképfájlok kötete sérült, a ZooKeeper kiszolgáló nem fog kvórumot létrehozni, ami a ZooKeeper kapcsolódó szolgáltatásait nem megfelelő állapotba hozza. ZooKeeper szerver nem távolítja el a régi pillanatkép fájlokat az adatkönyvtár, hanem ez egy időszakos feladat, hogy a felhasználók számára, hogy fenntartsák az egészséges ZooKeeper. További információ: [ZooKeeper Erősségei és korlátai](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Megoldás:

Ellenőrizze zookeeper `/hadoop/zookeeper/version-2` adatkönyvtárban, és `/hadoop/hdinsight-zookeeper/version-2` megtudja, ha a pillanatképek fájlmérete nagy. Nagy pillanatképek esetén tegye a következő lépéseket:

1. Biztonsági másolatot `/hadoop/zookeeper/version-2` készíthet `/hadoop/hdinsight-zookeeper/version-2`a pillanatképekről a és a ban.

1. A pillanatképek `/hadoop/zookeeper/version-2` karbantartása a és `/hadoop/hdinsight-zookeeper/version-2`a ban

1. Indítsa újra az összes ZooKeeper kiszolgálót az Apache Ambari felhasználói felületéről.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

- Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

- Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
