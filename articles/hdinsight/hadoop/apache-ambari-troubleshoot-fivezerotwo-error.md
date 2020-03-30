---
title: Apache Ambari UI 502 hiba az Azure HDInsightban
description: Apache Ambari UI 502 hiba, amikor megpróbálja elérni az Azure HDInsight-fürtöt
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895758"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Eset: Apache Ambari UI 502 hiba az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Amikor megpróbálja elérni a HDInsight-fürt Apache Ambari felhasználói felületét, a következőhöz hasonló üzenetet kap, mint például: "502 – A webkiszolgáló érvénytelen választ kapott, miközben átjáróként vagy proxykiszolgálóként működött."

## <a name="cause"></a>Ok

A HTTP 502 állapotkód általában azt jelenti, hogy az Ambari-kiszolgáló nem fut megfelelően az aktív csomóponton. Van néhány lehetséges kiváltó oka.

## <a name="resolution"></a>Megoldás:

A legtöbb esetben a probléma enyhítése érdekében újraindíthatja az aktív headnode. Vagy válasszon egy nagyobb virtuális gép méretét a headnode.Or choose a larger VM size for your headnode.

### <a name="ambari-server-failed-to-start"></a>Az Ambari kiszolgáló nem tudott elindulni

Ellenőrizheti az ambari-szerver naplókat, hogy megtudja, miért nem indult el az Ambari szerver. Ennek egyik gyakori oka az adatbázis-konzisztencia-ellenőrzési hiba. Ezt ebben a naplófájlban `/var/log/ambari-server/ambari-server-check-database.log`találja meg: .

Ha bármilyen módosítást végzett a fürtcsomóponton, vonja vissza azokat. Mindig használja az Ambari felhasználói felületet a Hadoop/Spark-kal kapcsolatos konfigurációk módosításához.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari szerver figyelembe 100%-os CPU-kihasználtság

Ritka esetekben, láttuk ambari-szerver folyamat közel 100%-os CPU-kihasználtság folyamatosan. Ennek mérséklése ként ssh az aktív headnode, és megöli az Ambari kiszolgáló folyamat, és indítsa újra.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari szerver megölte oom-gyilkos

Bizonyos esetekben a headnode elfogy a memória, és a Linux oom-gyilkos elkezdi felvenni a folyamatokat ölni. Ezt a helyzetet az AmbariServer folyamatazonosítóban való kereséssel ellenőrizheti, amely nem található. Akkor nézd `/var/log/syslog`meg a , és keresse meg valami ilyesmi:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Ezután azonosítsa, hogy mely folyamatok vesznek emlékeket, és próbálja meg tovább kiváltó oka.

### <a name="other-issues-with-ambari-server"></a>Egyéb problémák Ambari szerver

Ritkán az Ambari szerver nem tudja kezelni a bejövő kérelmet, további információkat találhat az ambari-szerver naplók ból bármilyen hibára. Az egyik ilyen eset egy ilyen hiba:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
