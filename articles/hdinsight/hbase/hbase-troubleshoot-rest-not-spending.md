---
title: Az Apache HBase REST nem válaszol a kérelmekre az Azure HDInsight
description: Hárítsa el a problémát az Apache HBase REST szolgáltatásban, és ne válaszoljon az Azure HDInsight-kérelmekre.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: c4a0ef82b951fa43eb4c58050d3148fd2d695026
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286982"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Forgatókönyv: az Apache HBase REST nem válaszol a kérelmekre az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase REST szolgáltatás nem válaszol a kérelmekre az Azure HDInsight.

## <a name="cause"></a>Ok

Ennek lehetséges oka az lehet, hogy az Apache HBase REST-szolgáltatás a kiszivárgó szoftvercsatornák, ami különösen gyakori, ha a szolgáltatás hosszú ideig futott (például hónapok). Az ügyfél-SDK-ból a következőhöz hasonló hibaüzenet jelenhet meg:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Feloldás

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

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]