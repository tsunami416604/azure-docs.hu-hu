---
title: Lemezterület kezelése az Azure HDInsightban
description: Az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák lépéseinek és lehetséges megoldásai elhárítása.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473011"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Lemezterület kezelése az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="hive-log-configurations"></a>Hive-napló konfigurációi

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net`meg `CLUSTERNAME` a , ahol a fürt neve.

1. Keresse meg a Hive**Configs** > Advanced**Advanced hive-log4j** **(Hive** > Configs**Advanced** > Advanced hive-log4j) fájlját. Tekintse át a következő beállításokat:

    * `hive.root.logger=DEBUG,RFA`. Ez az alapértelmezett érték, módosítsa `INFO` a napló [szintet](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) úgy, hogy kevesebb naplóbejegyzést nyomtasson.

    * `log4jhive.log.maxfilesize=1024MB`. Ez az alapértelmezett érték, szükség szerint módosítva.

    * `log4jhive.log.maxbackupindex=10`. Ez az alapértelmezett érték, szükség szerint módosítva. Ha a paraméter nincs megadva, a létrehozott naplófájlok végtelenek lesznek.

## <a name="yarn-log-configurations"></a>Fonalnapló-konfigurációk

Tekintse át a következő konfigurációkat:

* Az Ambari

    1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net`meg `CLUSTERNAME` a , ahol a fürt neve.

    1. Nyissa meg a Hive Configs Advanced Resource Manager **(Hive** > Configs Advanced Resource Manager)**(Hive** > **Configs** > Advanced**Resource Manager) elemre.** Győződjön meg **arról, hogy a naplóösszesítés engedélyezése** be van jelölve. Ha le van tiltva, a névcsomópontok helyileg tartják a naplókat, és nem összesítik őket a távoli tárolóban az alkalmazás befejezésekor vagy megszüntetésekor.

* Győződjön meg arról, hogy a fürt mérete megfelelő a számítási feladathoz. Lehet, hogy a munkaterhelés nemrég módosult, vagy a fürt átlett méretezve. A fürt [felskálázása,](../hdinsight-scaling-best-practices.md) hogy megfeleljen a nagyobb számítási feladatoknak.

* `/mnt/resource`lehet, hogy árva fájlokkal van megtöltve (mint az erőforrás-kezelő újraindítása esetén). Szükség esetén manuálisan `/mnt/resource/hadoop/yarn/local`tisztítsa meg és tisztítsa `/mnt/resource/hadoop/yarn/log` meg a .

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
