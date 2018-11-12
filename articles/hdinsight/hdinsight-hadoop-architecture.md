---
title: Az Apache Hadoop-architektúra – Azure HDInsight
description: Ismerteti az Apache Hadoop-tárolás és feldolgozás céljából a HDInsight-fürtökön.
services: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 066734c88890d5f1a6e42c5350db47d1a398b60a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277579"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>A HDInsight Apache Hadoop-architektúra

Az Apache Hadoop két alapvető összetevői: a Hadoop elosztott fájlrendszer (HDFS), tárhelyet biztosít, és még egy másik Resource Negotiator (YARN), amely feldolgozó biztosít. A tárolási és feldolgozási képességek a fürt lesz képes a kívánt adatfeldolgozási végrehajtásához MapReduce programok futtatása.

> [!NOTE]
> A HDFS nem általában telepítve belül a HDInsight-fürt a tárolást. Ehelyett egy HDFS-kompatibilis felületi rétegen Hadoop-összetevők használják. A tényleges tárterület funkció Azure Storage vagy az Azure Data Lake Store biztosítja. Hadoop a MapReduce-feladatok végrehajtása a HDInsight-fürtön futtatása, mintha egy HDFS jelen volt, és megkövetelik a módosításokat nem támogatja a tárolási igényeinek megfelelően. A HDInsight Hadoop, a storage kihelyezett van, de YARN feldolgozási marad az alapvető fontosságú. További információkért lásd: [Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md).

Ez a cikk bemutatja a YARN és a hogyan koordinálja a HDInsight-alkalmazásokat végrehajtását.

## <a name="apache-yarn-basics"></a>Az Apache YARN alapjai 

YARN szabályozza, és koordinálja a Hadoopban lévő adatok feldolgozása. YARN két alapvető szolgáltatásokat tartalmaz, amelyek futtathatók folyamatok, a fürtben található csomópontok: 

* ResourceManager 
* NodeManager

Az erőforrás-kezelő alkalmazások, mint a MapReduce-feladatok fürt számítási erőforrásokat biztosít. Az erőforrás-kezelő ezeket az erőforrásokat biztosít a tárolókként, ha az egyes tárolók mennyiségű processzormagot és memóriát RAM-MAL áll. Ha egy fürtben elérhető összes erőforrás együtt, és ezután elosztja a magok és a memória egységekben, minden adatblokk erőforrások egy tárolót. A fürt egyes csomópontjaihoz tárolók bizonyos számú kapacitással rendelkezik, ezért a fürt esetében a rögzített érhetők el tárolók száma. A tárolóban lévő erőforrások kiosztása nem konfigurálható. 

Ha egy MapReduce-alkalmazás egy fürtön fut, az erőforrás-kezelő biztosít az alkalmazás végrehajtási a tárolókat. Az erőforrás-kezelő nyomon követi az alkalmazások, elérhető fürtkapacitás, állapotát, és nyomon követi, mint azok teljes az alkalmazások és azok az erőforrások felszabadítása. 

Az erőforrás-kezelő is fut, a webes kiszolgálói folyamat, amely az alkalmazások állapotának figyelése webes felhasználói felületet biztosít.

Amikor egy felhasználó beküld egy MapReduce-alkalmazást a fürtön futnak, a kérelmet az erőforrás-kezelő. A ResourceManager, a tároló elérhető NodeManager csomópontokon foglal le. A NodeManager csomópontokat tartalmazza, ahol az alkalmazás tényleges végrehajtása. Az első tároló lefoglalva a ApplicationMaster nevű speciális alkalmazást futtat. Ez ApplicationMaster felelős erőforrások későbbi tárolók, a beküldött alkalmazás futtatásához szükséges formájában betöltése. A ApplicationMaster megvizsgálja az alkalmazások, például a térkép szakaszban fázisában, és csökkentheti a szakaszt, és mennyi adatot kell feldolgozni a tényezők. A ApplicationMaster majd kéri (*egyezteti*) az erőforrás-kezelő az alkalmazás nevében az erőforrásokat. Az erőforrás-kezelő viszont ad erőforrások a fürt a NodeManagers a végrehajtása sikertelen az alkalmazás használatához a hozzá tartozó ApplicationMaster. 

A feladatokat, amelyek az alkalmazás futtatása a NodeManagers majd jelentéseket azok előrehaladási és állapotmeghatározási vissza a ApplicationMaster. A ApplicationMaster viszont jelentéseket az alkalmazás a ResourceManager állapotát. Az erőforrás-kezelő és az ügyfél ad vissza eredményt.

## <a name="yarn-on-hdinsight"></a>A HDInsight YARN

Az összes HDInsight-fürttípusok üzembe, YARN. Az erőforrás-kezelő magas rendelkezésre állás érdekében egy elsődleges és másodlagos példányt, amely az első és második átjárócsomópontokkal a fürtön belül fut. van telepítve. Egyszerre csak egy példányát a ResourceManager aktív. A NodeManager példányok futtassa a fürtben rendelkezésre álló munkavégző csomópontok között.

![A HDInsight YARN](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>További lépések

* [A HDInsight a Hadoop MapReduce használata](hadoop/hdinsight-use-mapreduce.md)
* [Az Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md)
