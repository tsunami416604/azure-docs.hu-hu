---
title: Azure HDInsight virtuális hálózati architektúra
description: Ismerje meg a rendelkezésre álló erőforrásokat, amikor egy Azure virtuális hálózatban hdinsight-fürtöt hoz létre.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ad0e0250b32f2bdef4944e6e148be3215f3822f7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390208"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight virtuális hálózati architektúra

Ez a cikk ismerteti az erőforrásokat, amelyek egy HDInsight-fürt telepítésekor egy egyéni Azure virtuális hálózatra üzembe helyezésekor vannak jelen. Ez az információ segít a helyszíni erőforrások csatlakoztatásában az Azure-beli HDInsight-fürthöz. Az Azure virtuális hálózatokról a [Mi az Azure virtuális hálózat?](../virtual-network/virtual-networks-overview.md)

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Erőforrástípusok az Azure HDInsight-fürtökben

Az Azure HDInsight-fürtök különböző típusú virtuális gépekkel vagy csomópontokkal rendelkeznek. Minden csomóponttípus szerepet játszik a rendszer működésében. Az alábbi táblázat ezeket a csomóponttípusokat és a fürtben betöltött szerepüket foglalja össze.

| Típus | Leírás |
| --- | --- |
| Átjárócsomópont |  Az Apache Storm kivételével minden fürttípus esetében a fő csomópontok üzemeltetik az elosztott alkalmazás végrehajtását kezelő folyamatokat. A fő csomópont egyben az a csomópont is, amelybe SSH-t belehet vezetni, és futtathat olyan alkalmazásokat, amelyek et afürt-erőforrások on-át futtatnak. A főcsomópontok száma az összes fürttípusesetében kettőben van rögzítve. |
| ZooKeeper csomópont | Zookeeper koordinálja a feladatokat a csomópontok között, amelyek az adatfeldolgozás. A főcsomópont vezető választását is végzi, és nyomon követi, hogy melyik főcsomópont fut egy adott főszolgáltatásban. A ZooKeeper csomópontok száma három. |
| Dolgozói csomópont | Az adatfeldolgozási funkciókat támogató csomópontokat jelöli. A számítási képességek méretezése és a költségek kezelése érdekében munkavégző csomópontok adhatók hozzá vagy távolíthatók el a fürtből. |
| R-kiszolgáló peremhálózati csomópontja | Az R-kiszolgáló peremhálózati csomópontja azt a csomópontot jelöli, amelybe SSH-t belehet vezetni, és olyan alkalmazásokat futtathat, amelyek et afürt-erőforrások on keresztül futtatnak. A peremhálózati csomópont nem vesz részt a fürtön belüli adatelemzésben. Ez a csomópont az R Studio Server kiszolgálót is üzemelteti, lehetővé téve az R alkalmazás böngészővel történő futtatását. |
| Régió csomópontja | A HBase fürttípus esetében a régiócsomópont (más néven adatcsomópont) a régiókiszolgálót futtatja. A régiókiszolgálók a HBase által kezelt adatok egy részét szolgálják és kezelik. A régiócsomópontok hozzáadhatók vagy eltávolíthatók a fürtből a számítási képességek méretezése és a költségek kezelése érdekében.|
| Nimbus csomópont | A Storm-fürt típusához a Nimbus csomópont a Head csomóponthoz hasonló funkciókat biztosít. A Nimbus csomópont a storm-topológiák futtatását koordináló Zookeeper en keresztül rendeli hozzá a feladatokat a fürt más csomópontjaihoz. |
| Felügyeleti csomópont | A Storm-fürt típusához a felügyeleti csomópont végrehajtja a Nimbus csomópont által a feldolgozáshoz megadott utasításokat. |

## <a name="resource-naming-conventions"></a>Erőforrás-elnevezési konvenciók

A fürt csomópontjainak címzésekén használja a teljesen minősített tartományneveket. Az [Ambari API használatával](hdinsight-hadoop-manage-ambari-rest-api.md)beszerezheti a fürt különböző csomóponttípusainak teljes tartományn-tartományszámait.

Ezek a teljes tartományonok lesznek a forma `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`.

A `<node-type-prefix>` lesz *hn* a headnodes, *wn* a dolgozó csomópontok és *zn* az állattartó csomópontok.

Ha csak az állomásnévre van szüksége, csak a teljes tartománynév első részét használja:`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Alapvető virtuális hálózati erőforrások

Az alábbi ábrán a HDInsight-csomópontok és a hálózati erőforrások elhelyezése látható az Azure-ban.

![Az Azure egyéni virtuális hálózatában létrehozott HDInsight-entitások diagramja](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Az Azure virtuális hálózat alapértelmezett erőforrásai közé tartoznak az előző táblázatban említett fürtcsomópont-típusok. És a hálózati eszközök, amelyek támogatják a virtuális hálózat és a külső hálózatok közötti kommunikációt.

Az alábbi táblázat összefoglalja a HDInsight egyéni Azure virtuális hálózatba való üzembe helyezésekor létrehozott kilenc fürtcsomópontot.

| Erőforrás típusa | Jelen lévő szám | Részletek |
| --- | --- | --- |
|Átjárócsomópont | kettő |    |
|Zookeeper-csomópont | Három | |
|Dolgozói csomópont | kettő | Ez a szám a fürt konfigurációjától és méretezésétől függően változhat. Az Apache Kafka legalább három munkavégző csomópontra van szükség.  |
|Átjárócsomópont | kettő | Az átjárócsomópontok olyan Azure-alapú virtuális gépek, amelyeket az Azure-ban hoznak létre, de nem láthatók az előfizetésében. Lépjen kapcsolatba az ügyfélszolgálattal, ha újra kell indítania ezeket a csomópontokat. |

A következő hálózati erőforrások automatikusan létrejönnek a HDInsight által használt virtuális hálózaton belül:

| Hálózati erőforrás | Jelen lévő szám | Részletek |
| --- | --- | --- |
|Terheléselosztó | Három | |
|Hálózati illesztők | Kilenc | Ez az érték egy normál fürtön alapul, ahol minden csomópont saját hálózati adapterrel rendelkezik. A kilenc csomópont: két főcsomópont, három zookeeper csomópont, két feldolgozó csomópont és két átjárócsomópont, amelyek az előző táblázatban szerepelnek. |
|Nyilvános IP-címek | kettő |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Végpontok a HDInsighthoz való csatlakozáshoz

A HDInsight-fürtöt háromféleképpen érheti el:

- HTTPS-végpont a virtuális hálózaton `CLUSTERNAME.azurehdinsight.net`kívül a helyen.
- SSH-végpont a fejcsomóponthoz való közvetlen `CLUSTERNAME-ssh.azurehdinsight.net`csatlakoztatáshoz a .
- HTTPS-végpont a virtuális `CLUSTERNAME-int.azurehdinsight.net`hálózaton belül. Figyelje`-int`meg a " " kifejezést ebben az URL-ben. Ez a végpont a virtuális hálózat privát IP-címére oldódik fel, és nem érhető el a nyilvános internetről.

Ez a három végpont mindegyikhez egy-egy terheléselosztó van rendelve.

Nyilvános IP-címek is biztosított a két végpont, amely lehetővé teszi a kapcsolatot a virtuális hálózaton kívülről.

1. A teljesen minősített tartománynév (FQDN) terheléselosztójához egy nyilvános IP-cím van rendelve, `CLUSTERNAME.azurehdinsight.net`amelyet a fürthöz való csatlakozáskor az internetről használhat.
1. A második nyilvános IP-cím csak az `CLUSTERNAME-ssh.azurehdinsight.net`SSH tartománynévhez használatos.

## <a name="next-steps"></a>További lépések

- [Bejövő forgalom biztonságossá tétele a HDInsight-fürtök befelé irányuló, privát végpontot rendelkező virtuális hálózatban](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
