---
title: Engedély megtagadva hiba az Azure HDInsight Apache Hive táblájával
description: Engedély megtagadva, hiba történt egy Apache Hive tábla Azure HDInsight való létrehozásakor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: d9901132af992ea95a60773f404b1351386cfbcb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494203"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Forgatókönyv: engedély megtagadva, hiba történt egy Apache Hive tábla Azure HDInsight való létrehozásakor

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor megpróbál létrehozni egy táblát:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Ha a következő HDFS-tárolási parancsot futtatja, hasonló hibaüzenet jelenik meg:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Ok

A Apache Hive-táblázat létrehozásának lehetősége a fürt Storage-fiókjára alkalmazott engedélyek alapján dönt. Ha a fürt Storage-fiókjának engedélyei helytelenek, nem hozhat létre táblákat. Ez azt jelenti, hogy a megfelelő Ranger-szabályzatokkal rendelkezhet a tábla létrehozásához, és továbbra is az "engedély megtagadva" hibaüzenetek jelennek meg.

## <a name="resolution"></a>Felbontás

Ezt az okozza, hogy nincs megfelelő engedélye a használt tároló tárolójában. A kaptár táblát létrehozó felhasználónak olvasási, írási és végrehajtási engedélyre van szüksége a tárolón. További információ: [ajánlott eljárások a kaptár engedélyezéséhez az Apache Ranger használatával a HDP 2,2-ben](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához az Azure-Közösség és a megfelelő erőforrások összekapcsolásával: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletes információkat az [Azure-támogatáskérések létrehozásával](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) foglalkozó témakörben talál. Az előfizetés-kezeléssel és számlázással kapcsolatos támogatás a Microsoft Azure-előfizetés részét képezi, míg a technikai támogatást [Azure-támogatási csomagjainkkal](https://azure.microsoft.com/support/plans/) biztosítjuk.
