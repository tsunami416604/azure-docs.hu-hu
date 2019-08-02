---
title: Apache Spark a folyamatos átviteli feladatok a szokásosnál hosszabb időt fognak feldolgozni az Azure HDInsight
description: Apache Spark a folyamatos átviteli feladatok a szokásosnál hosszabb időt fognak feldolgozni az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679431"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>Forgatókönyv: Apache Spark a folyamatos átviteli feladatok a szokásosnál hosszabb időt fognak feldolgozni az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Megfigyelheti, hogy a Apache Spark folyamatos átviteli feladatok némelyike lassú, vagy a szokásosnál hosszabb időt vesz igénybe. A Spark streaming-alkalmazások esetében az üzenetek minden kötege a Sparkba küldött egyik feladatoknak felel meg. Ha egy adott feladatoknak általában X másodperce van a feldolgozáshoz, előfordulhat, hogy a szokásosnál több 2-3 percet is igénybe vehet.

## <a name="cause"></a>Ok

Az egyik lehetséges ok az, hogy a Apache Kafka gyártó több mint 2 percet vesz igénybe a Kafka-fürtbe való írás befejezéséhez. A Kafka-probléma további hibakereséséhez hozzáadhat egy olyan kódot, amely egy Kafka-gyártó által küldött üzeneteket küld, és korrelálja a Kafka-fürt naplófájljaival.

Egy másik lehetséges ok az, hogy a WASB gyakori olvasási és írási művelete a későbbi mikro-kötegek késését okozhatja. A WASB megvalósítása `Filesystem.listStatus` nagyon lassú az ismétlődések eltávolítására `O(n!)` szolgáló algoritmus miatt. Túl sok memóriát használ a további konverzió `BlobListItem` `FileMetadata` `FileStatus`miatt. Az algoritmus például 30 percet vesz igénybe az 700 000-fájlok listázásához. Tehát ha `ListBlobs` agresszíven hívja meg a SparkSQL minden mikro-kötegben, az azt eredményezi, hogy az ezt követő mikro-kötegek lemaradnak, ami azt eredményezi, hogy magas ütemezési késéseket tapasztal. [Ez a javítás](https://issues.apache.org/jira/browse/HADOOP-15547) kijavítja a problémát, de ha a környezetből hiányzik, `ListBlobs` nagy késést tapasztal. Emellett akkor is, ha óránként törli a fájlokat, a háttérbeli listaelemnek az összes sorra (beleértve a törölt adatokat is) meg kell ismételni, mert a szemét-gyűjtési folyamat még nem fejeződött be. Előfordulhat, hogy a javítás bizonyos problémák megoldását is megoldhatja, így előfordulhat, hogy a hulladék-gyűjtési probléma továbbra is késleltetést okoz a kötegek adatfolyam-feldolgozásában.

## <a name="resolution"></a>Megoldás:

Alkalmazza a [HADOOP-15547](https://issues.apache.org/jira/browse/HADOOP-15547) javítást. Ha ez nem lehetséges, a HDFS-t használhatja a ellenőrzési pont helyeként. A `checkpointDirectory` következőhöz hasonlóan kell `hdfs://mycluster/checkpoint`beállítania:.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
