---
title: A fürtcsomópont elfogyott a lemezterület az Azure HDInsight
description: A fürt csomópontjának lemezterülettel kapcsolatos problémáinak Apache Hadoop elhárítása az Azure HDInsight-ben.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894131"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Forgatókönyv: a fürtcsomópont elfogyott a lemezterület az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy feladat meghiúsulhat a következőhöz hasonló hibaüzenettel: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Vagy a következőhöz hasonló Apache Ambari-riasztás jelenhet meg: `local-dirs usable space is below configured utilization percentage`.

## <a name="cause"></a>Ok

Lehetséges, hogy az Apache fonal-alkalmazás gyorsítótára felhasználta az összes rendelkezésre álló lemezterületet. A Spark-alkalmazás valószínűleg nem fog hatékonyan futni.

## <a name="resolution"></a>Felbontás

1. A Ambari felhasználói felületének használatával meghatározhatja, hogy melyik csomóponton fogy el a szabad lemezterület.

1. Határozza meg, hogy az aggasztó csomópont melyik mappája járul hozzá a lemezterület nagy részét. Először az SSH-t a csomópontra, majd futtassa `df` az összes csatlakoztatási lemez használatának listázásához. Általában `/mnt`, amely az OSS által használt Temp-lemez. Megadhat egy mappát, majd beírhatja `sudo du -hs` a mappa összegzett fájlméretének megjelenítéséhez. Ha a `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`hoz hasonló mappa jelenik meg, ez azt jelenti, hogy az alkalmazás továbbra is fut. Ennek oka lehet a RDD-megőrzés vagy a közbenső véletlenszerű fájlok.

1. A probléma megoldásához öld meg az alkalmazást, amely felszabadítja az alkalmazás által használt lemezterületet.

1. A probléma végső megoldásához optimalizálja az alkalmazást.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához az Azure-Közösség és a megfelelő erőforrások összekapcsolásával: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
