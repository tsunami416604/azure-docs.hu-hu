---
title: Apache Ambari felhasználói felület 502 hiba az Azure HDInsight
description: Apache Ambari felhasználói felület 502 hiba, amikor megpróbál hozzáférni az Azure HDInsight-fürthöz
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: d4bcb8475f822675d39ca8e542155779384eacf1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087834"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Forgatókönyv: Apache Ambari felhasználói felület 502 hiba az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Amikor megpróbál hozzáférni az Apache Ambari felhasználói felületéhez a HDInsight-fürthöz, a következőhöz hasonló üzenet jelenik meg: "502 – a webkiszolgáló érvénytelen választ kapott az átjáróként vagy proxykiszolgálóként való működés közben."

## <a name="cause"></a>Ok

Általánosságban a HTTP 502 állapotkód azt jelenti, hogy a Ambari-kiszolgáló nem fut megfelelően az aktív átjárócsomóponthoz. Néhány lehetséges kiváltó oka van.

## <a name="resolution"></a>Megoldás:

A legtöbb esetben a probléma enyhítése érdekében újraindíthatja az aktív átjárócsomóponthoz. Vagy válasszon nagyobb méretű virtuálisgép-méretet a átjárócsomóponthoz.

### <a name="ambari-server-failed-to-start"></a>Nem sikerült elindítani a Ambari-kiszolgálót

A ambari-naplók segítségével megtudhatja, miért nem sikerült elindítani a Ambari-kiszolgálót. Az egyik gyakori ok az adatbázis konzisztencia-ellenőrzési hibája. Ezt a naplófájlban találja: `/var/log/ambari-server/ambari-server-check-database.log`.

Ha módosította a fürt csomópontjának módosításait, vonja vissza őket. Mindig használja a Ambari felhasználói felületét a Hadoop/Spark-hoz kapcsolódó konfigurációk módosításához.

### <a name="ambari-server-taking-100-cpu-utilization"></a>A Ambari-kiszolgáló 100%-os CPU-kihasználtságot vesz fel

Ritka helyzetekben láttuk, hogy a ambari-kiszolgáló folyamata folyamatosan 100%-os CPU-kihasználtságot eredményezett. Enyhítő megoldásként SSH-t használhat az aktív átjárócsomóponthoz, és megkezdheti a Ambari-kiszolgáló folyamatát, és elindíthatja azt.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari-kiszolgáló megölte a bácsi-Killer

Bizonyos helyzetekben a átjárócsomóponthoz elfogy a memóriából, és a Linux bácsi-Killer elkezdi felvenni a folyamatokat. Ezt a helyzetet úgy ellenőrizheti, ha megkeresi a AmbariServer folyamat AZONOSÍTÓját, amely nem található. Ezután tekintse meg `/var/log/syslog`a következőt:

```
Jul 27 15:29:30 hn0-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Ezután azonosítsa, hogy mely folyamatok vesznek részt a memóriában, és próbálja megismételni a kiváltó okot.

### <a name="other-issues-with-ambari-server"></a>A Ambari-kiszolgálóval kapcsolatos egyéb problémák

Ritkán a Ambari-kiszolgáló nem tudja kezelni a bejövő kérelmet, további információt a Ambari-Server naplókban talál a hibákért. Egy ilyen eset a következőhöz hasonló:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
