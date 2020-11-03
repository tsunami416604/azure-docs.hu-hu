---
title: Engedély megtagadva hiba az Azure HDInsight Apache Hive táblájával
description: Engedély megtagadva, hiba történt egy Apache Hive tábla Azure HDInsight való létrehozásakor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: b6e71f5f2c389926a2e6267fadeedebdce1c51d9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288891"
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

## <a name="resolution"></a>Feloldás

Ezt az okozza, hogy nincs megfelelő engedélye a használt tároló tárolójában. A kaptár táblát létrehozó felhasználónak olvasási, írási és végrehajtási engedélyre van szüksége a tárolón. További információ: [ajánlott eljárások a kaptár engedélyezéséhez az Apache Ranger használatával a HDP 2,2-ben](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]