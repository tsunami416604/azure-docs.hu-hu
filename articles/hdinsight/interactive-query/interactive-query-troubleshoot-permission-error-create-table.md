---
title: Az engedély megtagadva a hiba az Apache Hive táblával az Azure HDInsightban
description: Az engedély megtagadva a hibát, amikor Apache Hive-táblát próbált létrehozni az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895145"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Eset: Engedély megtagadva hiba, amikor megpróbál létrehozni egy Apache Hive-tábla az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtök interaktív lekérdezési összetevőinek használatával kapcsolatos problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor tábla létrehozását kísérelte meg:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Hasonló hibaüzenet jelenik meg, ha a következő HDFS-tárolóparancsot futtatja:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Ok

Az Apache Hive-ben egy tábla létrehozásának lehetőségét a fürt tárfiókjához alkalmazott engedélyek határozzák meg. Ha a fürttárfiók engedélyei helytelenek, nem hozhat létre táblákat. Ez azt jelenti, hogy a megfelelő Ranger-házirendek a tábla létrehozása, és továbbra is megjelenik a "Permission Denied" hibák.

## <a name="resolution"></a>Megoldás:

Ennek oka a használt tárolótárolóhoz vonatkozó megfelelő engedélyek hiánya. A Hive-táblát létrehozó felhasználónak olvasási, írási és végrehajtási engedélyeket kell végrehajtania a tárolón. További információ: [Gyakorlati tanácsok a Hive-engedélyezés Apache Ranger használatával a HDP 2.2-ben című témakörben.](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
