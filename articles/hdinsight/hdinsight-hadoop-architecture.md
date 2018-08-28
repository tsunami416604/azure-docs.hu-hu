---
title: Hadoop-architektúra – Azure HDInsight
description: Ismerteti a Hadoop-tárolás és feldolgozás céljából a HDInsight-fürtökön.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 039a16e7c33a1b3c09c91103c372553e282b6028
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43108265"
---
# <a name="hadoop-architecture-in-hdinsight"></a>Hadoop-architektúra a HDInsightban

Hadoop két fő összetevőjének, a nagy sűrűségű fájlrendszer (HDFS) által biztosított tárolási és még egy másik Resource Negotiator (YARN) által biztosított feldolgozási tartalmaz. Az adattárolási és feldolgozási képességek fürt lesz képes a kívánt adatfeldolgozási végrehajtásához MapReduce programok futtatása.

> [!NOTE]
> A HDFS nem általában telepítve belül a HDInsight-fürt a tárolást. Ehelyett egy HDFS-kompatibilis felületi rétegen Hadoop-összetevők használják. A tényleges tárterület funkció Azure Storage vagy az Azure Data Lake Store biztosítja. Hadoop a MapReduce-feladatok végrehajtása a HDInsight-fürtön futtatása, mintha egy HDFS jelen volt, és megkövetelik a módosításokat nem támogatja a tárolási igényeinek megfelelően. A HDInsight Hadoop, a storage kihelyezett van, de YARN feldolgozási marad az alapvető fontosságú. További információkért lásd: [Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md).

Ez a cikk bemutatja a YARN és a hogyan koordinálja a HDInsight-alkalmazásokat végrehajtását.

## <a name="yarn-basics"></a>YARN alapjai 

YARN szabályozza, és koordinálja a Hadoopban lévő adatok feldolgozása. YARN két alapvető szolgáltatásokat tartalmaz, amelyek futtathatók folyamatok, a fürtben található csomópontok: 

* ResourceManager 
* NodeManager

Az erőforrás-kezelő alkalmazások, mint a MapReduce-feladatok fürt számítási erőforrásokat biztosít. Az erőforrás-kezelő ezeket az erőforrásokat biztosít a tárolókként, ha az egyes tárolók mennyiségű processzormagot és memóriát RAM-MAL áll. Ha egy fürtben elérhető összes erőforrás együtt, és ezután elosztja azokat az adatblokkokat adott számú maggal és a memória, minden adatblokk erőforrások egy tárolót. A fürt egyes csomópontjaihoz kapacitása bizonyos számú tárolókat, és ezért a fürt esetében a rögzített érhetők el tárolók száma. A tárolóban lévő erőforrások kiosztása nem konfigurálható. 

Ha egy MapReduce-alkalmazás egy fürtön fut, az erőforrás-kezelő biztosít az alkalmazás végrehajtási a tárolókat. Az erőforrás-kezelő nyomon követi az alkalmazások, elérhető fürtkapacitás, állapotát, és nyomon követi, mint azok teljes az alkalmazások és azok az erőforrások felszabadítása. 

Az erőforrás-kezelő is fut, a webes kiszolgálói folyamat, amely segítségével figyelheti az alkalmazások állapotát webes felhasználói felületet biztosít. 

Amikor egy felhasználó beküld egy MapReduce-alkalmazást a fürtön futnak, a kérelmet az erőforrás-kezelő. A ResourceManager, a tároló elérhető NodeManager csomópontokon foglal le. A NodeManager csomópontokat tartalmazza, ahol az alkalmazás tényleges végrehajtása. Az első tároló lefoglalva a ApplicationMaster nevű speciális alkalmazást futtat. Ez ApplicationMaster felelős erőforrások későbbi tárolók, a beküldött alkalmazás futtatásához szükséges formájában betöltése. A ApplicationMaster megvizsgálja az alkalmazás fázisában (a térkép előkészítése, és csökkentheti. fázis) figyelembe véve az, hogy mennyi adatot kell feldolgozni. A ApplicationMaster majd kéri (*egyezteti*) az erőforrás-kezelő az alkalmazás nevében az erőforrásokat. Az erőforrás-kezelő viszont ad erőforrások a fürt a NodeManagers a végrehajtása sikertelen az alkalmazás használatához a hozzá tartozó ApplicationMaster. 

A feladatokat, amelyek az alkalmazás futtatása a NodeManagers majd jelentéseket azok előrehaladási és állapotmeghatározási vissza a ApplicationMaster. A ApplicationMaster viszont jelentéseket az alkalmazás a ResourceManager állapotát. Az erőforrás-kezelő és az ügyfél ad vissza eredményt.

## <a name="yarn-on-hdinsight"></a>A HDInsight YARN

Az összes HDInsight-fürttípusok üzembe, YARN. Az erőforrás-kezelő elsődleges és másodlagos példánnyal, magas rendelkezésre állású van telepítve, ami futtatni az első és második átjárócsomópontokkal a fürtön belül jelölik. Egyszerre csak egy példányát a ResourceManager aktív. A NodeManager példányok futtassa a fürtben rendelkezésre álló munkavégző csomópontok között.

![A HDInsight YARN](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>További lépések

* [A HDInsight a Hadoop MapReduce használata](hadoop/hdinsight-use-mapreduce.md)
* [Az Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md)
