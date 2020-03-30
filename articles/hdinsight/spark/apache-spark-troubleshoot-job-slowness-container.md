---
title: Az Apache Spark lelassul, ha az Azure HDInsight tárhelye sok fájlt tartalmaz
description: Az Apache Spark-feladat lassan fut, ha az Azure storage-tároló sok fájlt tartalmaz az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894323"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Az Apache Spark-feladatok lassan futnak, ha az Azure Storage-tároló sok fájlt tartalmaz az Azure HDInsightban

Ez a cikk az Apache Spark-összetevők Azure HDInsight-fürtökben való használata során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

HDInsight-fürt futtatásakor az Azure Storage tárolóba írásra írt Apache Spark-feladat lelassul, ha sok fájl/almappa van. Például 20 másodpercet vesz igénybe, amikor egy új tárolóba ír, de körülbelül 2 percet, amikor 200 ezer fájlt tartalmazó tárolóba ír.

## <a name="cause"></a>Ok

Ez egy ismert Spark-probléma. A lassúság származik a `ListBlob` és `GetBlobProperties` a spark-i feladat végrehajtása során műveletek.

A partíciók nyomon követéséhez `FileStatusCache` a Sparknak fenn kell tartania egy olyan partíciót, amely a címtárstruktúrával kapcsolatos információkat tartalmazza. Ezzel a gyorsítótárral a Spark elemezheti az elérési utakat, és tisztában lehet az elérhető partíciókkal. A partíciók nyomon követése előnye, hogy a Spark csak az adatok olvasásakor érinti a szükséges fájlokat. Ahhoz, hogy ezeket az információkat naprakészen tartsa, amikor új adatokat ír, a Sparknak fel kell sorolnia az összes fájlt a könyvtár alatt, és frissítenie kell ezt a gyorsítótárat.

A Spark 2.1-ben, bár nem kell minden írás után frissítenünk a gyorsítótárat, a Spark ellenőrzi, hogy egy meglévő partícióoszlop megegyezik-e az aktuális írási kérelemben javasolt oszloppal, így minden írási művelet elején is sorolási műveletekhez vezet.

A Spark 2.2-ben, ha hozzáfűzési módban adatokat ír, ezt a teljesítményproblémát ki kell javítani.

## <a name="resolution"></a>Megoldás:

Particionált adatkészlet létrehozásakor fontos, hogy particionálási sémát használjon, amely korlátozza `FileStatusCache`a Spark által a frissítéshez listázandó fájlok számát.

Minden Nth mikrokötegre, ahol az N % 100 == 0 (100 csak egy példa), helyezze át a meglévő adatokat egy másik könyvtárba, amelyet a Spark tölthet be.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
