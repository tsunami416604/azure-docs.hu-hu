---
title: Hadoop-architektúra - Azure HDInsight |} Microsoft Docs
description: A HDInsight-fürtökön ismerteti a Hadoop tárolás és feldolgozás céljából.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 5ec5f1f24d3bf953115bfa5023faf81df132f510
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31397453"
---
# <a name="hadoop-architecture-in-hdinsight"></a>Hadoop-architektúra a HDInsightban

Hadoop két fő összetevőjének, a nagy sűrűségű fájlrendszerrel (HDFS), amely a tároló és a még egy másik erőforrás egyeztető (YARN) feldolgozási biztosító tartalmazza. Tárolás és feldolgozás képességekkel fürt válik a kívánt adatok feldolgozási végrehajtásához MapReduce programok futtatására képes, vagyis.

> [!NOTE]
> Egy HDFS jellemzően nincs telepítve a HDInsight-fürt tárolást belül. Ehelyett egy HDFS-kompatibilis felület réteg Hadoop-összetevők használja. A tényleges tárolási lehetőségek vagy az Azure Storage, vagy az Azure Data Lake Store biztosítja. A Hadoop a MapReduce-feladatok végrehajtása a HDInsight-fürt futtatása, mintha egy HDFS voltak jelen, és ezért az nem tárolási igényei támogatásához módosítások szükségesek. A HDInsight Hadoop, a tárolási kihelyezett van, de YARN feldolgozási alappillére marad. További információkért lásd: [Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md).

Ez a cikk a YARN, és hogyan koordinálja a hdinsight-alkalmazások végrehajtási be.

## <a name="yarn-basics"></a>YARN alapjai 

YARN szabályozza, és a Hadoop adatfeldolgozási koordinálja. YARN folyamatok a fürt csomópontjain futó két alapvető szolgáltatások rendelkezik: 

* ResourceManager 
* NodeManager

Az erőforrás-kezelő alkalmazások, például a MapReduce-feladatok fürt számítási erőforrásokat biztosít. Az erőforrás-kezelő ezeket az erőforrásokat biztosít tárolóként, ha az egyes tárolókban lefoglalása, a CPU-magokat és a RAM memória áll. Ha együttesen érhető el a fürt összes erőforrást, majd azokat egy adott magok száma és memória-blokkok elosztott valamennyi blokkja erőforrásokat egy olyan tároló. A fürt minden csomópontja rendelkezik egy bizonyos számú tárolók kapacitás, és így a fürt egy rögzített korlát a rendelkezésre álló tárolók száma. Az allokációs arányt a tárolóban lévő erőforrások lehet konfigurálni. 

Ha egy MapReduce-alkalmazás fut a fürtön, az erőforrás-kezelő biztosít az alkalmazás a végrehajtási a tárolók. Az erőforrás-kezelő futó alkalmazások, elérhető fürtözött kapacitás állapotának nyomon követi, és nyomon követi az alkalmazások azok teljes, és erőforrásaik kiadással. 

Az erőforrás-kezelő is fut, a web server folyamat. segítségével figyelheti az alkalmazások állapotát webes felhasználói felülete lehetőséget nyújt. 

Ha a felhasználó elküld egy MapReduce-alkalmazás futtatása a fürtön, a kérelmet a erőforrás-kezelő. Az erőforrás-kezelő, a rendelkezésre álló NodeManager csomópontok tárolója osztja ki. A NodeManager csomópontra, ahol az alkalmazás tényleges hajt végre. Az első lefoglalt tárolót a ApplicationMaster nevű különleges alkalmazást futtat. A ApplicationMaster felelős az beszerzése erőforrások, az elküldött alkalmazás futtatásához szükséges további tárolók formájában. A ApplicationMaster megvizsgálja a szakaszt az alkalmazás (a leképezés tesztelése, és csökkentheti a szakasz) és mennyi adatot feldolgozandó tényező. A ApplicationMaster majd kéri (*egyezteti*) az erőforrás-kezelő nevében az alkalmazás a erőforrásait. Az erőforrás-kezelő pedig erőforrások a fürt NodeManagers a számára biztosít az alkalmazás végrehajtásakor használandó ki azt a ApplicationMaster. 

Futtassa az alkalmazást, a tevékenységek a NodeManagers majd visszajelzést a folyamatát és a ApplicationMaster. A ApplicationMaster pedig az alkalmazás a erőforrás-kezelő állapotának jelentése. Az erőforrás-kezelő eredményt visszaadja az ügyfélnek.

## <a name="yarn-on-hdinsight"></a>A HDInsight YARN

Az összes HDInsight-fürttípusok YARN telepítése. Az erőforrás-kezelő magas rendelkezésre állásra, ha egy elsődleges és másodlagos példánya van telepítve, ami futtatni az első és második átjárócsomópontokkal a fürtön belül rendre. Egyszerre csak az erőforrás-kezelő egy példánya lehet aktív. A NodeManager példányokat futtatni a fürt a rendelkezésre álló munkavégző csomópontjai között.

![A HDInsight YARN](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>További lépések

* [A HDInsight Hadoop MapReduce használata](hadoop/hdinsight-use-mapreduce.md)
* [Az Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md)
