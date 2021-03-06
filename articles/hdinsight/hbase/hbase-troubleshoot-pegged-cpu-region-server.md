---
title: Rögzített CPU az Apache HBase-fürtben – Azure HDInsight
description: Az Azure HDInsight-ben elérhető Apache HBase-fürtön található, a tartományban található, rögzített CPU-kiszolgáló hibáinak megoldása
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: bed73c3ccc7f514ffc9ff8f97534ae4b249834ce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017015"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Forgatókönyv: rögzített CPU a régió-kiszolgálón az Apache HBase-fürtben az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase-régió kiszolgálói folyamata a 200%-os CPU-hoz közeledik, ami miatt a riasztások HBase Master folyamaton és fürtön nem működnek teljes kapacitással.

## <a name="cause"></a>Ok

Ha a HBase-fürt v 3.4-es verzióját futtatja, lehetséges, hogy a JDK-t a 1.7.0 _151 verziójára való frissítés okozta. A megjelenő tünet a régió kiszolgálója, amely a következőhöz közeledik: 200% CPU (annak ellenőrzéséhez, hogy ez a parancs fut-e `top` . ha van olyan folyamat, amely az 200%-os CPU-hoz csatlakozik, kérje le a PID-t, és ellenőrizze, hogy fut-e a régió kiszolgáló `ps -aux | grep`

## <a name="resolution"></a>Feloldás

1. Telepítse a jdk 1,8-et a fürt összes csomópontján az alábbiak szerint:

    * Futtassa a parancsfájl-műveletet `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` . Ügyeljen arra, hogy az összes csomóponton a Futtatás lehetőséget válassza.

    * Másik lehetőségként bejelentkezhet minden egyes csomópontba, és futtathatja a parancsot `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Ugrás a Ambari felhasználói felületére – `https://<clusterdnsname>.azurehdinsight.net` .

1. Navigáljon a **HBase->konfigurációk->Advanced – >Advanced** (speciális) elemre `hbase-env configs` , és módosítsa a változót a következőre: `JAVA_HOME` `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` . Mentse a konfiguráció módosítását.

1. [Nem kötelező, de ajánlott] [Az összes tábla kiürítése a fürtön](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. A Ambari felhasználói felületéről indítsa újra az összes újraindítást igénylő HBase-szolgáltatást.

1. A fürtön lévő adattól függően előfordulhat, hogy akár egy óráig is eltarthat, amíg a fürt stabil állapotba nem ér. A fürt megerősítésének módja a HMaster felhasználói felületének ellenőrzése (az összes régió-kiszolgáló aktívnak kell lennie) a Ambari (frissítés) vagy a átjárócsomóponthoz Run HBase shell, majd az status parancs futtatásával.

Annak ellenőrzéséhez, hogy a frissítés sikeres volt-e, ellenőrizze, hogy az adott HBase-folyamatok a megfelelő Java-verzióval kezdődnek-e, például a régió-kiszolgáló ellenőrzéséhez a következő módon:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>További lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]