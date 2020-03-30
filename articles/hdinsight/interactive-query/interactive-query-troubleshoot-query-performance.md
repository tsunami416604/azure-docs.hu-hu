---
title: Gyenge teljesítmény az Apache Hive LLAP-lekérdezésekben az Azure HDInsightban
description: Az Apache Hive LLAP lekérdezései a vártnál lassabban hajtják végre az Azure HDInsightban.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895127"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Eset: Gyenge teljesítmény az Apache Hive LLAP-lekérdezésekben az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtök interaktív lekérdezési összetevőinek használatával kapcsolatos problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az alapértelmezett fürtkonfigurációk nincsenek megfelelően hangolva a számítási feladatokhoz. A Hive LLAP lekérdezései a vártnál lassabban hajtják végre a végrehajtást.

## <a name="cause"></a>Ok

Ez számos okból fordulhat elő.

## <a name="resolution"></a>Megoldás:

Az LLAP illesztéseket és összesítéseket tartalmazó lekérdezésekhez van optimalizálva. Az alábbihoz hasonló lekérdezések nem teljesítenek jól az interaktív hive-fürtben:

```
select * from table where column = "columnvalue"
```

A pontlekérdezés teljesítményének javítása a Hive LLAP-ban a következő konfigurációkat állítsa be:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Az LLAP-gyorsítótár használatát a következő konfigurációmódosítással is növelheti:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
