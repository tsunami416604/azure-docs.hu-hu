---
title: A fürtcsomópont elfogyott a lemezterület az Azure HDInsight
description: A fürt csomópontjának lemezterülettel kapcsolatos problémáinak Apache Hadoop elhárítása az Azure HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: ead79ca0a37a270f03a305064c80426553db59ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82628537"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Forgatókönyv: a fürtcsomópont elfogyott a lemezterület az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy feladat meghiúsulhat a következőhöz hasonló hibaüzenettel:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Vagy a következőhöz hasonló Apache Ambari-riasztás jelenhet meg: `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>Ok

Lehetséges, hogy az Apache fonal-alkalmazás gyorsítótára felhasználta az összes rendelkezésre álló lemezterületet. A Spark-alkalmazás valószínűleg nem fog hatékonyan futni.

## <a name="resolution"></a>Megoldás:

1. A Ambari felhasználói felületének használatával meghatározhatja, hogy melyik csomóponton fogy el a szabad lemezterület.

1. Határozza meg, hogy az aggasztó csomópont melyik mappája járul hozzá a lemezterület nagy részét. Először az SSH-t a csomópontra, majd futtassa a parancsot a `df` lemez használatának listázásához az összes csatlakoztatáshoz. Általában ez az `/mnt` OSS által használt Temp-lemez. Megadhat egy mappát, majd beírhatja egy `sudo du -hs` mappa összegzett fájlméretének megjelenítéséhez. Ha egy hasonló mappa jelenik meg `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` , ez azt jelenti, hogy az alkalmazás továbbra is fut. Ennek oka lehet a RDD-megőrzés vagy a közbenső véletlenszerű fájlok.

1. A probléma megoldásához öld meg az alkalmazást, amely felszabadítja az alkalmazás által használt lemezterületet.

1. Ha a probléma gyakran előfordul a munkavégző csomópontokon, beállíthatja a szál helyi gyorsítótárának beállításait a fürtön.

    Nyissa meg a Ambari felhasználói felületét, és navigáljon a > konfigurációk--> Advanced elemre.  
    Adja hozzá az alábbi két tulajdonságot az egyéni yarn-site.xml szakaszhoz, és mentse a következőt:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Ha a fentiek nem javítják ki véglegesen a problémát, optimalizálja az alkalmazást.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
