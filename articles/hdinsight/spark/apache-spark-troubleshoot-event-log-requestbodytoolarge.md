---
title: NativeAzureFileSystem ... A RequestBodyTooLarge megjelenik a Apache Spark streaming alkalmazás naplójában az Azure HDInsight
description: NativeAzureFileSystem ... A RequestBodyTooLarge megjelenik a Apache Spark streaming alkalmazás naplójában az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 571e02e79714d2e713d4173936120e78e4b067de
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700495"
---
# <a name="scenario-nativeazurefilesystem--requestbodytoolarge-appears-in-log-for-apache-spark-streaming-app-in-azure-hdinsight"></a>Forgatókönyv: "NativeAzureFileSystem ... RequestBodyTooLarge "üzenet jelenik meg Apache Spark streaming-alkalmazás naplójában az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A hiba: `NativeAzureFileSystem ... RequestBodyTooLarge` egy Apache Spark streaming alkalmazás illesztőprogram-naplójában jelenik meg.

## <a name="cause"></a>Ok

A Spark-Eseménynapló fájlja valószínűleg a WASB fájl hosszának korlátját éri el.

A Spark 2,3-ben minden Spark-alkalmazás létrehoz egy Spark-Eseménynapló-fájlt. Egy Spark streaming-alkalmazás Spark-Eseménynapló-fájlja folyamatosan növekszik, amíg az alkalmazás fut. Napjainkban a WASB egy fájlja 50000 blokkos korláttal rendelkezik, és az alapértelmezett blokk mérete 4 MB. Az alapértelmezett konfigurációban tehát a maximális fájlméret 195 GB. Az Azure Storage azonban megnövelte a maximális blokkolási méretet 100 MB-ra, ami gyakorlatilag egyetlen fájlra korlátozza a 4,75 TB-ot. További információ: az [Azure Storage skálázhatósági és teljesítménybeli céljai](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

## <a name="resolution"></a>Megoldás:

Ehhez a hibához három megoldás érhető el:

* Növelje a blokk méretét akár 100 MB-ra. A Ambari felhasználói felületén módosítsa a HDFS `fs.azure.write.request.size` konfigurációs tulajdonságot (vagy `Custom core-site` hozza létre a szakaszban). Állítsa a tulajdonságot nagyobb értékre, például: 33554432. Mentse a frissített konfigurációt, és indítsa újra az érintett összetevőket.

* A Spark-streaming feladatok rendszeres leállítása és újraküldése.

* A Spark-eseménynaplók tárolására használja a HDFS. A HDFS for Storage használata a fürtök skálázása vagy az Azure-frissítések során felmerülő Spark-események elvesztését eredményezheti.

    1. Módosítsa a Ambari `spark.eventlog.dir` felhasználói `spark.history.fs.logDirectory` felületén, és a használatával:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Könyvtárak létrehozása a HDFS-on:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Indítsa újra az összes érintett szolgáltatást a Ambari felhasználói felületén keresztül.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
