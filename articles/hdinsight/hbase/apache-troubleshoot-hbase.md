---
title: Az Azure HDInsight használatával történő HBase hibáinak megoldása
description: Választ kaphat a HBase és az Azure HDInsight való használattal kapcsolatos gyakori kérdésekre.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573945"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Az Apache HBase hibáinak megoldása az Azure HDInsight használatával

Ismerje meg a leggyakoribb problémákat és azok megoldásait, amikor Apache HBase-adattartalommal dolgozik az Apache Ambari-ben.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Több hozzá nem rendelt régióval Hogyan futtatni a hbck-parancsok jelentéseit?

A `hbase hbck` parancs futtatásakor a következő általános hibaüzenet jelenik meg: "több régió sincs kiosztva vagy lyukak a régiók láncában."

A HBase Master felhasználói felületén megtekintheti az összes régió-kiszolgáló közötti kiegyensúlyozatlan régiók számát. Ezután a parancs futtatásával `hbase hbck` megtekintheti a lyukakat a régió láncában.

Előfordulhat, hogy a lyukakat az offline régiók okozzák, ezért először javítsa ki a hozzárendeléseket. 

A nem hozzárendelt régiók normál állapotba való visszaállításához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a HDInsight HBase-fürtbe SSH használatával.
2. A Apache ZooKeeper rendszerhéjhoz való kapcsolódáshoz futtassa a `hbase zkcli` parancsot.
3. Futtassa a `rmr /hbase/regions-in-transition` parancsot vagy a `rmr /hbase-unsecure/regions-in-transition` parancsot.
4. A `hbase zkcli` rendszerhéjból való kilépéshez használja `exit` az parancsot.
5. Nyissa meg az Apache Ambari felhasználói felületét, majd indítsa újra az aktív HBase Master szolgáltatást.
6. Futtassa újra `hbase hbck` a parancsot (beállítások nélkül). Ellenőrizze a parancs kimenetét, és győződjön meg arról, hogy az összes régió hozzá van rendelve.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Hogyan kijavítani az időtúllépési problémákat, amikor hbck-parancsokat használ a régió-hozzárendelésekhez?

### <a name="issue"></a>Probléma

Ha a `hbck` parancs használatakor időtúllépési problémák merülhetnek fel, lehetséges, hogy több régió "átmenetes" állapotban van hosszú ideig. Ezeket a régiókat kapcsolat nélküli üzemmódban láthatja a HBase Master felhasználói felületen. Mivel a nagy számú régió átalakulóban van, HBase Master lehet, hogy időtúllépés történt, és a régiókat nem lehet ismét online állapotba hozni.

### <a name="resolution-steps"></a>A megoldás lépései

1. Jelentkezzen be a HDInsight HBase-fürtbe SSH használatával.
2. A Apache ZooKeeper rendszerhéjhoz való kapcsolódáshoz futtassa a `hbase zkcli` parancsot.
3. Futtassa a `rmr /hbase/regions-in-transition` vagy a `rmr /hbase-unsecure/regions-in-transition` parancsot.
4. A `hbase zkcli` rendszerhéjból való kilépéshez `exit` használja az parancsot.
5. A Ambari felhasználói felületén indítsa újra az aktív HBase Master szolgáltatást.
6. Futtassa újra `hbase hbck -fixAssignments` a parancsot.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
