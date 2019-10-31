---
title: Egyéni Apache Ambari-adatbázis az Azure HDInsight
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtöket a saját egyéni Apache Ambari-adatbázisával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: 8064fd5369e55ea223a697d30d7643ff5407cf76
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73066002"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>HDInsight-fürtök beállítása egyéni Ambari-ADATBÁZISsal

Az Apache Ambari leegyszerűsíti egy Apache Hadoop-fürt felügyeletét és figyelését. A Ambari egy könnyen használható webes felhasználói felületet és REST API biztosít. A Ambari a HDInsight-fürtökben található, és a fürt figyelésére és a konfiguráció módosítására szolgál.

A fürt normál létrehozásakor az egyéb cikkek, például a [fürtök beállítása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md)-ben című témakörben leírtak szerint a Ambari a HDInsight által felügyelt, [S0 Azure SQL Database-adatbázisba](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) kerül üzembe, és a felhasználók számára nem érhető el.

Az egyéni Ambari adatbázis-szolgáltatás lehetővé teszi, hogy új fürtöt helyezzen üzembe, és beállítsa a Ambari egy olyan külső adatbázisban, amelyet Ön felügyel. Az üzembe helyezés egy Azure Resource Manager sablonnal történik. Ez a funkció a következő előnyöket nyújtja:

- Testreszabás – kiválaszthatja az adatbázis méretét és feldolgozási kapacitását. Ha nagy méretű fürtökkel dolgoz fel intenzív számítási feladatokat, az alacsonyabb specifikációkkal rendelkező Ambari-adatbázisok szűk keresztmetszetet jelenthetnek a felügyeleti műveletek esetében.
- Rugalmasság – szükség szerint méretezheti az adatbázist az igényeinek megfelelően.
- Vezérlés – az adatbázis biztonsági mentéseit és biztonságát úgy kezelheti, hogy azok megfeleljenek a szervezet követelményeinek.

A cikk hátralévő része a következő szempontokat ismerteti:

- az egyéni Ambari adatbázis-szolgáltatás használatára vonatkozó követelmények
- a HDInsight-fürtök saját külső adatbázissal való kiépítéséhez szükséges lépések az Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Egyéni Ambari-ADATBÁZISra vonatkozó követelmények

Az egyéni Ambari-ADATBÁZISokat a fürt összes típusával és verziójával is üzembe helyezheti. Több fürt nem használhatja ugyanazt a Ambari-adatbázist.

Az egyéni Ambari-adatbázis a következő egyéb követelményekkel rendelkezik:

- Rendelkeznie kell egy meglévő Azure SQL DB-kiszolgálóval és-adatbázissal.
- A Ambari-telepítéshez megadott adatbázisnak üresnek kell lennie. Az alapértelmezett dbo-sémában nem szerepelhetnek táblák.
- Az adatbázishoz való kapcsolódáshoz használt felhasználónak SELECT, CREATE TABLE és INSERT engedélyekkel kell rendelkeznie az adatbázishoz.
- Kapcsolja be a lehetőséget, hogy engedélyezze az Azure- [szolgáltatásokhoz való hozzáférést](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) azon az Azure SQL Serveren, ahol a Ambari fogja üzemeltetni.
- A HDInsight szolgáltatásból származó felügyeleti IP-címeket engedélyezni kell a SQL Serverban. Tekintse meg az SQL Server-tűzfalhoz hozzáadni kívánt IP-címek listáját a [HDInsight-felügyeleti IP-címek](hdinsight-management-ip-addresses.md) listájában.

Ha egy külső adatbázisban üzemelteti az Apache Ambari-adatbázist, jegyezze fel a következő szempontokat:

- Ön felelős az Azure SQL DB Ambari-t tartalmazó további költségeiért.
- Rendszeresen készítse elő az egyéni Ambari-adatbázis biztonsági mentését. Azure SQL Database automatikusan készít biztonsági másolatokat, de a biztonsági mentés megőrzési ideje változó. További információ: az [automatikus SQL Database biztonsági mentések](../sql-database/sql-database-automated-backups.md)ismertetése.

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Fürtök üzembe helyezése egyéni Ambari-ADATBÁZISsal

Saját külső Ambari-adatbázist használó HDInsight-fürt létrehozásához használja az [Egyéni AMBARI db](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)gyors üzembe helyezési sablont.

Szerkessze a `azuredeploy.parameters.json` paramétereit, és adja meg az új fürtre és a Ambari tároló adatbázisra vonatkozó információkat.

Az üzembe helyezést az Azure CLI használatával kezdheti el. Cserélje le a `<RESOURCEGROUPNAME>`t arra az erőforráscsoporthoz, amelyben a fürtöt telepíteni kívánja.

```azure-cli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Következő lépések

- [Külső metaadattárak használata az Azure HDInsightban](hdinsight-use-external-metadata-stores.md)