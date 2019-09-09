---
title: Rögzített CPU a régió-kiszolgálón az Apache HBase-fürtben az Azure HDInsight
description: Az Azure HDInsight-ben elérhető Apache HBase-fürtön található, a tartományban található, rögzített CPU-kiszolgáló hibáinak megoldása
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: b1fba84e7841713b005e387bcd806344f6a9e7a1
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810797"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Forgatókönyv: Rögzített CPU a régió-kiszolgálón az Apache HBase-fürtben az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase-régió kiszolgálói folyamata a 200%-os CPU-hoz közeledik, ami miatt a riasztások HBase Master folyamaton és fürtön nem működnek teljes kapacitással.

## <a name="cause"></a>Ok

Ha a HBase-fürt v 3.4-es verzióját futtatja, lehetséges, hogy a JDK-t a 1.7.0 _151 verziójára való frissítés okozta. A megjelenő tünet a régió kiszolgálója, amely a következőhöz közeledik: 200% CPU (annak `top` ellenőrzéséhez, hogy ez a parancs fut-e. ha van olyan folyamat, amely az 200%-os CPU-hoz csatlakozik `ps -aux | grep` , kérje le a PID-t, és ellenőrizze, hogy fut-e a régió kiszolgáló

## <a name="resolution"></a>Megoldás:

1. Telepítse a jdk 1,8-et a fürt összes csomópontján az alábbiak szerint:

    * Futtassa a parancsfájl- `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`műveletet. Ügyeljen arra, hogy az összes csomóponton a Futtatás lehetőséget válassza.

    * Másik lehetőségként bejelentkezhet minden egyes csomópontba, és futtathatja a `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`parancsot.

1. Ugrás a Ambari felhasználói felületére – `https://<clusterdnsname>.azurehdinsight.net`.

1. Navigáljon a **HBase-> konfigurációk-> Advanced – > Advanced** `hbase-env configs` (speciális `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`) elemre, és módosítsa a változót `JAVA_HOME` a következőre:. Mentse a konfiguráció módosítását.

1. [Nem kötelező, de ajánlott] [Az összes tábla kiürítése a fürtön](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. A Ambari felhasználói felületéről indítsa újra az összes újraindítást igénylő HBase-szolgáltatást.

1. A fürtön lévő adattól függően előfordulhat, hogy akár egy óráig is eltarthat, amíg a fürt stabil állapotba nem ér. A fürt megerősítésének módja a HMaster felhasználói felületének ellenőrzése (az összes régió-kiszolgáló aktívnak kell lennie) a Ambari (frissítés) vagy a átjárócsomóponthoz Run HBase shell, majd az status parancs futtatásával.

Annak ellenőrzéséhez, hogy a frissítés sikeres volt-e, ellenőrizze, hogy az adott HBase-folyamatok a megfelelő Java-verzióval kezdődnek-e, például a régió-kiszolgáló ellenőrzéséhez a következő módon:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
