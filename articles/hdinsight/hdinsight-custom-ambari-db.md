---
title: Egyéni Apache Ambari-adatbázis az Azure HDInsight
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtöket a saját egyéni Apache Ambari-adatbázisával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: hrasheed
ms.openlocfilehash: e92b0679111a6d5c6173da04c5061c95956125b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322950"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>HDInsight-fürtök beállítása egyéni Ambari-ADATBÁZISsal

Az Apache Ambari leegyszerűsíti egy Apache Hadoop-fürt felügyeletét és figyelését. A Ambari egy könnyen használható webes felhasználói felületet és REST API biztosít. A Ambari a HDInsight-fürtökben található, és a fürt figyelésére és a konfiguráció módosítására szolgál.

A fürt normál létrehozásakor az egyéb cikkek, például a [fürtök beállítása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md)-ben című témakörben leírtak szerint a Ambari egy HDInsight által felügyelt [S0-Azure SQL Database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) van telepítve, és nem érhető el a felhasználók számára.

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

- Az adatbázis neve nem tartalmazhat kötőjeleket és szóközöket
- Rendelkeznie kell egy meglévő Azure SQL DB-kiszolgálóval és-adatbázissal.
- A Ambari-telepítéshez megadott adatbázisnak üresnek kell lennie. Az alapértelmezett dbo-sémában nem szerepelhetnek táblák.
- Az adatbázishoz való kapcsolódáshoz használt felhasználónak SELECT, CREATE TABLE és INSERT engedélyekkel kell rendelkeznie az adatbázishoz.
- Kapcsolja be a lehetőséget, hogy [engedélyezze az Azure-szolgáltatásokhoz való hozzáférést](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) azon a kiszolgálón, amelyen a Ambari fogja tárolni.
- A HDInsight szolgáltatásból származó felügyeleti IP-címeket engedélyezni kell a tűzfalszabályben. Tekintse meg a [HDInsight-felügyeleti IP-címeket](hdinsight-management-ip-addresses.md) azon IP-címek listájáért, amelyeket hozzá kell adni a kiszolgálói szintű tűzfalszabály-szabályhoz.

Ha egy külső adatbázisban üzemelteti az Apache Ambari-adatbázist, jegyezze fel a következő szempontokat:

- Ön felelős az Azure SQL DB Ambari-t tartalmazó további költségeiért.
- Rendszeresen készítse elő az egyéni Ambari-adatbázis biztonsági mentését. Azure SQL Database automatikusan készít biztonsági másolatokat, de a biztonsági mentés megőrzési ideje változó. További információ: az [automatikus SQL Database biztonsági mentések](../azure-sql/database/automated-backups-overview.md)ismertetése.

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Fürtök üzembe helyezése egyéni Ambari-ADATBÁZISsal

Saját külső Ambari-adatbázist használó HDInsight-fürt létrehozásához használja az [Egyéni AMBARI db](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)gyors üzembe helyezési sablont.

Szerkessze a paramétereket a alkalmazásban, `azuredeploy.parameters.json` és adja meg az új fürtre vonatkozó információkat, valamint a Ambari-t tároló adatbázist.

Az üzembe helyezést az Azure CLI használatával kezdheti el. Cserélje le `<RESOURCEGROUPNAME>` azt az erőforráscsoportot, amelyben a fürtöt telepíteni kívánja.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>További lépések

- [Külső metaadattárak használata az Azure HDInsightban](hdinsight-use-external-metadata-stores.md)
