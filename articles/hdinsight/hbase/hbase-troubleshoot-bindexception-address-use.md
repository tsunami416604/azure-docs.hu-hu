---
title: BindException – Az Azure HDInsightban már használatban lévő cím
description: BindException – Az Azure HDInsightban már használatban lévő cím
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887342"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Forgatókönyv: BindException – Az Azure HDInsightban már használatban lévő cím

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase régiókiszolgáló újraindítási művelete nem fejeződik be. A `region-server.log` feldolgozócsomópontok in könyvtárából, `/var/log/hbase` ahol a régiókiszolgáló indítása sikertelen, a következő höz hasonló hibaüzenet jelenhet meg:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Ok

Az Apache HBase régiókiszolgálóinak újraindítása nagy munkaterhelési tevékenység során. Az alábbiakban az látható, mi történik a színfalak mögött, amikor a felhasználó elindítja az újraindítási műveletet a HBase régió kiszolgálóaz Apache Ambari UI:

1. Az Ambari ügynök leállítási kérelmet küld a régiókiszolgálónak.

1. Az Ambari ügynök 30 másodpercet vár a régiókiszolgáló kecses leállítására

1. Ha az alkalmazás továbbra is csatlakozik a régiókiszolgálóhoz, a kiszolgáló nem áll le azonnal. A 30 másodperces időkiállás a leállítás előtt lejár.

1. 30 másodperc elteltével az Ambari ügynök`kill -9`erőölési () parancsot küld a régiókiszolgálónak.

1. A hirtelen leállítás miatt, bár a régiókiszolgáló folyamata meghal, előfordulhat, hogy a `AddressBindException`folyamathoz társított port nem szabadul fel, ami végül a hoz vezet.

## <a name="resolution"></a>Megoldás:

Csökkentse a HBase régiókiszolgálók terhelését az újraindítás megkezdése előtt. Is, ez egy jó ötlet, hogy először flush az összes asztalt. A táblák kiürítésével kapcsolatos tudnivalókat a [HDInsight HBase: Az Apache HBase fürt újraindítási idejének javítása a táblák kiürítésével című](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)témakörben található.

Másik lehetőségként próbálja meg manuálisan újraindítani a régiókiszolgálókat a munkavégző csomópontokon a következő parancsokkal:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
