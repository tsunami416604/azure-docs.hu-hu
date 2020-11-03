---
title: A hibaüzenet nem jelenik meg Apache Hive nézetben – Azure HDInsight
description: A lekérdezés Apache Hive nézetben meghiúsul az Azure HDInsight-fürt részleteinek megadása nélkül.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 7aadef0d4c70f748b4f7a7c7f9fc16c38216fa7a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288950"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Forgatókönyv: a lekérdezési hibaüzenet nem jelenik meg Apache Hive nézetben az Azure HDInsight

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Apache Hive View lekérdezési hibaüzenet a következőhöz hasonló módon fog kinézni, további információ nélkül:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Ok

Időnként előfordulhat, hogy a lekérdezési hiba hibaüzenete túl nagy a struktúra nézet főoldalán való megjelenítéshez.

## <a name="resolution"></a>Feloldás

Tekintse meg az értesítések lapot a Hive_view jobb felső sarkában, és tekintse meg a teljes stacktrace és-hibaüzenetet.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]