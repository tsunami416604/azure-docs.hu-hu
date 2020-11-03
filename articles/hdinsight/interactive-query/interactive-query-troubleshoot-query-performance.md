---
title: Gyenge teljesítmény Apache Hive LLAP-lekérdezésekben az Azure HDInsight
description: Apache Hive LLAP lévő lekérdezések a vártnál lassabban lesznek végrehajtva az Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ee7ce401f889dd9c06b14860f4fc9674c5350b52
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288871"
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

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]