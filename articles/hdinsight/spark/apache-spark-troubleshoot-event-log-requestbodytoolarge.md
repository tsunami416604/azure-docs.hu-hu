---
title: RequestBodyTooLarge hiba az Apache Spark alkalmazásból – Azure HDInsight
description: NativeAzureFileSystem ... A RequestBodyTooLarge megjelenik az Apache Spark streaming alkalmazás naplójában az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894402"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... A RequestBodyTooLarge" megjelenik az Apache Spark streaming alkalmazásnaplójában a HDInsightban

Ez a cikk az Apache Spark-összetevők Azure HDInsight-fürtökben való használata során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A hiba: `NativeAzureFileSystem ... RequestBodyTooLarge` megjelenik az Apache Spark streaming alkalmazás illesztőprogram-naplójában.

## <a name="cause"></a>Ok

A Spark eseménynapló-fájl valószínűleg eléri a WASB fájlhossz-korlátját.

A Spark 2.3-ban minden Spark-alkalmazás egy Spark-eseménynapló-fájlt hoz létre. A Spark eseménynapló-fájl egy Spark streamelési alkalmazás továbbra is növekszik, miközben az alkalmazás fut. Ma egy fájlt wasb van egy 50000 blokk limit, és az alapértelmezett blokk mérete 4 MB. Tehát az alapértelmezett konfigurációban a maximális fájlméret 195 GB. Az Azure Storage azonban 100 MB-ra növelte a maximális blokkméretet, ami gyakorlatilag 4,75 TB-ra növelte az egyfájlos korlátot. További információ: [Méretezhetőségi és teljesítménycélok a Blob storage.](../../storage/blobs/scalability-targets.md)

## <a name="resolution"></a>Megoldás:

A hibára három megoldás áll rendelkezésre:

* Növelje a blokk méretét akár 100 MB-ra. Az Ambari felhasználói felületén módosítsa `fs.azure.write.request.size` a HDFS `Custom core-site` konfigurációs tulajdonságot (vagy hozza létre szakaszban). Állítsa a tulajdonságot nagyobb értékre, például: 33554432. Mentse a frissített konfigurációt, és indítsa újra az érintett összetevőket.

* Rendszeresen állítsa le, és küldje el újra a spark-streamelési feladat.

* A Spark eseménynaplók tárolásához használja a HDFS-t. A HDFS használata a tároláshoz a Spark-eseményadatok elvesztését eredményezheti a fürtméretezés vagy az Azure-frissítések során.

    1. Az Ambari felhasználói felületmódosítása `spark.eventlog.dir` és `spark.history.fs.logDirectory` azon keresztül:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Könyvtárak létrehozása a HDFS-en:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Indítsa újra az összes érintett szolgáltatást az Ambari felhasználói felületén keresztül.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
