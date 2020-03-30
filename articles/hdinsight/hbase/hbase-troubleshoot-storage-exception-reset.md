---
title: Tárolási kivétel a kapcsolat alaphelyzetbe állítása után az Azure HDInsightban
description: Tárolási kivétel a kapcsolat alaphelyzetbe állítása után az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887223"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Eset: Tárolási kivétel a kapcsolat alaphelyzetbe állítása után az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet létrehozni az új Apache HBase táblát.

## <a name="cause"></a>Ok

A táblacsonkolási folyamat során tárolási kapcsolati probléma merült fel. A táblabejegyzés törölve lett a HBase metaadattáblájában. Egy kivételével az összes blobfájl törölve lett.

Bár nem volt mappa `/hbase/data/default/ThatTable` blob nevű ül a tárolóban. A WASB-illesztőprogram megtalálta a fenti blobfájl létezését, és nem `/hbase/data/default/ThatTable` engedélyezte a hívott blob létrehozását, mert feltételezte, hogy a szülőmappák léteznek, így a tábla létrehozása sikertelen lesz.

## <a name="resolution"></a>Megoldás:

1. Az Apache Ambari felhasználói felületéről indítsa újra az aktív HMaster programot. Ez lehetővé teszi, hogy a két készenléti HMaster közül az egyik legyen az aktív, és az új aktív HMaster újratölti a metaadat-tábla adatait. Így nem fogja `already-deleted` látni a táblázatot a HMaster felhasználói felületen.

1. Megtalálhatja az árva blob fájlt a felhasználói felület eszközeiből, például a Cloud Explorerből vagy a futó parancsból, mint például `hdfs dfs -ls /xxxxxx/yyyyy`. Futtassa `hdfs dfs -rmr /xxxxx/yyyy` a blob törléséhez. Például: `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Most már létrehozhat új táblát ugyanazzal a névvel a HBase-ben.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
