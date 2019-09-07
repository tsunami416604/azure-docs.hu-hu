---
title: Az Apache HBase REST nem válaszol a kérelmekre az Azure HDInsight
description: Hárítsa el a problémát az Apache HBase REST szolgáltatásban, és ne válaszoljon az Azure HDInsight-kérelmekre.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 5b1291e6996cb5db366128f829ca2cdf3cad0a9d
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735303"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Forgatókönyv: Az Apache HBase REST nem válaszol a kérelmekre az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase REST szolgáltatás nem válaszol a kérelmekre az Azure HDInsight.

## <a name="cause"></a>Ok

Ennek lehetséges oka az lehet, hogy az Apache HBase REST-szolgáltatás a kiszivárgó szoftvercsatornák, ami különösen gyakori, ha a szolgáltatás hosszú ideig futott (például hónapok). Az ügyfél-SDK-ból a következőhöz hasonló hibaüzenet jelenhet meg:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Megoldás:

Indítsa újra a HBase REST-et az alábbi parancs használatával, miután SSH a gazdagépre. Parancsfájl-műveletek használatával a szolgáltatás újraindítását is elvégezheti az összes munkavégző csomóponton:

```bash
sudo service hdinsight-hbrest restart
```

Ez a parancs leállítja a HBase-régió kiszolgálóját ugyanazon a gazdagépen. A HBase-régió kiszolgálóját manuálisan is elindíthatja a Ambari használatával, vagy engedélyezheti a Ambari automatikus újraindítási funkciójának automatikus helyreállítását a HBase-régió kiszolgáló

Ha a probléma továbbra is fennáll, telepítheti a következő kockázatcsökkentő parancsfájlt CRON-feladatként, amely minden munkavégző csomóponton 5 percenként fut. Ez a megoldási parancsfájl Pingeli a REST szolgáltatást, és újraindítja azt abban az esetben, ha a REST szolgáltatás nem válaszol.

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

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
