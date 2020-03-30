---
title: Rögzített CPU az Apache HBase fürtben - Azure HDInsight
description: Az Azure HDInsight ban az Apache HBase fürt régiókiszolgálóján lévő rögzített PROCESSZOR – problémamegoldás
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887308"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Eset: Rögzített CPU a régiókiszolgálón az Apache HBase fürtben az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase régiókiszolgálói folyamat közel 200%-os CPU-t foglal el, így a HBase Master folyamatra és fürtre vonatkozó riasztások nem teljes kapacitással működnek.

## <a name="cause"></a>Ok

Ha a HBase fürt 3.4-es verzióját futtatja, előfordulhat, hogy a jdk 1.7.0_151-es verziójára való frissítése által okozott potenciális hiba hibát okozott. A tünet azt látjuk, hogy a régió kiszolgáló folyamat a 200% CPU-hoz közel 200% -os CPU-t foglal el (hogy ellenőrizze a `top` parancs futtatását; ha van egy közel 200% CPU-t elfoglaló folyamat, megkapja a pid-et, és megerősíti, hogy régiókiszolgálófolyamat a futással). `ps -aux | grep`

## <a name="resolution"></a>Megoldás:

1. Telepítse a jdk 1.8-at a fürt összes csomópontjára az alábbiak szerint:

    * Futtassa `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`a parancsfájlműveletet . Ügyeljen arra, hogy válassza ki a lehetőséget, hogy fut az összes csomóponton.

    * Azt is megteheti, hogy minden egyes csomópontra `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`bejelentkezik, és futtatja a parancsot.

1. Tovább az Ambari `https://<clusterdnsname>.azurehdinsight.net`felhasználói felülethez - .

1. Nyissa meg a **HBase->Configs->Advanced->Advanced csomópontot,** `hbase-env configs` és módosítsa a változót `JAVA_HOME` a (Nass. `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` Mentse a konfigurációs módosítást.

1. [Nem kötelező, de ajánlott] [A fürt összes táblájának kiürítése](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. Az Ambari felhasználói felületéről indítsa újra újra az összes újraindítást igénylő HBase-szolgáltatást.

1. A fürt adataitól függően néhány percig, akár egy órát is igénybe vehet, amíg a fürt stabil állapotba kerül. A fürt stabil állapotba kerülésének ellenőrzése az Ambari (frissítés) HMaster felhasználói felületének (minden régiókiszolgálóaktív) ellenőrzése, vagy a Headnode futtatása HBase rendszerhéj, majd az állapotparancs futtatása.

A frissítés sikerességének ellenőrzéséhez ellenőrizze, hogy a megfelelő HBase folyamatok a megfelelő java verzióval kezdődnek-e - például a régiókiszolgáló ellenőrzéséhez:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
