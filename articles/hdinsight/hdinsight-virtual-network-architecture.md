---
title: Azure HDInsight virtuális hálózati architektúra
description: Ismerje meg a rendelkezésre álló erőforrásokat, amikor egy Azure virtuális hálózatban hdinsight-fürtöt hoz létre.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b3f622b360f565ef5b16d5376cb1aa2498655017
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272148"
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
| Felügyeleti csomópont | A Storm-fürt típusához a felügyeleti csomópont végrehajtja a Nimbus csomópont által a kívánt feldolgozás végrehajtására adott utasításokat. |

## <a name="resource-naming-conventions"></a>Erőforrás-elnevezési konvenciók

A fürt csomópontjainak címzésekén használja a teljesen minősített tartományneveket.Please use Fully Qualified Domain Names (FQDNs) when addressing nodes in your cluster. Az [Ambari API használatával](hdinsight-hadoop-manage-ambari-rest-api.md)beszerezheti a fürt különböző csomóponttípusainak teljes tartományn-tartományszámait. 

Ezek a teljes tartományonok lesznek a forma `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`.

A `<node-type-prefix>` lesz *hn* a headnodes, *wn* a dolgozó csomópontok és *zn* az állattartó csomópontok.

Ha csak az állomásnévre van szüksége, csak a teljes tartománynév első részét használja:`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Alapvető virtuális hálózati erőforrások

Az alábbi ábrán a HDInsight-csomópontok és a hálózati erőforrások elhelyezése látható az Azure-ban.

![Az Azure egyéni virtuális hálózatában létrehozott HDInsight-entitások diagramja](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Az Azure virtuális hálózatba üzembe helyezett HDInsight alapértelmezett erőforrások közé tartoznak az előző táblázatban említett fürtcsomópont-típusok, valamint a virtuális hálózat és a külső hálózatok közötti kommunikációt támogató hálózati eszközök.

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
|Hálózati illesztők | Kilenc | Ez az érték egy normál fürtön alapul, ahol minden csomópont saját hálózati adapterrel rendelkezik. A kilenc csomópont a két főcsomóponthoz, három zookeeper csomóponthoz, két feldolgozó csomóponthoz és az előző táblázatban említett két átjárócsomóponthoz. |
|Nyilvános IP-címek | kettő |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Végpontok a HDInsighthoz való csatlakozáshoz

A HDInsight-fürtöt háromféleképpen érheti el:

- HTTPS-végpont a virtuális hálózaton `CLUSTERNAME.azurehdinsight.net`kívül a helyen.
- SSH-végpont a fejcsomóponthoz való közvetlen `CLUSTERNAME-ssh.azurehdinsight.net`csatlakoztatáshoz a .
- HTTPS-végpont a virtuális `CLUSTERNAME-int.azurehdinsight.net`hálózaton belül. Figyelje meg a "-int" ebben az URL-ben. Ez a végpont a virtuális hálózat privát IP-címére oldódik fel, és nem érhető el a nyilvános internetről.

Ez a három végpont mindegyikhez egy-egy terheléselosztó van rendelve.

Nyilvános IP-címek is biztosított a két végpont, amely lehetővé teszi a kapcsolatot a virtuális hálózaton kívülről.

1. A teljesen minősített tartománynév (FQDN) terheléselosztójához egy nyilvános IP-cím van rendelve, `CLUSTERNAME.azurehdinsight.net`amelyet a fürthöz való csatlakozáskor az internetről használhat.
1. A második nyilvános IP-cím csak az `CLUSTERNAME-ssh.azurehdinsight.net`SSH tartománynévhez használatos.

## <a name="next-steps"></a>További lépések

- [Bejövő forgalom biztonságossá tétele a HDInsight-fürtök befelé irányuló, privát végpontot rendelkező virtuális hálózatban](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
