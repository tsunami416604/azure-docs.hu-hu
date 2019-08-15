---
title: BindException – az Azure HDInsight már használatban van a címe
description: BindException – az Azure HDInsight már használatban van a címe
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 8851a4dfb7deafab7ad77ef80619dd49ca46ed71
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947850"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Forgatókönyv: BindException – az Azure HDInsight már használatban van a címe

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase-régió kiszolgálójának újraindítási művelete nem fejeződött be. A (z) azon munkavégző csomópontok mappájában,aholarégiókiszolgálójánakindításasikertelen,akövetkezőhözhasonlóhibaüzenetjelenhetmeg:`/var/log/hbase` `region-server.log`

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Ok

A HBase-régió kiszolgálóinak újraindítása a nagy számítási feladatok tevékenysége során. Az alábbiakban látható, hogy mi történik a jelenetek mögött, amikor egy felhasználó elindítja az újraindítási műveletet a HBase-régió kiszolgálójának Ambari felhasználói felületéről:

1. A Ambari-ügynök leállítási kérelmet küld a régió-kiszolgálónak.

1. A Ambari-ügynök ezután 30 másodpercig várakozik, hogy a régió kiszolgálója szabályosan leálljon.

1. Ha az alkalmazás továbbra is a régió-kiszolgálóval csatlakozik, az nem fog azonnal leállni, és így a 30 másodperces időkorlát hamarabb lejár.

1. 30 másodperc lejárta után a Ambari-ügynök kényszerített kill (kill-9) értéket küld a régió-kiszolgálónak.

1. A hirtelen leállás miatt, bár a régió-kiszolgáló folyamata leállt, előfordulhat, hogy a folyamathoz társított port nem jelenik meg `AddressBindException`, ami végül a következőhöz vezet:.

## <a name="resolution"></a>Megoldás:

Az újraindítás megkezdése előtt csökkentse a HBase-régió kiszolgálóinak terhelését.

Azt is megteheti, hogy a következő parancsokkal próbálja meg manuálisan újraindítani a régió kiszolgálóit a munkavégző csomópontokon:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
