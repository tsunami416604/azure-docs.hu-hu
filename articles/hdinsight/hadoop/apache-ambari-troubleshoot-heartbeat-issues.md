---
title: Az Apache Ambari szívveréssel kapcsolatos problémái az Azure HDInsightban
description: Az Apache Ambari szívverési problémáinak különböző okainak áttekintése az Azure HDInsightban
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057073"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Az Apache Ambari szívveréssel kapcsolatos problémái az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-high-cpu-utilization"></a>Forgatókönyv: Magas CPU-kihasználtság

### <a name="issue"></a>Probléma

Ambari ügynök magas cpu-kihasználtság, ami riasztásokat ambari felhasználói felület, hogy egyes csomópontok az Ambari ügynök szívverése elvész. A szívverés elveszett riasztás általában átmeneti.

### <a name="cause"></a>Ok

Különböző ambari-ügynök hibák miatt, ritka esetekben az ambari-ügynök magas (közel 100) százalékos CPU-kihasználtsága.

### <a name="resolution"></a>Megoldás:

1. Az ambari-ügynök folyamatazonosítójának (pid) azonosítása:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Ezután futtassa a következő parancsot a processzorhasználat megjelenítéséhez:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Az ambari-agent újraindítása a probléma enyhítése érdekében:

    ```bash
    service ambari-agent restart
    ```

1. Ha az újraindítás nem működik, öld meg az ambari-agent folyamatot, majd indítsd el:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Eset: Az Ambari ügynök nem indult el

### <a name="issue"></a>Probléma

Ambari ügynök még nem indult el, amely az Ambari felhasználói felületétől származó riasztásokat eredményez, amelyek egyes csomópontok esetében az Ambari ügynök szívverése elvész.

### <a name="cause"></a>Ok

A riasztásokat az Ambari ügynök nem fut.

### <a name="resolution"></a>Megoldás:

1. Ambari-ügynök állapotának megerősítése:

    ```bash
    service ambari-agent status
    ```

1. Ellenőrizze, hogy futnak-e a feladatátvevő vezérlő szolgáltatások:

    ```bash
    ps -ef | grep failover
    ```

    Ha a feladatátvevő vezérlő szolgáltatások nem futnak, valószínűleg egy probléma miatt akadályozza meg a hdinsight-ügynök indítását feladatátvevő vezérlő. Ellenőrizze a hdinsight-agent naplóját a fájlból. `/var/log/hdinsight-agent/hdinsight-agent.out`

## <a name="scenario-heartbeat-lost-for-ambari"></a>Forgatókönyv: Szívverés elveszett Ambari

### <a name="issue"></a>Probléma

Ambari szívverés ügynök elveszett.

### <a name="cause"></a>Ok

Az OMS-naplók magas processzorkihasználtságot okoznak.

### <a name="resolution"></a>Megoldás:

* Tiltsa le az Azure Monitor naplózását az [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell parancsmag használatával.
* A `mdsd.warn` naplófájl törlése

---

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
