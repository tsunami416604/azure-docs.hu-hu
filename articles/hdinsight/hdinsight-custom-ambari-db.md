---
title: Egyéni Apache Ambari-adatbázis az Azure HDInsightban
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtöket saját egyéni Apache Ambari adatbázisával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240156"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>HDInsight-fürtök beállítása egyéni Ambari DB-vel

Az Apache Ambari leegyszerűsíti az Apache Hadoop-fürt felügyeletét és felügyeletét. Az Ambari egy könnyen használható webes felhasználói felületet és REST API-t biztosít. Az Ambari a HDInsight-fürtökben található, és a fürt figyelésére és a konfiguráció módosítására szolgál.

A normál fürtlétrehozása, ahogy azt más cikkekben, például [a fürtök beállítása a HDInsightban,](hdinsight-hadoop-provision-linux-clusters.md)Ambari egy [S0 Azure SQL-adatbázis,](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) amely hdinsight által felügyelt, és nem érhető el a felhasználók számára.

Az egyéni Ambari DB szolgáltatás lehetővé teszi egy új fürt telepítését és az Ambari telepítését egy külső adatbázisban, amelyet Ön kezel. A központi telepítés egy Azure Resource Manager-sablonnal történik. Ez a funkció a következő előnyökkel jár:

- Testreszabás - kiválaszthatja az adatbázis méretét és feldolgozási kapacitását. Ha nagy fürtök feldolgozása intenzív számítási feladatok, egy Ambari adatbázis alacsonyabb specifikációk válhat szűk keresztmetszetet a felügyeleti műveletek.
- Rugalmasság – szükség szerint méretezheti az adatbázist, hogy megfeleljen az igényeinek.
- Control – az adatbázis biztonsági mentéseit és biztonságát a szervezeti követelményeknek megfelelőmódon kezelheti.

A cikk további része a következő pontokat tárgyalja:

- az egyéni Ambari DB funkció használatához
- a HDInsight-fürtök kiépítéséhez szükséges lépések az Apache Ambari saját külső adatbázisának használatával

## <a name="custom-ambari-db-requirements"></a>Egyéni Ambari DB követelmények

Egyéni Ambari DB-t telepíthet az összes fürttípussal és verzióval. Több fürt nem használhatja ugyanazt az Ambari DB-t.

Az egyéni Ambari DB a következő egyéb követelményekkel rendelkezik:

- Rendelkeznie kell egy meglévő Azure SQL DB-kiszolgálóval és -adatbázissal.
- Az Ambari telepítéséhez megadott adatbázisnak üresnek kell lennie. Az alapértelmezett dbo sémában nem lehetnek táblák.
- Az adatbázishoz való csatlakozáshoz használt felhasználónak SELECT, CREATE TABLE és INSERT engedélyekkel kell rendelkeznie az adatbázishoz.
- Kapcsolja be az [Azure-szolgáltatások hoz való hozzáférés engedélyezése](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) az Azure SQL-kiszolgálón, ahol az Ambari lesz.
- A HDInsight szolgáltatás felügyeleti IP-címeit engedélyezni kell az SQL Server ben. Az SQL-kiszolgáló tűzfalához hozzáadandó IP-címek listáját a [HDInsight-kezelési IP-címek](hdinsight-management-ip-addresses.md) című témakörben található.

Amikor az Apache Ambari DB-t egy külső adatbázisban üzemelteti, ne feledje a következő pontokat:

- Ön felelős az Ambari-t tartalmazó Azure SQL DB további költségeiért.
- Rendszeresen biztonsági másolatot kell ad az egyéni Ambari DB-ről. Az Azure SQL Database automatikusan létrehozza a biztonsági mentéseket, de a biztonsági mentés megőrzési időkerete változó. További információt az [SQL Database automatikus biztonsági mentései című témakörben talál.](../sql-database/sql-database-automated-backups.md)

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Fürtök telepítése egyéni Ambari DB-vel

Ha saját külső Ambari-adatbázist használó HDInsight-fürtöt szeretne létrehozni, használja az [egyéni Ambari DB gyorsútmutató sablont.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)

Az új fürtés `azuredeploy.parameters.json` az Ambari tárolására képes adatbázis adatainak megadásához szerkesztheti a paramétereket.

Megkezdheti a központi telepítést az Azure CLI használatával. Cserélje `<RESOURCEGROUPNAME>` le azt az erőforráscsoportot, amelyben a fürtöt telepíteni szeretné.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>További lépések

- [Külső metaadattárak használata az Azure HDInsightban](hdinsight-use-external-metadata-stores.md)