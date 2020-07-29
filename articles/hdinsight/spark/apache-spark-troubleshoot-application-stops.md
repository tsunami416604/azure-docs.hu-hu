---
title: A Apache Spark streaming-alkalmazás 24 nap elteltével leáll az Azure HDInsight
description: Egy Apache Spark streaming-alkalmazás 24 nap elteltével leáll, és nincsenek hibák a naplófájlokban.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894437"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Forgatókönyv: Apache Spark folyamatos átviteli alkalmazás az Azure HDInsight 24 napjainak végrehajtása után leáll

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy Apache Spark streaming-alkalmazás 24 nap elteltével leáll, és nincsenek hibák a naplófájlokban.

## <a name="cause"></a>Ok

Az `livy.server.session.timeout` érték azt határozza meg, hogy az Apache Livy mennyi ideig várjon, amíg a munkamenet befejeződik. Ha a munkamenet hossza eléri az `session.timeout` értéket, a rendszer automatikusan leveszi a Livy-munkamenetet és az alkalmazást.

## <a name="resolution"></a>Megoldás:

A hosszú ideig futó feladatok esetében növelje a `livy.server.session.timeout` Ambari felhasználói felületének értékét. A Livy konfigurációját a Ambari felhasználói felületén keresztül érheti el az URL-cím használatával `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Cserélje le a `<yourclustername>` nevet a HDInsight-fürt nevére a portálon látható módon.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
