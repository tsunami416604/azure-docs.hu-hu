---
title: Apache Spark a feladatok lassan futnak, amikor az Azure Storage-tároló sok fájlt tartalmaz az Azure HDInsight
description: Apache Spark a feladatok lassan futnak, amikor az Azure Storage-tároló sok fájlt tartalmaz az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 78dff1b9d9db4e54ab1a8f7203088753e206c610
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641954"
---
# <a name="scenario-apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Forgatókönyv: Apache Spark a feladatok lassan futnak, amikor az Azure Storage-tároló sok fájlt tartalmaz az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

HDInsight-fürt futtatásakor az Azure Storage-tárolóba írást Apache Spark feladat lassú lesz, ha sok fájl/almappa van. Például a rendszer 20 másodpercet vesz igénybe egy új tárolóba való íráskor, de körülbelül 2 percet, amikor több mint 200.000-fájlokat tartalmazó tárolóba ír.

## <a name="cause"></a>Ok

Ez egy ismert Spark-probléma. A lassulás a Spark-feladatok `ListBlob` végrehajtása `GetBlobProperties` során a és a műveletből származik.

A partíciók nyomon követéséhez a sparknak `FileStatusCache` egy olyan információt kell fenntartania, amely a címtár struktúrájáról tartalmaz információkat. Ezen gyorsítótár használatával a Spark elemezheti az elérési utakat, és tisztában lehet a rendelkezésre álló partíciókkal. A partíciók nyomon követésének előnye, hogy a Spark csak az adatolvasás során érinti a szükséges fájlokat. Az adatok naprakészen tartásához az új adatok írásakor a Sparknak fel kell sorolnia a könyvtár alatt lévő összes fájlt, és frissítenie kell a gyorsítótárat.

A Spark 1,6-ben minden alkalommal, amikor frissíti a könyvtárat, (1) törölje a gyorsítótárat (2) rekurzív módon listázza az összes fájlt, és (3) frissítse a teljes gyorsítótárat. Ez számos listaelem-művelethez vezet.

A Spark 2,1-es verziójában a Spark azt jelzi, hogy egy meglévő partíciós oszlop megegyezik-e a jelenlegi írási kérelemben szereplővel, és hogy a rendszer minden írási művelet után is a következő műveleteket fogja-e megtekinteni.

Ha a Spark 2,2-as verzióban az adatírás hozzáfűzési móddal történik, ezt a teljesítménnyel kapcsolatos problémát meg kell oldani.

## <a name="resolution"></a>Megoldás:

Particionált adatkészlet létrehozásakor fontos a particionálási séma használata, amely korlátozni fogja a Spark által a frissítéshez `FileStatusCache`szükséges fájlok számát.

Ha N% 100 = = 0 (100), akkor a meglévő adat áthelyezése egy másik könyvtárba, amelyet a Spark betölt.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
