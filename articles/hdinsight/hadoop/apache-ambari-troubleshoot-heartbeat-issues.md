---
title: Az Apache Ambari szívveréssel kapcsolatos problémái az Azure HDInsightban
description: Az Apache Ambari szívverésével kapcsolatos problémák különböző okainak áttekintése az Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 8d23b89ab155c47e09f82d22c065db47ab9ac73d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540788"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Az Apache Ambari szívveréssel kapcsolatos problémái az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-high-cpu-utilization"></a>Forgatókönyv: magas CPU-kihasználtság

### <a name="issue"></a>Probléma

A Ambari-ügynök magas CPU-kihasználtsággal rendelkezik, ami a Ambari felhasználói felületének riasztásait eredményezi, hogy egyes csomópontok esetén a Ambari-ügynök szívverése megszakad. A szívverés elveszett riasztása általában átmeneti.

### <a name="cause"></a>Ok

A különböző ambari hibák miatt előfordulhat, hogy a ambari-ügynöknek magas (100) százalékos CPU-kihasználtsága lehet.

### <a name="resolution"></a>Feloldás

1. Azonosítsa a ambari-ügynök folyamat-AZONOSÍTÓját (PID):

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Ezután futtassa a következő parancsot a processzorhasználat megjelenítéséhez:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. A probléma megoldásához indítsa újra a ambari-Agent ügynököt:

    ```bash
    service ambari-agent restart
    ```

1. Ha az újraindítás nem működik, öld meg a ambari folyamatot, majd indítsa el:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Forgatókönyv: a Ambari-ügynök nem indult el

### <a name="issue"></a>Probléma

A Ambari-ügynök nem indult el, amely a Ambari felhasználói felületén riasztásokat eredményez, hogy egyes csomópontok esetében a Ambari-ügynök szívverése megszakad.

### <a name="cause"></a>Ok

A riasztások oka a Ambari-ügynök nem fut.

### <a name="resolution"></a>Feloldás

1. Ambari-ügynök állapotának megerősítése:

    ```bash
    service ambari-agent status
    ```

1. Ellenőrizze, hogy futnak-e a feladatátvételi vezérlő szolgáltatásai:

    ```bash
    ps -ef | grep failover
    ```

    Ha a feladatátvételi vezérlő szolgáltatásai nem futnak, valószínűleg egy probléma okozza, hogy a hdinsight-ügynök nem indítja el a feladatátvételi vezérlőt. A fájlból olvassa be a hdinsight-ügynök naplóját `/var/log/hdinsight-agent/hdinsight-agent.out` .

## <a name="scenario-heartbeat-lost-for-ambari"></a>Forgatókönyv: elveszett szívverés a Ambari esetében

### <a name="issue"></a>Probléma

A Ambari szívverés ügynöke megszakadt.

### <a name="cause"></a>Ok

A OMS-naplók nagy CPU-kihasználtságot okoznak.

### <a name="resolution"></a>Feloldás

* Tiltsa le Azure Monitor naplózást a [disable-AzHDInsightMonitoring PowerShell-](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) parancsmag használatával.
* A `mdsd.warn` naplófájl törlése

---

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.