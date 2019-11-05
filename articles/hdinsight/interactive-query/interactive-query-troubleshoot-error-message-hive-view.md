---
title: A hibaüzenet nem jelenik meg Apache Hive nézetben – Azure HDInsight
description: A lekérdezés Apache Hive nézetben meghiúsul az Azure HDInsight-fürt részleteinek megadása nélkül.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 38dd064fe8365e6661ce7ea7e1077a4e1e32dbf5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494261"
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

## <a name="resolution"></a>Felbontás

Tekintse meg a Hive_view jobb felső sarkában található értesítések lapot, és tekintse meg a teljes stacktrace és-hibaüzenetet.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához az Azure-Közösség és a megfelelő erőforrások összekapcsolásával: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletes információkat az [Azure-támogatáskérések létrehozásával](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) foglalkozó témakörben talál. Az előfizetés-kezeléssel és számlázással kapcsolatos támogatás a Microsoft Azure-előfizetés részét képezi, míg a technikai támogatást [Azure-támogatási csomagjainkkal](https://azure.microsoft.com/support/plans/) biztosítjuk.
