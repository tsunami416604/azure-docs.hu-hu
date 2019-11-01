---
title: Apache Spark lassú, ha az Azure HDInsight-tárolóban sok fájl van
description: Apache Spark a feladatok lassan futnak, amikor az Azure Storage-tároló sok fájlt tartalmaz az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: 82280532fa91923bd08d8ff3164dc841282c392c
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241173"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Apache Spark a feladatok lassan futnak, amikor az Azure Storage-tároló sok fájlt tartalmaz az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

HDInsight-fürt futtatásakor az Azure Storage-tárolóba írást Apache Spark feladat lassú lesz, ha sok fájl/almappa van. Például a rendszer 20 másodpercet vesz igénybe egy új tárolóba való íráskor, de körülbelül 2 percet, amikor több mint 200.000-fájlokat tartalmazó tárolóba ír.

## <a name="cause"></a>Ok

Ez egy ismert Spark-probléma. A lassítás a Spark-feladatok végrehajtása során `ListBlob` és `GetBlobProperties` műveletből származik.

A partíciók nyomon követéséhez a Sparknak meg kell őriznie egy `FileStatusCache`, amely a címtár struktúrájára vonatkozó információkat tartalmaz. Ezen gyorsítótár használatával a Spark elemezheti az elérési utakat, és tisztában lehet a rendelkezésre álló partíciókkal. A partíciók nyomon követésének előnye, hogy a Spark csak az adatolvasás során érinti a szükséges fájlokat. Az adatok naprakészen tartásához az új adatok írásakor a Sparknak fel kell sorolnia a könyvtár alatt lévő összes fájlt, és frissítenie kell a gyorsítótárat.

A Spark 2,1-es verziójában a Spark azt jelzi, hogy egy meglévő partíciós oszlop megegyezik-e a jelenlegi írási kérelemben szereplővel, és hogy a rendszer minden írási művelet után is a következő műveleteket fogja-e megtekinteni.

Ha a Spark 2,2-as verzióban az adatírás hozzáfűzési móddal történik, ezt a teljesítménnyel kapcsolatos problémát meg kell oldani.

## <a name="resolution"></a>Felbontás

Particionált adatkészlet létrehozásakor fontos a particionálási séma használata, amely korlátozza a Spark által a `FileStatusCache`frissítéséhez szükséges fájlok számát.

Ha N %100 = = 0 (100), akkor a meglévő adat áthelyezése egy másik könyvtárba, amelyet a Spark betölt.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához az Azure-Közösség és a megfelelő erőforrások összekapcsolásával: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
