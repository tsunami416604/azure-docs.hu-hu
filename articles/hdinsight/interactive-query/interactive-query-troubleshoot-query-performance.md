---
title: Gyenge teljesítmény Apache Hive LLAP-lekérdezésekben az Azure HDInsight
description: Apache Hive LLAP lévő lekérdezések a vártnál lassabban lesznek végrehajtva az Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895127"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Forgatókönyv: gyenge teljesítmény Apache Hive LLAP-lekérdezésekben az Azure HDInsight

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az alapértelmezett fürtkonfiguráció nincsenek megfelelően hangolva a számítási feladatokhoz. A kaptár LLAP lévő lekérdezések a vártnál lassabban lesznek végrehajtva.

## <a name="cause"></a>Ok

Ez különféle okok miatt fordulhat elő.

## <a name="resolution"></a>Feloldás

A LLAP illesztéseket és összesítéseket érintő lekérdezésekre van optimalizálva. A következőhöz hasonló lekérdezések nem jól teljesítenek interaktív kaptár-fürtben:

```
select * from table where column = "columnvalue"
```

Ha javítani szeretné a pont lekérdezési teljesítményét a kaptár LLAP, állítsa be a következő konfigurációkat:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Növelheti a LLAP gyorsítótár használatát is, így javíthatja a teljesítményt a következő konfigurációs változásokkal:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
