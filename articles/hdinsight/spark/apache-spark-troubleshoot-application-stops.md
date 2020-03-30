---
title: Az Apache Spark Streaming alkalmazás 24 nap után leáll az Azure HDInsightban
description: Az Apache Spark Streaming alkalmazás 24 napig tartó végrehajtása után leáll, és nincsenek hibák a naplófájlokban.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894437"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Eset: Az Apache Spark Streaming alkalmazás leáll, miután 24 napig futtatta az Azure HDInsightban

Ez a cikk az Apache Spark-összetevők Azure HDInsight-fürtökben való használata során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Spark Streaming alkalmazás 24 napig tartó végrehajtása után leáll, és nincsenek hibák a naplófájlokban.

## <a name="cause"></a>Ok

Az `livy.server.session.timeout` érték határozza meg, hogy az Apache Livy-nek mennyi ideig kell várnia a munkamenet befejezésére. Amint a munkamenet `session.timeout` hossza eléri az értéket, a Livy-munkamenet és az alkalmazás automatikusan leáll.

## <a name="resolution"></a>Megoldás:

Hosszú ideig futó feladatok esetén `livy.server.session.timeout` növelje az Ambari felhasználói felület használatának értékét. A Livy konfigurációt az Ambari felhasználói felületéről az URL-cím `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`segítségével érheti el.

Cserélje `<yourclustername>` le a HDInsight-fürt nevét a portálon látható módon.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
