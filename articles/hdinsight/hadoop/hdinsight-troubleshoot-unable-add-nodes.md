---
title: Nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz
description: Hibaelhárítás, hogy miért nem lehet csomópontokat hozzáadni az Apache Hadoop-fürthöz az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894100"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Eset: Nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet csomópontokat hozzáadni az Azure HDInsight-fürthöz.

## <a name="cause"></a>Ok

Az okok eltérőek lehetnek.

## <a name="resolution"></a>Megoldás:

A [Fürtméret](../hdinsight-scaling-best-practices.md) szolgáltatás használatával számítsa ki a fürthöz szükséges további magok számát. Ez a munkavégző csomópontok magjainak számától függ. Ezután próbálkozzon az alábbi lépésekkel:

* Ellenőrizze, hogy vannak-e elérhető magok a fürt helyén.

* Ellenőrizze a más helyeken rendelkezésre álló magok számát. Fontolja meg a fürt egy másik, elegendő elérhető maggal rendelkező helyen való létrehozását.

* Ha egy adott hely magkvótáját szeretné növelni, hozzon létre egy támogatási jegyet a HDInsight magkvóta-növeléséhez.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
