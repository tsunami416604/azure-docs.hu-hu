---
title: Az Apache HBase REST nem válaszol a kérésekre az Azure HDInsightban
description: Hárítsa el azt a problémát, amely miatt az Apache HBase REST nem válaszol az Azure HDInsight-beli kérésekre.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887172"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Eset: Az Apache HBase REST nem válaszol az Azure HDInsight ban lévő kérésekre

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase REST szolgáltatás nem válaszol az Azure HDInsight ban lévő kérésekre.

## <a name="cause"></a>Ok

A lehetséges ok itt lehet Apache HBase REST szolgáltatás szivárog foglalat, ami különösen gyakori, ha a szolgáltatás már régóta fut (például hónapok). Az ügyfél SDK-ból a következőhöz hasonló hibaüzenet jelenhet meg:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Megoldás:

Indítsa újra a HBase REST programot az alábbi paranccsal az SSH-állomásra való ssh-elés után. Parancsfájlműveletek segítségével is újraindíthatja ezt a szolgáltatást az összes munkavégző csomóponton:

```bash
sudo service hdinsight-hbrest restart
```

Ez a parancs leállítja a HBase régiókiszolgálót ugyanazon az állomáson. Manuálisan is elindíthatja a HBase Region Server alkalmazást az Ambari segítségével, vagy hagyja, hogy az Ambari automatikus újraindítási funkciói automatikusan helyreállítsák a HBase Region Server kiszolgálót.

Ha a probléma továbbra is fennáll, telepítheti a következő kockázatcsökkentési parancsfájlt CRON-feladatként, amely minden munkavégző csomóponton 5 percenként fut. Ez a kockázatcsökkentő parancsfájl pingeli a REST-szolgáltatást, és újraindítja azt, ha a REST szolgáltatás nem válaszol.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
