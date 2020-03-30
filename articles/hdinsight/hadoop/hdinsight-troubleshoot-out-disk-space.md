---
title: A fürtcsomópontból elfogy a lemezterület az Azure HDInsightban
description: Az Apache Hadoop fürtcsomópont lemezterületével kapcsolatos problémák elhárítása az Azure HDInsightban.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894131"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Eset: A fürtcsomópontból elfogy a lemezterület az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy feladat a következőhöz hasonló hibaüzenettel sikertelen lehet:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Vagy apache Ambari riasztást kaphat, hasonlóan a következőkhöz: `local-dirs usable space is below configured utilization percentage`.

## <a name="cause"></a>Ok

Előfordulhat, hogy az Apache Yarn alkalmazás-gyorsítótára minden rendelkezésre álló lemezterületet felemésztett. A Spark-alkalmazás valószínűleg nem hatékonyan fut.

## <a name="resolution"></a>Megoldás:

1. Az Ambari felhasználói felületével határozza meg, hogy melyik csomóponton elfogy a lemezterület.

1. Határozza meg, hogy a zavaró csomópont melyik mappája járul hozzá a lemezterület nagy részéhez. SSH a csomóponthoz először, majd futtassa `df` a lemezhasználat listázásához az összes csatlakoztatáshoz. Általában ez `/mnt` az, ami egy ideiglenes lemez által használt OSS. Beírhat egy mappát, `sudo du -hs` majd beírhatja az összesített fájlméretek megjelenítéséhez egy mappát. Ha a ,hoz `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`hasonló mappát lát, az azt jelenti, hogy az alkalmazás még fut. Ennek oka lehet az RDD-tartalommegőrzés vagy a köztes véletlen sorrendű adat-megőrzési fájlok.

1. A probléma enyhítése érdekében hagyja el az alkalmazást, amely felszabadítja az alkalmazás által használt lemezterületet.

1. A probléma végső megoldása érdekében optimalizálja az alkalmazást.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
